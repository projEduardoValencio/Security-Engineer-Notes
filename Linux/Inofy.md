# 🧩 Inotify and Its Security Pitfalls on Linux

> **Author:** Eduardo V.  
> **Category:** Linux Internals / Security Research  
> **Tags:** `inotify`, `linux`, `security`, `kernel`, `filesystem`, `DoS`

---

## 🧠 1. What Is Inotify?

**Inotify** is a Linux kernel subsystem that lets applications **watch files or directories** for changes — like creation, deletion, modification, or permission updates.

It’s used by a ton of tools you probably have running right now:

- 🧰 Sync apps like Dropbox or Syncthing  
- 💻 Dev servers like Webpack, Nodemon, or .NET Hot Reload  
- 🕵️ Security monitoring tools and log watchers  
- ⚙️ Daemons that auto-reload configs (systemd, Nginx, etc.)

Introduced in **kernel 2.6.13**, it replaced the older `dnotify` system with a more efficient and flexible model.

---

## ⚙️ 2. How It Works (In Short)

An app talks to inotify using these syscalls:

```c
int inotify_init(void);
int inotify_add_watch(int fd, const char *pathname, uint32_t mask);
int inotify_rm_watch(int fd, int wd);
```

Basically:
1. `inotify_init()` → create a watcher instance  
2. `inotify_add_watch()` → tell it *what* to watch  
3. `read()` → receive events as they happen

📘 *Example:*

```c
#include <sys/inotify.h>
#include <stdio.h>

int main() {
    int fd = inotify_init();
    int wd = inotify_add_watch(fd, "/etc/passwd", IN_MODIFY);
    struct inotify_event event;
    read(fd, &event, sizeof(event));
    printf("File changed: %s\n", event.name);
}
```

---

## 📉 3. Limits and Common Errors

Each inotify **instance** and **watch** eats kernel memory, so Linux sets limits to avoid meltdown.

| Parameter | Description | Default |
|------------|--------------|----------|
| `/proc/sys/fs/inotify/max_user_instances` | Max inotify instances per user | 128 |
| `/proc/sys/fs/inotify/max_user_watches` | Max watched paths per user | 8192 |
| `/proc/sys/fs/inotify/max_queued_events` | Max queued events | 16384 |

If you go over the limit, you’ll see something like this:

```
System.IO.IOException: The configured user limit (128) on the number of inotify instances has been reached
```

💡 **Tip:** You can tweak these with `sysctl`.

---

## 💥 4. Security Risks and Exploits

### 🚫 4.1. Denial of Service (DoS)

Attackers can create **too many watchers** to drain kernel memory.  
This can freeze legitimate apps or cause system crashes.

Example:

```bash
#!/bin/bash
# inotify DoS demo — do NOT run in prod
while true; do
  inotifywait -m /tmp &
done
```

**Mitigation:**
- Lower the inotify limits  
- Add error handling for `EMFILE` / `ENOSPC`  
- Monitor kernel memory usage with `sysctl fs.inotify.*`

---

### 🕵️ 4.2. Information Leaks

A low-privileged user could spy on **file system activity**.

They can:
- Watch `/etc` or `/home/user/.ssh`  
- Detect config changes or SSH key creation  
- Infer system behavior and timing

**Mitigation:**
- Restrict permissions  
- Avoid running untrusted apps with watcher access  
- Mount sensitive dirs with `nosuid`, `nodev`, `noexec`

---

### ⚡ 4.3. Race Conditions & Privilege Escalation

Some daemons or scripts automatically reload configs when files change — and that’s a juicy target.

**Scenario:**
1. Root daemon watches `/etc/config.yaml`  
2. User replaces file with a symlink  
3. Daemon reloads → reads attacker-controlled content 😈

**Mitigation:**
- Write to temp → rename atomically  
- Use `O_NOFOLLOW` flag  
- Always check file owner and permissions before reloading

---

## 🧩 5. How to Stay Safe

### 🔧 Tune System Limits

```bash
sudo sysctl fs.inotify.max_user_instances=64
sudo sysctl fs.inotify.max_user_watches=4096
sudo sysctl fs.inotify.max_queued_events=8192
```

Make persistent:

```bash
echo "fs.inotify.max_user_instances=64" | sudo tee -a /etc/sysctl.conf
```

---

### 🧱 Validate Paths

```c
if (lstat(path, &st) == -1 || !S_ISREG(st.st_mode)) {
    fprintf(stderr, "Invalid or unsafe path: %s\n", path);
    exit(1);
}
```

---

## 🧨 6. Real-World CVEs

| CVE | Description | Impact |
|------|--------------|--------|
| **CVE-2019-19814** | DoS via inotify exhaustion in `systemd` | Local DoS |
| **CVE-2013-0246** | Race condition in `udev` (inotify-based) | Privilege escalation |
| **CVE-2017-18249** | Memory leak in `inotify_add_watch()` | Kernel DoS |
| **CVE-2022-43750** | Use-after-free in `overlayfs` triggered by inotify | Kernel panic |

---

## 🧭 7. TL;DR Summary

| Aspect | Description | Risk |
|--------|--------------|------|
| 💣 DoS | Too many watchers → memory exhaustion | 🔴 High |
| 👀 Info Leak | Monitoring sensitive paths | 🟠 Medium |
| ⚙️ Race Condition | Exploiting reload mechanisms | 🟠 Medium |
| 🧰 Mitigation | Limit instances, sanitize paths, validate ownership | 🟢 Recommended |

---

## 🔗 8. References

- [man7.org: inotify(7)](https://man7.org/linux/man-pages/man7/inotify.7.html)  
- [Red Hat CVE-2019-19814](https://access.redhat.com/security/cve/CVE-2019-19814)  
- [Linux Kernel Source: fs/notify/inotify](https://elixir.bootlin.com/linux/latest/source/fs/notify/inotify)  
- [OWASP Secure Coding Practices](https://owasp.org/www-project-secure-coding-practices-quick-reference-guide/)

---

## 💬 9. Personal Note

This study idea came up after I hit the dreaded error:

```
System.IO.IOException: The configured user limit (128) on the number of inotify instances has been reached
```

That happened on **Fedora 42** when I tried to run a `.NET` project **while VSCode and nvim were both open** —  
basically, too many file watchers fighting for attention 😅.  

What started as a debugging session turned into a full exploration of how `inotify` can impact both **system stability** and **security**.


# FTP — Vulnerabilities & Mitigations 🧩🔐

## 1 — Quick summary 🚀
FTP (File Transfer Protocol) transmits control messages and credentials in **plaintext** by default (typically on TCP port **21**).  
From HackTheBox labs and practical recon: FTP is susceptible to **Man-in-the-Middle (MITM)** attacks, credential disclosure, sensitive data exposure via logs, and frequent misconfigurations such as enabled `anonymous` login.

---

## 2 — Key concepts 🧠
- **FTP (port 21)** — legacy file transfer protocol. Commands and credentials are sent unencrypted.  
- **FTPS** — FTP over SSL/TLS. Adds confidentiality and integrity using X.509 certificates (explicit `AUTH TLS` or implicit on port **990**).  
- **SFTP** — SSH File Transfer Protocol. Runs over SSH (typically port **22**). Not “FTP over SSH”; a different protocol that inherits SSH security.  
- **Misconfiguration** — common issues: `anonymous` enabled, overly permissive directory permissions, improper chroot.  
- **Logs** — FTP server logs (e.g., `RETR`, `STOR` entries) can reveal filenames, paths and other metadata useful during enumeration or data discovery.

---

## 3 — Signatures & server responses to watch for 🔎
- Successful login response:
```
230 Login OK
```
- Typical port/service line:
```
21/tcp open  ftp
```
- `nmap -sV` often reveals the FTP banner/version — crucial for correlating with known CVEs.

---

## 4 — Reconnaissance commands (authorized testing only) 🛠️
> Run these only in labs or against systems you are authorized to test.

- Nmap service/version scan:
```bash
nmap -sV -p 21 <target>
```

- Nmap with FTP scripts (enumeration, anonymous test, brute, known backdoors):
```bash
nmap -sV -p 21 --script=ftp* <target>
```
Common scripts: `ftp-anon`, `ftp-brute`, `ftp-syst`, `ftp-vsftpd-backdoor`, `ftp-proftpd-backdoor`.

- Manual anonymous login test:
```bash
ftp <target>
# when prompted: user -> anonymous, password -> any (e.g., user@domain)
```

- Banner grab with netcat/telnet:
```bash
nc <target> 21
# or
telnet <target> 21
```

---

## 5 — Practical risks & impact ⚠️
- **Credential interception (MITM):** plaintext transport allows sniffers (tcpdump/wireshark) to capture usernames, passwords and file contents.  
- **Anonymous abuse:** if `anonymous` is enabled and write is allowed, attackers can upload files, drop tools/shells (in integrated environments) or exfiltrate data.  
- **Logs as intel:** logs often include filenames (e.g., `db_dump.sql`, `backup.tar.gz`) and paths that point to sensitive artifacts.  
- **Misconfigurations:** world-writable directories, missing chroot, or outdated software may lead to escalation or code execution depending on context.

---

## 6 — Mitigations & best practices 🔧
- Prefer **SFTP (SSH)** or **FTPS (TLS)** over plain FTP; disable plain FTP where feasible.  
- Disable **anonymous** login unless absolutely required. If required, confine it to a strictly chrooted directory with least privilege.  
- Require TLS for FTPS (explicit `AUTH TLS`) and use valid certificates. Consider using implicit FTPS on port **990** only if your environment requires it.  
- Restrict FTP access with firewall rules and require VPN for administrative access.  
- Enforce least-privilege on upload directories; avoid allowing uploads into web-served or executable paths.  
- Rotate and minimize log retention; do **not** log secrets/tokens. Monitor for unusual `STOR`/`RETR` activity.  
- Keep FTP server software updated (vsftpd, proftpd, pure-ftpd, etc.) and monitor relevant CVEs.  
- Use IDS/IPS to detect unexpected FTP traffic where FTP is not expected.

---

## 7 — Detecting misconfiguration quickly ✅
- `nmap -sV -p21 --script=ftp-anon,ftp-syst <target>` to check anonymous login and server info.  
- Attempt `anonymous` login with an FTP client to validate read/write permissions.  
- Use `LIST`, `NLST`, `RETR` and (if allowed) `STOR` to confirm directory contents and upload capability — only in authorized engagements.  
- Correlate banner/version with known CVEs and exploit advisories.

---

## 8 — Illustrative log excerpt (example) — DO NOT USE REAL DATA 🔐
```
Oct  9 14:23:11 ftpserver vsftpd[1234]: [pid 1234] CONNECT: Client "10.0.0.42"
Oct  9 14:23:12 ftpserver vsftpd[1234]: [pid 1234] FTP response: 220 (vsFTPd 3.0.3)
Oct  9 14:23:13 ftpserver vsftpd[1234]: [pid 1234] USER anonymous
Oct  9 14:23:13 ftpserver vsftpd[1234]: [pid 1234] 230 Login OK.
Oct  9 14:24:01 ftpserver vsftpd[1234]: [pid 1234] RETR /backups/db_dump_2025-09-30.sql
```
- Note: `RETR` entries pointing to backups or DB dumps are high-value intel for attackers.

---

## 9 — Pentest / audit checklist 🧾
- [ ] Run `nmap -sV -p 21 --script=ftp*` against the target.  
- [ ] Test `anonymous` with `ftp-anon` and manual client.  
- [ ] Check for upload (`STOR`) capability and directory write permissions.  
- [ ] Correlate banner/version with CVEs.  
- [ ] Test FTPS (explicit `AUTH TLS`) and SFTP availability.  
- [ ] Inspect FTP logs for `RETR`/`STOR` entries that reference sensitive filenames.  
- [ ] Verify network segmentation and firewall rules for FTP services.  
- [ ] Recommend migration plan to SFTP/FTPS and apply hardening steps.

---

## 10 — Example nmap command & sample output
```bash
nmap -sV -p 21 --script=ftp-anon,ftp-syst <target>
```

Sample (illustrative) output:
```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (ftp://<target>/)
|_  - writable: /pub/uploads
| ftp-syst: SYST: UNIX Type: L8
```

---

## 11 — Suggested file path / commit message 🗂️
File path:
```
Network/FTP/01-FTP-vulnerabilities.md
```
Suggested commit message:
```
feat(network/ftp): add English notes on FTP vulnerabilities, MITM, anonymous login and mitigations
```

---

## 12 — References & next steps 📚
- I can add a **References** section with curated links (official docs for `vsftpd` / `proftpd` / `pure-ftpd`, FTPS vs SFTP comparisons, Nmap FTP script docs, HTB lab notes and CVEs). Want me to fetch and append those links?  
- Next actions you can do in your lab:
  - Run `nmap -sV -p21 --script=ftp* <target>` and paste the output here — I’ll help interpret and map to CVEs.  
  - If you want, I can generate a `vsftpd.conf` hardening snippet or an example Ansible task to enforce safe FTP configuration.

---
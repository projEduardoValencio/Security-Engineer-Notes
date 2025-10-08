# 🌐 HTTP Headers and Browser Behavior Reference

This document summarizes the main HTTP headers and related mechanisms that define how browsers, proxies, and APIs interpret, cache, secure, or optimize web traffic.  
Useful for Security Engineers, Web Developers, and AppSec professionals to understand how the web really works under the hood.

---

## 🧱 1. Cache and Conditional Requests

| Functionality                         | Header(s)                                           | Browser Interpretation                                           | Common Usage                      |
| ------------------------------------- | --------------------------------------------------- | ---------------------------------------------------------------- | --------------------------------- |
| **ETag / If-None-Match**              | `ETag`, `If-None-Match`                             | Revalidates cached resources; if unchanged → `304 Not Modified`. | ✅ Common for APIs & static files |
| **Last-Modified / If-Modified-Since** | `Last-Modified`, `If-Modified-Since`                | Revalidation based on date; simpler alternative to ETag.         | ✅ Widely used                    |
| **Cache-Control**                     | `Cache-Control: max-age, no-cache, public, private` | Defines how long a response can be cached.                       | ✅ Essential                      |
| **Expires**                           | `Expires: <date>`                                   | Legacy version of Cache-Control.                                 | ⚠️ Still supported                |
| **Vary**                              | `Vary: Accept-Encoding, Accept-Language`            | Controls cache variations by header.                             | ⚙️ Used in CDNs/proxies           |

---

## 🔒 2. Security and Content Protection

| Functionality                            | Header(s)                                                    | Browser Interpretation                                   | Common Usage            |
| ---------------------------------------- | ------------------------------------------------------------ | -------------------------------------------------------- | ----------------------- |
| **CORS (Cross-Origin Resource Sharing)** | `Access-Control-Allow-Origin`, etc.                          | Controls which origins can access the resource.          | ✅ Fundamental for APIs |
| **Content-Security-Policy (CSP)**        | `Content-Security-Policy`                                    | Restricts script/image/font sources to prevent XSS.      | 🔒 Strong best practice |
| **Strict-Transport-Security (HSTS)**     | `Strict-Transport-Security`                                  | Forces HTTPS for future requests.                        | ✅ Standard             |
| **X-Frame-Options**                      | `DENY`, `SAMEORIGIN`                                         | Blocks clickjacking via iframes.                         | ✅ Common               |
| **X-Content-Type-Options**               | `nosniff`                                                    | Prevents MIME-type guessing.                             | ✅ Always enabled       |
| **Referrer-Policy**                      | `Referrer-Policy`                                            | Controls how much referrer info is sent.                 | ⚙️ Often used           |
| **Permissions-Policy**                   | `camera=(), geolocation=()`                                  | Limits access to browser APIs.                           | ⚙️ Modern use           |
| **COOP / COEP**                          | `Cross-Origin-Opener-Policy`, `Cross-Origin-Embedder-Policy` | Enables cross-origin isolation (e.g. SharedArrayBuffer). | ⚙️ Advanced isolation   |

---

## 🧠 3. Content Negotiation

| Functionality             | Header(s)                | Browser Interpretation                       | Common Usage          |
| ------------------------- | ------------------------ | -------------------------------------------- | --------------------- |
| **Accept / Content-Type** | `Accept`, `Content-Type` | Defines content format (JSON, HTML, XML).    | ✅ Always             |
| **Accept-Encoding**       | `gzip`, `br`, `deflate`  | Compression formats accepted by the browser. | ✅ Default            |
| **Accept-Language**       | `pt-BR`, `en-US`         | Indicates user’s language preference.        | ⚙️ Multilingual sites |
| **Accept-Charset**        | `utf-8`                  | Legacy charset negotiation.                  | ⚠️ Rare today         |

---

## 🔐 4. Authentication and Sessions

| Functionality                        | Header(s)                  | Browser Interpretation                | Common Usage       |
| ------------------------------------ | -------------------------- | ------------------------------------- | ------------------ |
| **WWW-Authenticate / Authorization** | `Basic`, `Bearer`          | HTTP-level authentication.            | ⚙️ Used in APIs    |
| **Cookie / Set-Cookie**              | `Set-Cookie`               | Stores cookies with security flags.   | ✅ Web apps        |
| **SameSite Cookies**                 | `SameSite=Lax/Strict/None` | Prevents CSRF in cross-site contexts. | ✅ Modern security |

---

## 🚀 5. Performance and Compression

| Functionality              | Header(s)              | Browser Interpretation                       | Common Usage        |
| -------------------------- | ---------------------- | -------------------------------------------- | ------------------- |
| **Content-Encoding**       | `gzip`, `br`           | Browser decompresses automatically.          | ✅ CDN default      |
| **Transfer-Encoding**      | `chunked`              | Streams response in parts.                   | ⚙️ Automatic        |
| **Connection: keep-alive** | `keep-alive`           | Reuses TCP connection for multiple requests. | ⚙️ Default behavior |
| **ETag Weak vs Strong**    | `W/"etag"` vs `"etag"` | Weak ignores minor changes.                  | ⚙️ API tuning       |

---

## 📦 6. File Handling and Downloads

| Functionality             | Header(s)                         | Browser Interpretation                      | Common Usage   |
| ------------------------- | --------------------------------- | ------------------------------------------- | -------------- |
| **Content-Disposition**   | `attachment; filename="file.pdf"` | Forces download instead of inline view.     | ✅ Common      |
| **Content-Type**          | `application/json`, `text/html`   | Defines the MIME type.                      | ✅ Fundamental |
| **Content-Length**        | bytes                             | Indicates size of response body.            | ⚙️ Automatic   |
| **Range / Accept-Ranges** | `Range: bytes=0-1000`             | Enables partial requests (video streaming). | ⚙️ Supported   |

---

## 🧩 7. Connection and Transport

| Functionality                      | Header(s)            | Browser Interpretation                     | Common Usage          |
| ---------------------------------- | -------------------- | ------------------------------------------ | --------------------- |
| **Connection: keep-alive / close** |                      | Persistent vs. one-off TCP connections.    | ⚙️ Managed by browser |
| **Upgrade**                        | `Upgrade: websocket` | Switches to another protocol (e.g., WS).   | ✅ WebSocket          |
| **Origin / Host**                  | Sent by browser      | Identifies request origin and target host. | ✅ Always             |

---

## 🔁 8. Redirects and Navigation

| Functionality     | Code / Header              | Browser Interpretation         | Common Usage     |
| ----------------- | -------------------------- | ------------------------------ | ---------------- |
| **3xx Redirects** | `301`, `302`, `307`, `308` | Browser follows automatically. | ✅ Core behavior |
| **Location**      | `Location: /new-path`      | Specifies redirect target.     | ✅ Always        |
| **Refresh**       | `Refresh: 5;url=...`       | Timed redirect (legacy).       | ⚙️ Niche         |

---

## 🛰️ 9. Proxy and CDN Integration

| Functionality             | Header(s)            | Browser Interpretation                       | Common Usage      |
| ------------------------- | -------------------- | -------------------------------------------- | ----------------- |
| **Via / X-Forwarded-For** |                      | Shows proxy chain.                           | ⚙️ Infrastructure |
| **Forwarded**             | `Forwarded: for=...` | Standardized proxy info.                     | ⚙️ Modern proxies |
| **Age**                   | `Age: 3600`          | Indicates how long resource has been cached. | ⚙️ CDNs           |

---

## ⚙️ 10. Miscellaneous and Optimization

| Functionality                 | Header(s)                        | Browser Interpretation                       | Common Usage        |
| ----------------------------- | -------------------------------- | -------------------------------------------- | ------------------- |
| **Link Preload / Prefetch**   | `Link: <style.css>; rel=preload` | Preloads assets for faster load.             | ⚙️ Performance      |
| **Retry-After**               | `Retry-After: 120`               | Tells client when to retry after rate-limit. | ⚙️ APIs             |
| **Allow**                     | `Allow: GET, POST`               | Lists valid HTTP methods.                    | ⚙️ REST docs        |
| **Upgrade-Insecure-Requests** | `1`                              | Requests HTTPS automatically.                | ✅ Security default |

---

## 🧭 Notes and Refs

This document was created after reading https://medium.com/@harish852958/forget-becoming-a-cto-heres-the-career-path-no-one-tells-you-about-665fcce45121, where https://medium.com/@harish852958 talks more about horizontal growth and gives an example with ETag:

```javascript
const data = await fetch("/api/users", {
  headers: {
    "Cache-Control": "max-age=300",
    "If-None-Match": lastETag,
  },
});
if (data.status === 304) {
  return cachedUsers;
}
const result = await data.json();
lastETag = data.headers.get("ETag");
```

over just

```javascript
const data = await axios.get("/api/users");
setUsers(data.data);
```

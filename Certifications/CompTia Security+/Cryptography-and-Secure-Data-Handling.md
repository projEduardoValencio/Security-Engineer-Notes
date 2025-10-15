# 🔐 Cryptography and Secure Data Handling

## 🧠 1. What Is Cryptography?

**Cryptography** is the science of protecting information by transforming it into an unreadable format, ensuring **confidentiality**, **integrity**, and **authenticity**.  
It’s a cornerstone of secure communication, protecting data both **at rest** and **in transit**.

---

## 🧱 2. Core Principles

| Principle | Description |
|------------|-------------|
| 🔒 **Confidentiality** | Ensures that only authorized parties can read the data. |
| 🧾 **Integrity** | Guarantees that data has not been altered or tampered with. |
| 🧍‍♂️ **Authentication** | Confirms the identity of a user, device, or system. |
| ⏳ **Non-repudiation** | Prevents denial of having sent or received a message. |

---

## 🧩 3. Symmetric vs. Asymmetric Encryption

### 🔁 3.1 Symmetric Key Encryption  
Uses **a single key** for both encryption and decryption.  
It’s fast and efficient but requires secure key exchange.

- Common algorithms: **AES**, **Twofish**, **3DES**  

### ⚖️ 3.2 Asymmetric Key Encryption  
Uses **two mathematically linked keys**:  
a **public key** for encryption and a **private key** for decryption.  

- Common algorithms: **RSA**, **ECC**  
- Used in: **TLS handshakes**, **digital signatures**, **email encryption**

---

## 🧬 4. Cipher Suites and Encryption Strength

A **cipher suite** defines the algorithms used for encryption, hashing, and key exchange in secure communications (e.g., TLS).

| Category | Example | Notes |
|-----------|----------|-------|
| ✅ **Strong** | AES-256-GCM, ChaCha20-Poly1305 | Modern, secure and efficient |
| ⚠️ **Weak** | 3DES, RC4, Blowfish | Deprecated — vulnerable to modern attacks |
| 🚫 **Obsolete** | WEP, early WPA | Broken and insecure, should never be used |

---

## 🪄 5. Data Masking and Tokenization

### 🧩 Data Masking  
Replaces sensitive data with **obfuscated values** that look realistic but have no exploitable meaning.  
Used in **non-production environments** or **application logs**.

Example:  
```
1234-5678-9012-3456 → 1234-XXXX-XXXX-3456
```

### 🪙 Tokenization  
Replaces sensitive values with **unique tokens** stored in a secure vault.  
Common in **payment systems** and **PII protection** — prevents real data exposure even if databases are compromised.

---

## 🧰 6. Key Management and Secure Hardware

| Tool | Description |
|------|--------------|
| 🧱 **TPM (Trusted Platform Module)** | Hardware chip storing cryptographic keys and performing secure operations. |
| 🔐 **HSM (Hardware Security Module)** | Dedicated device for generating, managing, and protecting high-value keys. |
| 🧠 **Key Manager** | Software solution controlling key lifecycle — generation, rotation, revocation. |
| 🍏 **Secure Enclave / TEE** | Isolated processor region (e.g., Apple Secure Enclave, Intel SGX) for sensitive computation. |

---

## 🔑 7. Key Exchange Protocols

Key exchange ensures two parties can securely share encryption keys over an insecure channel.

- **Diffie–Hellman (DH)** → classic key exchange  
- **Elliptic Curve Diffie–Hellman (ECDH)** → modern, faster alternative  
- Used in **TLS**, **SSH**, **VPNs**

---

## 🧂 8. Hashing and Salting

### 🧮 Hashing  
A **one-way transformation** that converts input data into a fixed-length digest.  
Used for verifying integrity and securely storing passwords.

Examples: **SHA-256**, **SHA-3**, **BLAKE2**

### 🧂 Salting  
Adds **random data** before hashing to protect against precomputed attacks like rainbow tables.

```text
salt = random()
hash = SHA256(password + salt)
```

---

## 🪢 9. Key Stretching

**Key stretching** strengthens weak passwords by increasing computational effort required to brute-force them.

| Algorithm | Description |
|------------|-------------|
| 🔁 **PBKDF2** | Applies HMAC multiple times to slow down guessing attacks. |
| ⚙️ **bcrypt** | Adaptive cost factor with salt; protects against GPU cracking. |
| 🧬 **scrypt / Argon2** | Memory-hard functions, ideal against parallel brute-force attacks. |

---

## ⚡ 10. Transient Encryption

**Transient encryption** involves **temporary keys** used only for short-lived sessions or data in transit.  
Common in **TLS sessions**, where ephemeral keys (e.g., ECDHE) are generated, used, and destroyed after connection ends — ensuring **forward secrecy**.

---

## ⛓ 11. Blockchain and Cryptography

Cryptography is the backbone of blockchain technology.

| Component | Role | Example |
|------------|------|----------|
| 🔏 **Hashing** | Links blocks together, ensures immutability | SHA-256 (Bitcoin) |
| ✍️ **Digital Signatures** | Verifies ownership and authenticity | ECDSA, Ed25519 |
| 🤝 **Consensus** | Uses cryptography for distributed agreement | Proof-of-Work, Proof-of-Stake |

---

## 🧭 12. TL;DR Summary

| Topic | Purpose | Example / Note |
|--------|----------|----------------|
| 🧩 Data Masking | Hide sensitive data | Masked logs |
| 🪙 Tokenization | Replace with secure tokens | Payment systems |
| 🔐 Encryption | Protect data in transit/rest | AES, RSA |
| 🧂 Hashing | Verify integrity or passwords | SHA-256 |
| ⚙️ Salting | Prevent rainbow table attacks | Randomized salts |
| 🔁 Key Stretching | Strengthen password hashes | PBKDF2, bcrypt |
| 🔑 Key Exchange | Secure key negotiation | ECDH, DH |
| 🧰 Tools | Key lifecycle and hardware protection | TPM, HSM |
| ⛓ Blockchain | Distributed trust and integrity | SHA-256 + ECDSA |

---

## 📚 13. References

- [NIST SP 800-57: Key Management Guidelines](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final)  
- [OWASP Cryptographic Storage Cheatsheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)  
- [RFC 8446 - TLS 1.3 Specification](https://www.rfc-editor.org/rfc/rfc8446)  
- [FIPS 197 - Advanced Encryption Standard (AES)](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.197.pdf)  
- [NIST SP 800-132: Password-Based Key Derivation](https://csrc.nist.gov/publications/detail/sp/800-132/final)  

---

## 💬 14. Final Note

This document summarizes key points that I took notes on and researched during my lessons on the PluralSight course.

During my years of experience, I have seen these technologies in the applications where I was working and also missed them in some projects.

---

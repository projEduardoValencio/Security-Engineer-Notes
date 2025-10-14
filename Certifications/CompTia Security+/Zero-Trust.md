# 🧱 Zero Trust & Zero Trust Network Architecture (ZTNA)

> **Core principle:**\
> 🔐 *Never trust, always verify.*

**Zero Trust Architecture (ZTA)** eliminates implicit trust in users,
devices, or networks --- requiring **continuous verification, strong
authentication, and dynamic access control** for every resource request.

------------------------------------------------------------------------

## 🧩 Zero Trust Core Principles

  **1️⃣ Least Privilege   Each user, device,   Service accounts with limited
  Access**               or application       rights; RBAC (Role-Based Access
                         should have **only   Control).
                         the minimum          
                         privileges           
                         required**.          

  **2️⃣                   Divides the network  VLANs, firewall policies between
  Micro-Segmentation**   into **small,        workloads, per-app ZTNA gateways.
                         isolated trust       
                         zones**, reducing    
                         lateral movement.    

  **3️⃣ Continuous        Constant monitoring  SIEM, EDR, UEBA.
  Monitoring**           of                   
                         **authentications,   
                         logs, traffic, and   
                         device posture**.    

  **4️⃣ Multi-Factor      Requires **at least  Password + physical token; PIN +
  Authentication (MFA)** two factors from     biometric.
                         different            
                         categories**.        

  **5️⃣ Device & User     Validates the        Device posture checks (antivirus,
  Identity**             **context and        patch level, geolocation).
                         integrity** of both  
                         the user and the     
                         device.              

  **6️⃣ Encryption        All data should be   TLS 1.3, AES-256, SSH, HTTPS, VPN.
  Everywhere**           **encrypted at rest  
                         and in transit**.    

  **7️⃣ Strict Access     Access policies      PDP/PEP enforcement; dynamic policy
  Control**              **based on context,  decisions.
                         identity, and        
                         risk**.              

  **8️⃣ Assume Breach**   Always assume the    Lateral detection, honeypots,
                         environment **has    adaptive segmentation.
                         already been         
                         compromised**.       

------------------------------------------------------------------------

## 🔐 MFA --- Multi-Factor Authentication

MFA is a critical pillar of Zero Trust.\
Its purpose is to ensure that **a single compromised factor cannot grant
access**.

### 🔸 Authentication Factor Categories

  **Something you know**      Knowledge       Password, PIN, unlock
                                              pattern

  **Something you have**      Possession      Physical token, smart card,
                                              authenticator app, e-SIM

  **Something you are**       Inherence       Fingerprint, facial
                                              recognition, voice, retina

  **Something you do**        Behavior        Typing pattern, mouse
                                              movement, signature

  **Where you are**           Context         Geolocation, source IP,
                                              time of access

  -----------------------------------------------------------------------

> ⚠️ **Important:** PIN + Password is **not** considered MFA, as both
> belong to the **same category** (*something you know*).\
> A true 2FA must combine **two or more different categories**, for
> example:\
> - ✅ *Password (know)* + *Authenticator App (have)*\
> - ✅ *Smart Card (have)* + *Fingerprint (are)*\
> - ❌ *Password (know)* + *PIN (know)*

In Zero Trust, MFA should not only occur at **initial login**, but also
during **contextual re-authentications**, such as when location, device,
or resource sensitivity changes.

------------------------------------------------------------------------

## ⚙️ Enforcement: *Policy Enforcement Point (PEP)*

Zero Trust enforcement depends on **clear separation of decision and
enforcement layers**:

  🧭 **Policy Decision      Evaluates access rules  "Brain" --- makes
  Point (PDP)**             based on identity,      decisions
                            context, device         
                            posture, time, etc.     

  🧱 **Policy Enforcement   Enforces PDP decisions  "Gatekeeper"
  Point (PEP)**             --- **allows, denies,   
                            or inspects** traffic.  

  🌐 **Control Plane**      Where decisions are     Defines policies
                            exchanged between PDP   
                            and PEP.                

  💾 **Data Plane**         Where actual data       Executes decisions
                            traffic flows.          

## 🔄 FTP vs Zero Trust-Aligned Protocols

  ------------------------------------------------------------------------------
  Comparison           FTP (Traditional)    Zero Trust-Compatible Protocols
  -------------------- -------------------- ------------------------------------
  **Authentication**   Basic user/password; HTTPS, SFTP, SMB3 support MFA and
                       no MFA.              session tokens.

  **Encryption**       Data and credentials TLS/SSH encrypt both channel and
                       sent in clear text.  data.

  **Continuous         Session trusted      Short-lived tokens and
  Verification**       after login.         reauthentication.

  **Access Control**   Static permissions   Contextual policies: who, where, and
                       per directory.       what device.

  **Segregation**      Single channel for   Separation of **Control Plane**
                       control and data.    (decision) and **Data Plane**
                                            (execution).

  ------------------------------------------------------------------------------

📉 **Summary:**\
**FTP** violates almost all Zero Trust principles:\
- No encryption\
- No strong authentication\
- Permanent trust after login\
- No dynamic access policies

In contrast, **SFTP, HTTPS, and SMB3** implement:\
- End-to-end encryption\
- MFA and session tokens\
- Control/Data plane separation

------------------------------------------------------------------------

## 🧠 Summary

-   Zero Trust is a **strategy**, not a product.\
-   Every request is treated as potentially malicious.\
-   Implementing **Least Privilege**, **MFA**, **Micro-Segmentation**,
    and **Continuous Monitoring** builds layered defense.\
-   Legacy protocols like **FTP** demonstrate what *not* to do ---
    implicit trust and no contextual validation.\
-   True Zero Trust relies on **dynamic decisions**, **continuous
    enforcement**, and **constant telemetry**.

------------------------------------------------------------------------

### 📚 References

-   [NIST SP 800-207 --- Zero Trust
    Architecture](https://csrc.nist.gov/publications/detail/sp/800-207/final)
-   [Microsoft Zero Trust Maturity
    Model](https://learn.microsoft.com/en-us/security/zero-trust/)
-   [CISA Zero Trust Maturity Model
    2.0](https://www.cisa.gov/resources-tools/resources/zero-trust-maturity-model)

------------------------------------------------------------------------

🧾 **Author:** Eduardo Santos\
📁 Directory: `/Certificacao/CompTIA-Security+/Zero-Trust.md`

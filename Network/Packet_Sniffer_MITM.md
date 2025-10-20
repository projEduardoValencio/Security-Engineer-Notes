# 🕵️‍♂️ Packet Sniffers, Wireshark, and Man‑in‑the‑Middle (MITM)

This note describes packet sniffer tools (with emphasis on **Wireshark**), how they are used for legitimate analysis and in **Man‑in‑the‑Middle (MITM)** scenarios, practical examples, detection and mitigation techniques, legal and ethical constraints, and recommended best practices.  
It includes a real-world anecdote about using a proxy to modify data arriving at a device (user-provided context).

---

## 2. Definitions and Core Concepts

- **Packet sniffer**: software or hardware that captures network traffic (packets) on an interface for analysis. Captures can include headers and payloads depending on the capture point and encryption.
- **Capture point**: location where capturing occurs — host (loopback), mirrored port / SPAN, gateway/router, wireless monitor interface, or inline (proxy/TAP).
- **Promiscuous mode** vs **Monitor mode**:
  - **Promiscuous**: NIC accepts all Ethernet frames on the same segment.
  - **Monitor**: wireless mode capturing 802.11 frames including management frames.
- **MITM (Man‑in‑the‑Middle)**: an actor intercepts and possibly alters communications between endpoints. Can be **passive** (observe) or **active** (modify/inject/drop).

---

## 3. Common Tools (quick reference)

| Tool | Type | Notes |
|------|------|-------|
| 🧾 **Wireshark** | GUI analyzer | Full protocol dissectors, display filters, follow streams, decryption with keys |
| 🖥️ **tshark** | CLI (Wireshark engine) | Scriptable captures and exports |
| 🐚 **tcpdump** | CLI capture | Low-overhead captures, BPF filters |
| 🔎 **ngrep** | CLI payload grep | Pattern matching in packet payloads |
| 🧰 **mitmproxy** | Intercepting proxy | Interactive HTTP/HTTPS interception, scripting |
| 🧪 **Burp Suite** | Web security proxy | Interception, automated scans, extensions |
| 🛰️ **Zeek (Bro)** | Network monitoring | Flow analysis, IDS-style logs |
| 🔌 **Hardware TAP / SPAN** | Passive capture | Non-intrusive capture at link level |

---

## 4. How Sniffers Enable or Support MITM

- **Passive reconnaissance**: sniffers capture sensitive data (credentials, tokens) when traffic is unencrypted or weakly encrypted.
- **Active MITM**: sniffing tools combined with techniques (ARP spoofing, rogue APs, transparent proxies, DNS spoofing) allow traffic interception **and modification**.
- **Decryption possibilities**:
  - Possess server private keys (rare with modern TLS using ECDHE).
  - Exported session keys (e.g., `SSLKEYLOGFILE` from browsers).
  - Client trusting a forged CA (corporate proxy or malicious CA installed on device).
  - Weak cipher suites or protocol downgrade attacks.

---

## 5. Typical MITM Scenarios & Setup Examples

### 5.1 Transparent proxy with TLS interception (mitmproxy / corporate proxy)
- Client traffic is forwarded to an inline proxy that terminates TLS and re‑encrypts to the server using a generated certificate.
- Requires the client to **trust** the proxy's CA (user-installed or preconfigured in enterprise).
- Allows modification of HTTP bodies, JSON payloads, headers, etc.

### 5.2 ARP spoofing + forwarding (LAN MITM)
- Attacker poisons ARP cache of victim and gateway, enabling traffic forwarding through the attacker's host.
- Capture tools (tcpdump/Wireshark) run on attack host to inspect or modify passing traffic.

### 5.3 Rogue Wi‑Fi access point
- Attacker advertises an open/compromised SSID; clients connect and route traffic through attacker-controlled gateway.

---

## 6. Practical Command Examples

> Use these examples for testing in controlled/lab environments only.

**Basic tcpdump capture (limit noise):**
```bash
sudo tcpdump -i eth0 -w capture.pcap 'tcp port 80 or tcp port 443'
```

**Rotate capture files (dumpcap):**
```bash
dumpcap -i eth0 -b filesize:10240 -w capture-%Y-%m-%d_%H%M.pcapng
```

**Wireshark display filters (examples):**
```
http.request or http.response
tls.handshake.type == 1
ip.addr == 10.0.0.5 && tcp.port == 443
```

**tshark extract HTTP bodies to files:**
```bash
tshark -r capture.pcap -Y "http" -T fields -e http.file_data > http_bodies.txt
```

**mitmproxy simple inline script (modify JSON field):**
```python
# modify_price.py
from mitmproxy import http
import json

def response(flow: http.HTTPFlow) -> None:
    if "api.example.com" in flow.request.host and flow.response.headers.get("content-type", "").startswith("application/json"):
        data = json.loads(flow.response.get_text())
        # Example: adjust influencer value
        if "display_value" in data:
            data["display_value"] = 999  # test modification
            flow.response.set_text(json.dumps(data))
```
Run:
```bash
mitmproxy --mode transparent -s modify_price.py
```

**Quick ARP spoofing detection (Linux):**
```bash
arp -an | grep -i '00:11:22:33:44:55'  # look for unexpected MACs
```

---

## 7. Real‑World Anecdote (user-provided)

> At one time I needed to use a proxy to alter data arriving at a device. Values shown by influencers were manipulated: the proxy changed the payload before it reached the device, so both the displayed value and subsequent input to the server were affected by the proxy's modifications.

**Key lessons from the incident:**
- Inline proxies can **silently change UI data** if server-side integrity checks are absent.
- Client-side validation is insufficient — the server must validate and not trust client-sent values.
- For test/QA interception, proxies must be isolated, audited, and used under explicit authorization.

---

## 8. Detection Indicators of MITM or Sniffing

- **TLS anomalies**: certificate warnings, unexpected CA chains, OCSP failures, pinning errors.
- **Network anomalies**: duplicate ARP replies, sudden gateway MAC changes, unexpected DNS responses.
- **Device changes**: new trusted root CA installed, modified proxy settings, browser SSL keylog presence.
- **Performance changes**: added latency due to proxy processing.
- **IDS/Firewall logs**: ARP spoofing alerts, DNS anomalies, unusual TLS handshakes.

---

## 9. Mitigations & Best Practices

- **Encrypt everything end‑to‑end**:
  - Use **TLS 1.2+ / TLS 1.3** with ECDHE key exchange and AEAD ciphers (AES‑GCM, ChaCha20‑Poly1305).
  - Prefer TLS 1.3 where possible.
- **Certificate hygiene**:
  - Enforce proper certificate validation, OCSP stapling, Certificate Transparency monitoring.
  - Use certificate pinning for high-risk applications.
- **Limit trusted CAs** and audit root stores regularly.
- **Server‑side validation**: never trust client-calculated values; validate all inputs and use signatures/hmacs for critical fields.
- **Network hardening**:
  - Use switch port security, DHCP snooping, Dynamic ARP Inspection.
  - Deploy 802.1X for device authentication.
- **Monitoring & detection**:
  - IDS/IPS (Suricata, Snort, Zeek) tuned for ARP/DNS/TLS anomalies.
  - Endpoint detection for unauthorized root CA additions or proxy changes.
- **Operational controls for proxies**:
  - Keep interception proxies in controlled test environments.
  - Protect their CA private keys as high-value secrets.
  - Maintain audit trails of interception activities.

---

## 10. Forensics: Capture & Reproducible Analysis

- **Metadata**: record timestamps (NTP sync), interface, capture filters, operator, and purpose.
- **File integrity**: compute hashes (e.g., `sha256sum capture.pcap`) for chain of custody.
- **Use BPF filters** to reduce noise; keep raw original and work copies.
- **Document decryption keys** and how they were obtained (e.g., server private key, SSLKEYLOGFILE).
- **Preserve evidence** (readonly copies, secure storage) and follow legal chain‑of‑custody procedures.

---

## References & Further Reading

- Wireshark Documentation — https://www.wireshark.org/docs/  
- mitmproxy Documentation — https://docs.mitmproxy.org/  
- OWASP Cheat Sheets — https://cheatsheetseries.owasp.org/  
- Zeek (Bro) — https://zeek.org/  
- Snort/Suricata documentation

---

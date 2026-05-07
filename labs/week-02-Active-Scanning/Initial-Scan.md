

---

# Lab Report: Active Scanning & Service Discovery

## 🎯 Objective
Transition from passive reconnaissance to active enumeration by identifying open ports, service versions, and potential defensive deceptions on the target infrastructure.

---

## ⚡ 1. Initial Stealth Service Discovery
**Target:** `skullsecurity.org` (185.199.111.153)
**rDNS:** `cdn-185-199-111-153.github.com`

**Command:**
```bash
sudo nmap -sS -Pn -F skullsecurity.org
```

### **Findings Summary**
The initial scan identified an unusually high number of open ports for a CDN-hosted static site.

| Port | State | Service | Significance |
| :--- | :--- | :--- | :--- |
| **21/tcp** | Open | FTP | Legacy file transfer; cleartext credential risk. |
| **22/tcp** | Open | SSH | Potential remote management entry point. |
| **23/tcp** | Open | Telnet | High-risk legacy protocol; insecure by design. |
| **25/tcp** | Open | SMTP | Possible mail relay or enumeration vector. |
| **445/tcp** | Open | SMB | Microsoft Directory Services (Lateral movement risk). |
| **3306/tcp** | Open | MySQL | Database exposure; potential for brute force. |
| **5900/tcp** | Open | VNC | Remote Desktop exposure; high-value target. |

### **🔍 SOC Perspective: Anomaly Detection**
The discovery of protocols such as **Telnet (23)** and **SMB (445)** on a GitHub-managed IP address is highly anomalous. Under standard conditions, a CDN should only expose web-related ports (80/443). This profile strongly suggests the presence of a **Honeypot** or **Stateful Firewall** configured to spoof open ports to deceive and track automated scanners.

---

## 🔍 2. Service Version Detection & Fingerprinting
**Objective:** Determine if the identified services are genuine or simulated "ghost" services.

**Command:**
```bash
sudo nmap -sV -Pn -p 21,22,23,25,80,443,445,3306 skullsecurity.org
```

### **Service Analysis**
| Port | Service | Version Result | Analysis |
| :--- | :--- | :--- | :--- |
| **21/tcp** | FTP | `ftp?` | Unrecognized fingerprint. |
| **22/tcp** | SSH | `ssh?` | Unrecognized fingerprint. |
| **80/tcp** | HTTP | `GitHub.com` | Verified CDN Hosting. |
| **445/tcp** | SMB | `microsoft-ds?` | Unrecognized fingerprint. |

### **🕵️‍♂️ SOC Analysis: Honeypot Identification**
The service detection phase yielded raw "Service Fingerprints" rather than standard version strings (e.g., OpenSSH 8.x). 
* **Conclusion:** The target utilizes **Deception Technology**. The ambiguous responses from ports 21, 22, and 445 indicate emulated services designed to capture attacker telemetry. 
* **Actionable Intel:** For a defender, this identifies a "high-alert" zone where any interaction beyond ports 80/443 is likely logged and attributed to malicious intent.

---

## 📜 3. Nmap Scripting Engine (NSE) Verification
**Objective:** Use default scripts to verify SSL configurations and web server metadata.

**Command:**
```bash
sudo nmap -sC -p 80,443 skullsecurity.org
```

### **Key Findings**
* **SSL Health:** Verified a valid certificate for `www.skullsecurity.org` (Common Name match).
* **SAN Validation:** Certificate is correctly scoped for both the root and `www` subdomains.
* **Redirection:** Confirmed Port 80 correctly enforces a redirect to HTTPS, reducing the risk of MITM attacks.

---

## 🛡️ Week 2 Summary & Conclusion
Week 2 demonstrated the importance of **critical thinking over tool output**. While the tools reported dozens of "Open" ports, cross-referencing that data with the known infrastructure (GitHub Pages) revealed a sophisticated defensive posture.

**Key Technical Achievements:**
* Successfully mapped an complex attack surface.
* Identified and analyzed service fingerprints to detect a honeypot.
* Validated security certificates and redirection logic for public-facing assets.

---

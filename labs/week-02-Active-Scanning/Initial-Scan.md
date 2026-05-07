## ⚡ 1. Initial Stealth Service Discovery
**Target:** `skullsecurity.org` (185.199.111.153)
**rDNS:** `cdn-185-199-111-153.github.com`

### **Findings Summary:**
The scan identified an unusually high number of open ports for a CDN-hosted static site.

| Port | State | Service | Significance |
| :--- | :--- | :--- | :--- |
| 21/tcp | Open | FTP | Legacy file transfer; cleartext risk. |
| 22/tcp | Open | SSH | Remote management access. |
| 23/tcp | Open | Telnet | High-risk legacy cleartext protocol. |
| 25/tcp | Open | SMTP | Potential mail relay vector. |
| 445/tcp | Open | SMB | Microsoft Directory Services (Lateral movement risk). |
| 3306/tcp | Open | MySQL | Database exposure. |
| 5900/tcp | Open | VNC | Remote Desktop exposure. |

### **🔍 SOC Perspective: Anomaly Detection**
The discovery of protocols like **Telnet (23)** and **SMB (445)** on a GitHub-managed IP address is highly anomalous. Under normal conditions, a CDN should only expose web-related ports (80/443). This profile strongly suggests the presence of a **Honeypot** or a **Stateful Firewall** configured to spoof open ports to deceive automated scanners.

**Next Step:** Perform **Service Version Detection (`-sV`)** to determine if these services are genuine or simulated "ghost" services.

## 🔍 2. Service Version Detection & Fingerprinting
**Objective:** Determine the specific software and versions running on the identified open ports.

**Command:**
```bash
sudo nmap -sV -Pn -p 21,22,23,25,80,443,445,3306 skullsecurity.org

Port,Service,Version Result,Analysis
21/tcp,FTP,ftp?,Unrecognized fingerprint.
22/tcp,SSH,ssh?,Unrecognized fingerprint.
80/tcp,HTTP,GitHub.com,Verified CDN Hosting.
445/tcp,SMB,microsoft-ds?,Unrecognized fingerprint.
```
## 🕵️‍♂️ SOC Analysis: Honeypot Identification
The service detection phase yielded "Service Fingerprints" (raw hex/text data) rather than standard version strings.

Conclusion: The target is utilizing a Honeypot or a Deception Technology layer. The services are likely emulated to capture attacker telemetry. For a SOC Analyst, this is a "Look but don't touch" indicator—engaging further with these ports would likely alert the target's monitoring systems and result in an IP ban.



---

# Lab Report: Passive Reconnaissance & DNS Enumeration

## 🎯 Objective
Perform passive information gathering on `skullsecurity.com` to map its infrastructure, identify mail servers, and determine hosting providers without direct interaction with the target's servers.

---

## 🛠️ 1. Domain Registration Analysis (WHOIS)
Using the `whois` utility, I extracted the administrative and organizational metadata for the target domain.

**Key Findings:**
*   **Registrar:** Squarespace Domains II LLC
*   **Creation Date:** 2006-12-04 (Established domain age: ~20 years)
*   **Name Servers:** `ns1.linode.com` through `ns5.linode.com` (Indicates infrastructure is managed via Linode/Akamai).
*   **Domain Status:** `clientTransferProhibited` and `clientDeleteProhibited` (Standard security locks in place).

---

## 📧 2. Mail Exchange (MX) Records
I utilized `dig` to query the Mail Exchange records to identify the organization's email provider.

**Command:**
```bash
dig skullsecurity.com MX
```

**Results:**
| Priority | Mail Server | Provider |
| :--- | :--- | :--- |
| 1 | `aspmx.l.google.com` | Google Workspace |
| 5 | `alt1.aspmx.l.google.com` | Google Workspace (Backup) |
| 5 | `alt2.aspmx.l.google.com` | Google Workspace (Backup) |
| 10 | `aspmx2.googlemail.com` | Google Workspace (Secondary) |
| 10 | `aspmx3.googlemail.com` | Google Workspace (Secondary) |

---
### 🌐 DNS Enumeration: skullsecurity.com
Successfully performed passive DNS reconnaissance to identify host IP addresses.

**Command:** `dig skullsecurity.com A`

**Findings:**
* Resolved 4 unique A records (IPv4).
* Identified the hosting infrastructure (GitHub Pages IP range).
* Verified DNS latency at ~71ms, confirming stable network pathing.
Here is the professional GitHub-flavored Markdown for your Week 1 Passive Reconnaissance lab report. 

This structure uses tables and code blocks to make the data highly readable for your portfolio.

## 🛡️ 3. SOC Analyst Summary

From a defensive and offensive perspective, this data reveals several critical points:
*   **External Reliance:** The target relies on **Google** for productivity/email and **Linode** for DNS/Hosting. An attacker would focus on these third-party trust relationships rather than trying to find a local on-premise mail server.
*   **Attack Surface:** The multiple MX records provide redundancy for the target, but also confirm they are fully integrated into the Google ecosystem.
*   **Lab Verification:** The `NOERROR` status and consistent response from the local DNS stub resolver (`192.168.32.2`) confirm that the lab's network stack is functioning optimally following the host-level NAT reset.


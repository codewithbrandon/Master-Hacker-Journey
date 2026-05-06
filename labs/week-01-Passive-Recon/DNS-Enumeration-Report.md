

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
*   **Name Servers:** `ns1.linode.com` through `ns5.linode.com` (Infrastructure managed via Linode/Akamai).
*   **Domain Status:** `clientTransferProhibited` and `clientDeleteProhibited`.

---

## 📧 2. Mail Exchange (MX) Records
I utilized `dig` to identify the organization's email provider and redundancy configuration.

**Command:**
```bash
dig skullsecurity.com MX
```

**Results:**
| Priority | Mail Server | Provider |
| :--- | :--- | :--- |
| 1 | `aspmx.l.google.com` | Google Workspace |
| 5 | `alt1.aspmx.l.google.com` | Google Workspace (Backup) |
| 10 | `aspmx2.googlemail.com` | Google Workspace (Secondary) |

---

## 🌐 3. Address (A) Records
Successfully performed passive DNS reconnaissance to identify host IP addresses.

**Command:** `dig skullsecurity.com A`

**Findings:**
*   Resolved **4 unique A records** (IPv4).
*   Identified hosting infrastructure within the **GitHub Pages** IP range.
*   Verified DNS latency at **~71ms**, confirming stable network pathing.

---

## 🛡️ 4. Email Security Analysis (SPF/TXT)
Querying TXT records to identify anti-spoofing configurations and domain ownership verifications.

**Command:** 
`dig skullsecurity.com TXT`

**Findings:**
*   **Record Identified:** `google-site-verification=XhzTwlgp1sc5YJEvy9v3YV7aaBrSqjM3i4lidRkY__I`
*   **Security Gap:** **No `v=spf1` (SPF) record detected.**

> [!CAUTION]
> **SOC Perspective:** The lack of an SPF record indicates a high risk for **Domain Spoofing** and **Phishing**. There is currently no mechanism for receiving Mail Transfer Agents (MTAs) to validate that an email truly originated from an authorized server.

---

## 🛠️ 5. Subdomain Discovery: Troubleshooting & Pivot
During active enumeration, the environment encountered a technical hurdle that required a strategic shift.

**Initial Approach:** Active Fuzzing via `ffuf`.
*   **Result:** 100% Error Rate (4614/4614).
*   **Root Cause Analysis:** The high error rate suggests egress filtering or DNS rate-limiting by the local gateway. Automated, high-velocity queries triggered a protective "drop" in connectivity.

**Strategic Pivot:** 
Switched to **Passive Discovery** using `subfinder` to query aggregated public records (OSINT) without triggering network-level defenses.

**Refined Results:**
*   **Command:** `subfinder -d skullsecurity.com`
*   **Findings:** Identified `[www.skullsecurity.com](https://www.skullsecurity.com)`.

---

## 🛠️ 6. Web Technology Fingerprinting
Performed deep service detection on the primary web asset to map the application stack.

**Command:**
```bash
whatweb -v https://www.skullsecurity.com
```

**Technical Findings:**
*   **Delivery Pipeline:** GitHub Pages origin server behind a Fastly/Varnish caching layer.
*   **Generator:** Jekyll v3.10.0 (Static Site Generator).
*   **Redirection Logic:** 301 Permanent Redirect chain from `.com` ➔ `.org` ➔ `www.skullsecurity.org`.
*   **Permissive Policy:** `Access-Control-Allow-Origin: *` (CORS).

---

## 🛡️ 7. Final SOC Analyst Summary

From both a defensive and offensive perspective, this reconnaissance phase reveals:

*   **Infrastructure Reliance:** The target relies heavily on **Google** (Email) and **GitHub/Linode** (Hosting/DNS). Attackers would likely focus on these third-party trust relationships rather than on-premise targets.
*   **Attack Surface:** The use of a static site (**Jekyll**) significantly reduces the attack surface by eliminating server-side processing and database dependencies.
*   **Remediation Priority:** The most critical finding is the **absence of an SPF record**, which should be flagged for immediate remediation to prevent organizational impersonation.
*   **Lab Integrity:** Consistent `NOERROR` statuses confirm that the lab environment and NAT reset are functioning optimally for future active scanning modules.

---


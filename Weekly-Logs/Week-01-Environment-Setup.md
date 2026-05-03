# Week 1: Lab Environment & Technical Onboarding

## 🎯 Objective
Establish a persistent, high-performance laboratory environment to support a 12-week intensive study of offensive security and SOC analysis.

---

## 🛠️ 1. Physical Environment & Hardware
*   **Virtualization Platform:** VMware Workstation (Local Disk Path).
*   **Host-to-Guest Configuration:** Migrated VM from cloud-syncing directories (OneDrive) to local storage (`C:\VirtualMachines\`) to optimize disk I/O performance and prevent file-locking conflicts.
*   **Storage Expansion:** Successfully expanded virtual hardware disk from **20GB to 80GB**. 
*   **Partition Management:** Utilized `GParted` to relocate the linux-swap partition and resize the `/dev/sda1` root partition to utilize the newly allocated space.

## 💻 2. Software & Tooling
*   **Primary Attacker:** Kali Linux (Rolling Release).
*   **Target Machine:** Metasploitable 2 (Verified connectivity via internal Host-Only network).
*   **Documentation Suite:** 
    *   **Notion:** Private technical deep-dives and raw logs.
    *   **GitHub:** Public-facing professional portfolio and lab reports.
    *   **LinkedIn:** Career transition updates and industry networking.

## 🔍 3. Initial Commands & Verification
```bash
# Verify Disk Expansion and Mount Point
df -h | grep sda1

# Verify Network Connectivity to Target Environment
ping -c 4 [Target_IP_Address]

# Systems Maintenance & Repository Synchronization
sudo apt clean && sudo apt update
```
## 🛠️ Infrastructure Troubleshooting & Security Analysis

### ⚠️ 4. Troubleshooting: Network Resolution Fix
**Issue:**  
Encountered `Temporary failure in name resolution` and `Network is unreachable` errors immediately following the VM directory migration.

**Root Cause:**  
Virtual network binding was disrupted during the physical move from OneDrive to local storage, causing the **NAT gateway (VMnet8)** to fail to hand off traffic from the host to the guest OS.

**Resolution Path:**  
*   **Access:** Launched the **VMware Virtual Network Editor** on the host machine with Administrative privileges.
*   **Reset:** Initialized **Restore Defaults** to re-index the virtual switches and repair the NAT service bridge.
*   **Validation:** Successfully verified restoration of **Layer 3 connectivity** via `ping 8.8.8.8` and confirmed DNS functionality with successful `whois` queries.

### 📝 Command Audit: Infrastructure Recovery
Below is the sequence of operations used to restore environment connectivity:

| Category | Commands |
| :--- | :--- |
| **Storage & Partitions** | `df -h`, `sudo gparted` |
| **Package Management** | `sudo apt clean`, `sudo apt update` |
| **Network Diagnostics** | `ping`, `ip route show`, `whois` |
| **Service Remediation** | `sudo systemctl restart NetworkManager` |
| **DNS Configuration** | `sudo nano /etc/resolv.conf` |
---

### 🛡️ 5. SOC Perspective (The "Why")
Prioritizing a **local lab setup** over cloud-synced folders is a critical security and operational baseline for the following reasons:

*   **Data Integrity:** Prevents file corruption and synchronization conflicts that occur when a cloud provider (like OneDrive) attempts to lock active VMDK files during operation.
*   **Information Leakage Prevention:** Ensures that sensitive artifacts, capture files (PCAPs), and exploit code remain within a controlled local environment rather than being automatically uploaded to third-party cloud storage.
*   **Operational Performance:** Eliminates network-induced disk latency that can trigger false negatives or timeouts in time-sensitive exploitation scripts and scanning tools.

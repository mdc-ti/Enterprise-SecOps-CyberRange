# Phase 1: Enterprise-Grade Network Segmentation, Zero Trust Isolation & DNS Hijacking via pfSense

## 📐 Network Architecture & Topology

The foundational phase of this Cyber Range focuses on transforming a standard flat home network into a highly secure, enterprise-grade virtualized infrastructure. The environment is hosted on a physical **Proxmox VE 8.x** hypervisor, utilizing **Netgate pfSense Community Edition** (allocated 2 vCPUs, 1.5 GB RAM) as the core stateful perimeter firewall and internal router.

To strictly enforce the Principle of Least Privilege (PoLP) and prevent unauthorized lateral movement, infrastructure isolation is structurally enforced at both Layer 2 (Data Link) and Layer 3 (Network) of the OSI model.

### Subnet Mapping & Allocation (RFC 1918)

* **WAN Interface (`em0` / `vmbr0`):** Bridged directly to the physical upstream network gateway. This acts as the untrusted external perimeter.
    * *IP Assignment:* DHCP (Static Lease: `192.168.1.138/24`)
* **DMZ Segment (`em1` / `vmbr1`):** An isolated virtual switch hosting public-facing and reverse-proxied corporate workloads (e.g., Nginx Proxy Manager, Nextcloud, Homer dashboards).
    * *Network Range:* `10.10.20.0/24`
    * *Gateway IP:* `10.10.20.1`
* **RED_SEGURA (Secure Internal Domain) (`em2` / `vmbr2`):** A highly restricted internal segment housing core enterprise identity and security assets (Windows Server 2022 Active Directory DC, Wazuh SIEM Management Server).
    * *Network Range:* `10.10.30.0/24`
    * *Gateway IP:* `10.10.30.1`

---

## 🛠️ Security Implementations & Hardening Baselines

### 1. Layer 2 & Layer 3 Micro-Segmentation
Workloads are physically decoupled using distinct Linux Bridges (`vmbr1` and `vmbr2`) within the hypervisor engine. This layout ensures that any inter-VLAN packet exchange is forced to traverse the pfSense routing engine, subjecting all lateral traffic to strict Access Control Lists (ACLs). Furthermore, the IPv6 stack has been completely decommissioned across all internal interfaces to minimize the attack surface and eliminate unintended routing leaks via SLAAC/DHCPv6.

### 2. Zero Trust Firewall Policies
A strict **Zero Trust Default-Deny** posture was adopted on the DMZ interface. In pfSense, firewall rules are evaluated sequentially (Top-Down). The standard "Allow All" rule was locked down by prepending explicit block actions:
* **Rule 1:** Block all traffic from `DMZ net` targeting `RED_SEGURA net` (Prevents lateral compromise of Active Directory/SIEM from breached web applications).
* **Rule 2:** Block all traffic from `DMZ net` targeting the physical LAN perimeter (`192.168.1.0/24`).

### 3. Transparent DNS Hijacking (DNAT Redirection)
To prevent rogue network assets or malware from bypassing security filtering (via hardcoded DNS settings like `8.8.8.8` or `1.1.1.1`), a Port Forwarding rule (DNAT) was deployed on port 53. Any DNS query originating from the DMZ is transparently intercepted and redirected to the internal secure DNS resolver (**AdGuard Home**), forcing centralized corporate DNS visibility and logging.

---

## 🔍 Verification & Audit Logs (Proof of Concept)

### Inter-VLAN Block Verification
ICMP echo requests and TCP handshakes initiated from a test container inside the DMZ segment (`10.10.20.X`) targeting any asset on the Secure Segment (`10.10.30.X`) are silently dropped by the packet filter engine. Firewall system logs confirm the stateful drop action under rule telemetry.

### DNS Redirection Validation
Executing a forced query from an internal host:
```bash
nslookup google.com 8.8.8.8
# Enterprise-Grade Network Segmentation, Zero Trust Isolation & DNS Hijacking with pfSense

**Cyber Range Lab - Phase 1: Perimeter Hardening & Infrastructure Security**

This repository contains the architectural design, technical documentation, and implementation details for Phase 1 of my Cyber Range Lab. The primary objective of this project is to transform a standard flat network into a highly secure, enterprise-grade virtualized environment using pfSense on a physical Proxmox VE hypervisor, implementing strict network segmentation, stateful access controls, and transparent traffic redirection.

## Architectural Topology & Subnet Mapping

The infrastructure isolation is enforced at both Layer 2 (Data Link) and Layer 3 (Network) of the OSI model, eliminating any single broadcast domain and restricting unauthorized lateral movement.

* Hypervisor Engine: Proxmox VE 8.x
* Firewall Engine: Netgate pfSense Community Edition (2 vCPUs, 1.5 GB RAM)

### Subnet Allocation (RFC 1918)

* WAN Interface (em0 / vmbr0): Bridged to the physical upstream network gateway. Acts as the untrusted external perimeter.
  * IP Assignment: DHCP (Static Lease: 192.168.1.138/24)
* DMZ Segment (em1 / vmbr1): Isolated virtual switch containing public-facing or reverse-proxied workloads (e.g., Nginx Proxy Manager, Nextcloud, Homer).
  * Network Range: 10.10.20.0/24
  * Gateway IP: 10.10.20.1
* RED_SEGURA Segment (em2 / vmbr2): Highly restricted internal domain housing core enterprise assets (e.g., Windows Server 2022 Active Directory DC, Wazuh SIEM Server).
  * Network Range: 10.10.30.0/24
  * Gateway IP: 10.10.30.1

## Security Implementations

### 1. Layer 2 & Layer 3 Isolation
Workloads are physically decoupled utilizing separate Proxmox Linux Bridges (vmbr1 and vmbr2). This configuration strictly ensures that packet delivery between segments must traverse the pfSense firewall routing engine, forcing compliance with layer 3 access control lists (ACLs). IPv6 stacks have been entirely decommissioned (None) across internal interfaces to shrink the attack surface and eliminate unintended tunneling or routing leaks via SLAAC/DHCPv6.

### 2. Stateful Firewalling & Zero Trust Implementation
A Zero Trust security posture was adopted on the DMZ perimeter interface. By default, pfSense evaluates policies sequentially (Top-Down). The default "Allow All" configuration was restricted by placing precise Block actions ahead of the egress rule.

## Verification & Audit Logs
* Inter-VLAN Block Verification: ICMP echo requests and TCP handshakes initiated from the DMZ segment (10.10.20.X) targeting any IP on 10.10.30.X or 192.168.1.X are silently dropped by the firewall filter engine.
* DNS Hijacking Proof-of-Concept: Executing nslookup google.com 8.8.8.8 from a container within the DMZ reports a successful resolution, but the query telemetry is actively intercepted, filtered, and explicitly logged.

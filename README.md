# Enterprise SecOps Cyber Range & Autonomous SOC

Welcome to my **Enterprise-Grade Cyber Range**. This repository documents the end-to-end deployment, defensive hardening, and automation of a simulated corporate IT infrastructure built within my Homelab. The primary objective of this scalable project is to apply advanced SecOps practices, ranging from Zero Trust network micro-segmentation to autonomous, AI-powered incident response.

---

## 👤 About the Author
**Manuel del Campo Ortiz** - *Junior SOC Analyst & Systems Administrator*
Passionate about Infrastructure as Code (IaC), defensive cybersecurity, detection engineering, and continuous hands-on learning.

---

## 📐 Infrastructure Architecture & Phases

The laboratory core is structured into **4 completed logical phases**, scaling from physical network isolation to automated threat hunting. 

Click on each phase to access the **detailed technical documentation, network diagrams, and Proof of Concepts (PoC)**:

### 🛡️ [Phase 1: Perimeter Hardening & Network Segmentation](./01_Network_Security_pfSense/README.md)
* **Technologies:** Netgate pfSense Community Edition, Proxmox VE (VLANs / Linux Bridges), AdGuard Home.
* **Key Milestones:** Enforced Zero Trust isolation on the DMZ perimeter, stateful firewalling, complete decommissioning of the IPv6 stack to reduce attack surface, and transparent DNS Hijacking via DNAT redirection.

### 🔑 [Phase 2: Identity Hardening & Active Directory](./02_Identity_Hardening_AD/README.md)
* **Technologies:** Windows Server 2022, Group Policy Objects (GPOs).
* **Key Milestones:** Custom OU hierarchy following the Principle of Least Privilege (PoLP), LLMNR/NBT-NS poisoning mitigation, strict Living-off-the-Land (LotL) execution restrictions, USB data exfiltration prevention, and forensic audit logging enforcement.

### 👁️ [Phase 3: SIEM Implementation & Active Response (IPS)](./03_SIEM_Active_Response_Wazuh/README.md)
* **Technologies:** Wazuh SIEM/XDR, Linux (Ubuntu/Proxmox), Netfilter/iptables.
* **Key Milestones:** Centralized endpoint telemetry ingestion, alert correlation mapping to the MITRE ATT&CK framework, automated active response triggers for real-time SSH brute-force containment, and Red Team simulation via direct log injection.

### 🤖 [Phase 4: AI-Powered Alerting Pipeline & SOAR Automation](./04_SOAR_AI_Automation/README.md)
* **Technologies:** n8n, Ollama (Llama 3 8B), Telegram Bot API.
* **Key Milestones:** Intercepted critical SIEM alerts, automated Tier 1 SOC triage via precise Prompt Engineering using a **100% on-premises LLM** (ensuring absolute data sovereignty and 0% Cloud API leaks), and real-time push notification gateway to mobile devices.

---

## 🗺️ Project Roadmap (Future Expansions)

This Cyber Range is designed as an open-ended, modular infrastructure. The following active developments and modules are planned to be integrated into the range. 

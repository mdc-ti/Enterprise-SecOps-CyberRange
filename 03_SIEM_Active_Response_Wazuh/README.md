# Phase 3: Centralized SIEM/XDR Deployment & Automated Active Response (IPS) with Wazuh

## 👁️ Overview & Laboratory Architecture

The critical objective of this phase is to eliminate monitoring blind spots across the virtualized corporate environment and transition the defensive infrastructure from a purely reactive stance (Intrusion Detection System - IDS) to an autonomous, proactive model (Intrusion Prevention System - IPS).

The monitoring matrix consists of a centralized orchestration architecture:
* **Central Brain (Wazuh Manager):** A dedicated security server hosted on Ubuntu Server at IP `192.168.1.144`.
* **Virtualization Node (Proxmox VE):** The bare-metal hypervisor host system (`192.168.1.138`) running lightweight Wazuh EDR agents.
* **Identity Node (Windows Server DC):** The active directory controller (`ad-windows-server`) running the Windows Wazuh agent to monitor authentication events.

---

## 🛡️ Technical Milestones & Engineering Phases

### 1. Endpoint Detection & Response (EDR) Deployment
Lightweight Wazuh agents were systematically deployed across all targeted production nodes. Telemetry streams were validated to ensure 100% visibility over active processes, file integrity monitoring (FIM), system configuration assessments (SCA), and continuous software vulnerability inventories.

### 2. Threat Hunting Simulation (SSH Brute Force)
Controlled cyber attacks were simulated targeting the open SSH management infrastructure (Port 22) on exposed hosts. Raw log generation streams were analyzed as malicious actors attempted to brute-force authentication barriers using extensive credential guessing dictionaries.

### 3. Transitioning from IDS to IPS (Active Response Integration)
To neutralize threats dynamically without human intervention, automated remediation scripts (`Active Response`) were programmed into the central Wazuh configuration. When specific alert thresholds are triggered (e.g., multiple failed SSH login attempts within a tight timeframe), the Wazuh Manager commands the remote agent to interact with the local packet filtering subsystem (**Netfilter/iptables**), instantly dropping all subsequent traffic originating from the attacker's IP address.

---

## 🔍 Verification & Red Team Log Injection Proof of Concept (PoC)

To audit the detection engine thresholds and evaluate how the infrastructure responds to internet-sourced threats bypassing management whitelists, a Red Team log injection loop was executed. 

By executing a direct shell loop targeting the internal Proxmox log sockets, a rapid succession of spoofed external authentication failures was generated:

```bash
for i in {1..6}; do logger -t sshd "Failed password for invalid user batman from 8.8.8.8 port 5000 ssh2"; done
📈 Execution Results & Telemetry Flow
Log Generation: The system socket registers 6 rapid-fire high-severity SSH authentication failures coming from the rogue IP 8.8.8.8.

Ingestion & Decoding: The Wazuh agent captures the stream, sends it to the central manager (192.168.1.144), which decodes it under rule ID 5712 (SSHD brute force trying to gain access).

MITRE ATT&CK Mapping: The alert is correlated automatically to MITRE T1110 (Brute Force).

Active Response Action: The IPS subsystem triggers an immediate reactive drop instruction. The agent on the affected node appends a temporary block rule to its local iptables ruleset, severing all network connectivity from 8.8.8.8 in milliseconds.📈 Execution Results & Telemetry Flow
Log Generation: The system socket registers 6 rapid-fire high-severity SSH authentication failures coming from the rogue IP 8.8.8.8.

Ingestion & Decoding: The Wazuh agent captures the stream, sends it to the central manager (192.168.1.144), which decodes it under rule ID 5712 (SSHD brute force trying to gain access).

MITRE ATT&CK Mapping: The alert is correlated automatically to MITRE T1110 (Brute Force).

Active Response Action: The IPS subsystem triggers an immediate reactive drop instruction. The agent on the affected node appends a temporary block rule to its local iptables ruleset, severing all network connectivity from 8.8.8.8 in milliseconds.
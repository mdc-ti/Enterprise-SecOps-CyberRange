# Project 3: SIEM/EDR Implementation and Automated Active Response (IPS) with Wazuh

## Overview
The critical purpose of this project was to completely eliminate blind spots in a simulated corporate network and evolve the architecture from a purely reactive approach (Intrusion Detection System - IDS) to a proactive, autonomous, and dynamic one (Intrusion Prevention System - IPS). 

By deploying lightweight agents on exposed endpoints (a Linux virtualization hypervisor and a Windows Domain Controller), raw logs ingestion was centralized for normalization, decoding, and correlation. Finally, automated triggers (Active Responses) were programmed to intercept and immediately neutralize common intrusion vectors (brute force and credential guessing attacks) at the affected host's network layer.

## Laboratory Architecture
* Central Brain (Wazuh Manager): Dedicated server hosted at IP 192.168.1.144. 
* Virtualization Node (Proxmox VE): Base physical server (192.168.1.138) hosting the lab. 
* Identity Node (Windows Server AD): Virtualized Active Directory Domain Controller (ad-windows-server).

## Project Phases and Technical Milestones
1. Agent Deployment and Telemetry Ingestion (EDR): 100% visibility validation of active nodes and establishment of continuous vulnerability inventory.
2. Threat Simulation and Investigation (Threat Hunting): Execution of brute force attacks targeting port 22 (sshd).
3. Transition from IDS to IPS (Active Response): Injection of reactive scripts in remote agents to intercept and interact with local packet filtering subsystems.
4. Advanced Auditing and Red Team Simulation: Use of direct Log Injection techniques on the infrastructure (Syslog injection) to emulate attacks.

## Command Guide and Proof of Concept (PoC)
To evade internal administration whitelists and emulate an attack coming from the internet, a direct injection loop was executed in the Proxmox log socket:
`for i in {1..6}; do logger -t sshd "Failed password for invalid user batman from 8.8.8.8 port 5000 ssh2"; done`

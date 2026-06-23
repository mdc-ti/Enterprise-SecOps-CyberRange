# Enterprise Active Directory Deployment & Security Hardening

## Overview
This repository documents the deployment, structural segmentation, and defensive hardening of an Active Directory Domain Services (AD DS) infrastructure. Built on Windows Server 2022, this project simulates a corporate environment (cyberrange.local) and focuses on mitigating common identity-based attack vectors through strict Group Policy Object (GPO) enforcement.

## Architecture & Organizational Segmentation
To adhere to the Principle of Least Privilege (PoLP) and avoid the security pitfalls of a default flat directory structure, a custom Organizational Unit (OU) hierarchy was implemented.

Root Forest: cyberrange.local
* Protected Container: CYBERRANGE (Accidental deletion protection enabled)
  * IT: System Administrators and Technical Service Accounts.
  * RRHH: Human Resources personnel.
  * Ventas: Sales and commercial operations.

## Defensive Hardening Baselines (GPO)
The Default Domain Policy was extensively modified to align with modern cybersecurity baselines (e.g., CIS Benchmarks / ENS), neutralizing prevalent lateral movement and privilege escalation techniques:

1. LLMNR Poisoning Mitigation: Turn off multicast name resolution -> Enabled
2. Cryptographic & Credential Hardening: Minimum password length -> 12 Characters
3. Physical Threat & Data Exfiltration Prevention: All Removable Storage classes: Deny all access -> Enabled
4. Living-off-the-Land (LotL) Restrictions: Prevent access to the command prompt -> Enabled
5. Security Telemetry & Forensics: Audit logon events -> Success & Failure Enabled

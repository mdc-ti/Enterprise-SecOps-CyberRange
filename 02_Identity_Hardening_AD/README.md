# Phase 2: Enterprise Active Directory Deployment & Identity Hardening Baselines

## 🔑 Architecture & Organizational Segmentation

This phase focuses on the structural implementation and defensive hardening of an Active Directory Domain Services (AD DS) core infrastructure. Built natively on **Windows Server 2022**, the environment simulates a production corporate forest domain named `cyberrange.local`.

To adhere to the **Principle of Least Privilege (PoLP)** and eliminate the severe security risks associated with default flat directory structures, a strict custom Organizational Unit (OU) hierarchy was enforced. 

### Domain Forest Structure: `cyberrange.local`
* **Protected Root Container:** `CYBERRANGE` *(Accidental deletion protection flags enabled across all nested objects)*.
    * 📂 `IT` / `SysAdmins`: Houses administrative accounts, break-glass credentials, and technical service principles.
    * 📂 `RRHH` (Human Resources): Contains standard employee identities associated with corporate staff personnel.
    * 📂 `Ventas` (Sales): Hosts general business operation accounts restricted from touching internal technical assets.

---

## 🛡️ Defensive Hardening Baselines (GPO Enforcement)

The Default Domain Policy and custom high-severity Group Policy Objects (GPOs) were extensively modified and deployed to align with rigorous modern cybersecurity baselines (e.g., CIS Benchmarks). These configurations actively neutralize prevalent credential harvesting, lateral movement, and privilege escalation vectors:

### 1. LLMNR & NBT-NS Poisoning Mitigation
* **Policy:** *Turn off multicast name resolution* -> **Enabled**
* **Impact:** Disables the legacy Link-Local Multicast Name Resolution protocol domain-wide. This effectively neutralizes automated credential-sniffing and spoofing tools (such as `Responder`) utilized by adversaries to capture NetNTLM hashes during local network broadcasts.

### 2. Cryptographic & Credential Hardening
* **Policy:** *Minimum password length* -> Set to **12 Characters**
* **Impact:** Dramatically increases the mathematical entropy required for offline brute-force and dictionary attacks on captured identity hashes.

### 3. Physical Threat & Data Exfiltration Prevention
* **Policy:** *All Removable Storage classes: Deny all access* -> **Enabled**
* **Impact:** Prevents malicious insider threats or physical attackers from introducing rogue payloads via USB drives, while simultaneously neutralizing data exfiltration capabilities on managed endpoints.

### 4. Living-off-the-Land (LotL) Execution Restrictions
* **Policy:** *Prevent access to the command prompt* -> **Enabled**
* **Impact:** Restricts standard users from executing `cmd.exe` or interactive scripts, severely crippling an attacker's ability to live off the land using native system binaries for local discovery or enumeration.

### 5. Security Telemetry & Forensics Enforcement
* **Policy:** *Audit logon events* -> Configured for **Success & Failure**
* **Impact:** Generates critical Windows Event Logs (such as Event ID 4624/4625). This ensures granular forensic telemetry is captured and ready to be ingested by centralized monitoring systems for threat detection.
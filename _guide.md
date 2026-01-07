# Goal
- Penalty - Compliance (Asset Management, Baseline, patch, decommissioning)
- Ransom, Disaster, Loss - Recovery (Backup)
- DDOS - Availablity (Redudant, Load Balancer)
- Monitor Employee - (DNS filtering)
- Intelectual Property (white list, compensating control)

# Cause
- Social Engineering < least privilege 
- Vulnerabilities < patch, compensating control
- Weak Configuration < Baseline
- Configuration Drift (Shadow IT) < Managemnt
- Lack of Security Controls < [EDR, Firewall, DLP, MDM...] 

# General Concept
"Ideally, security is designed into these systems from the start, considering aspects such as secure coding practices, minimal design (where the system has only the features it needs to perform its function), secure boot mechanisms, physical tamper-proofing, and comprehensive security testing."

- Authentication, Authorization, Access Control 
- Configure (Baseline + Tool)
- Unnecesary port, service, app, NIC | peer-to-peer function (PAN), Wi-Fi Direct, BT, BLE, NFC
- Encrypted
- Patch
- Management
- Accounting, Monitor
- Security Product(anitimalware, firewall, IDS, EDR, DLP)

- Network
    - Segment
    - 
- Endpoint
    - registry limited
    - Disk Encryption (Self-encrypting Drive)
    - Intergrity Check (File, Memory...), Hash


# My Subjective Note
- AntiVirus (signature-based) is useless
- Security Product (IDS, EDR, UTF...) only work when come along with the people know how to use it (Think other case in real world)


---

# Windows
- kerberos > NetNTLM(Legacy)
- If SNMP is not used, it should be disabled
- **GPO**
- OS
    -Device Management
        - **Security Compliance Toolkit** (Microsoft Baseline Security Analyzer *obsolted*)
- **System Center Configuration Manager**

# Linux
- **SELinux**

---

# Practical

. Hosts should always be configured to prevent autorun when USB devices are attached. USB ports can be blocked altogether using most types of host intrusion detection systems (HIDS).

# Standard and Certification
- Common Criteria [https://www.commoncriteriaportal.org/index.cfm]
- ISO/IEC 15408, IEC 62443, MISRA-C, and CERT Secure Coding Standards.
- ISO 27001, IEC 61508, and others.
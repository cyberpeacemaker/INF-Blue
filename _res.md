#
OpenID, SAML, OAuth, WS-Security, ADFS
| 類別 | 代表工具 (開源/主流) | 核心功能 (一句話形容) | 自動化整合點 |
|-----|--------------------|------------------|-------------|
| TIP (情資管理) | MISP, ThreatConnect, Recorded Future | 「誰是壞人？」：收集並關聯全球威脅情資。 | 自動同步外部黑名單，產出電腦可讀的情資格式。 |
| SOAR (編排回應) | Cortex XSOAR, Splunk SOAR, Shuffle | 「自動做事」：串接所有設備，跑自動化劇本。 | 根據情資，自動下指令給防火牆封鎖或通知管理員。 |
| SBOM (零件清單) | Dependency-Track, Syft / Grype, Trivy | 「裡面裝啥？」：管理軟體組件與漏洞。 | 當新漏洞爆發，自動定位公司內哪些軟體受災。 |
| SIEM / XDR (監測分析) | ELK (Elastic), Splunk, Microsoft Sentinel | 「發生什麼事？」：分析日誌，發出警報。 | 觸發 SOAR 開始跑劇本，並比對 TIP 情資。 |

# Web
- **Windows Security Docs** [https://learn.microsoft.com/en-us/windows/security/]
- Microsoft SCT [https://learn.microsoft.com/en-us/windows/security/operating-system-security/device-management/windows-security-configuration-framework/security-compliance-toolkit-10]
- **CISA Services** [https://www.cisa.gov/resources-tools/services]
- gcatoolkit [https://gcatoolkit.org/tool/adblock/]
- AbusedIDPB
- **VirusTotal** [https://www.virustotal.com/gui/home/upload]
- CIS Benchmarks
- Security Technical Implementation Guides (STIGs) < Defense Information Systems Agency (DISA) 
- DNSChecker [https://dnschecker.org/]
- Message Header Analyzer [https://mha.azurewebsites.net/]
- zero trust CISA [https://www.cisa.gov/zero-trust-maturity-model]

# Education Framework
- NIST NICE [https://www.nist.gov/itl/applied-cybersecurity/nice]

# Threat Feed
- Alien Lab Open Threat Exchange
- IBM X-Force Exchange
- Recorded Future

- Cyber Threat Alliance [https://www.cyberthreatalliance.org/]
- MISP [https://www.misp-project.org/]

- IThome
- 資通安全研究院
- 中研院資安中心
- DARPA (https://www.darpa.mil/news)
- CISA [Cybersecurity & Infrastructure Security Agency] (https://www.cisa.gov/)
- Twitter
- Feedly
- thehackernews.com

# Open-source Intelligence (**OSINT**) 
- [Shodan, Maltego, Recon-ng, theHarverster]
- OSINT framework [https://github.com/lockfale/osint-framework]

# Threat Intelligence
- MITRE ATT&CK Matrix
- exploit database (https://www.exploit-db.com/)
- NIST Cybersecurity Framework
- CISA [https://www.cisa.gov/]
- Github awesome-threat-intelligence
- Github awesome-threat-detection
- Google Threat Intelligence [https://cloud.google.com/security/products/threat-intelligence]
- Mandiant Trends Report [https://cloud.google.com/security/resources/m-trends]
- Cybersecurity & Infrastructure Security Agency
- Microsoft Security [https://www.microsoft.com/en-us/security/]

# Info

---

# Net
- Capturing raw packets [WinPcap, Npcap]
- Wireshark
- Tcpdump
- Tshark
\
- Glasswire
- Zeek [https://zeek.org/]
- splunk

# Analysis
- PeStudeio

- **ProcMon** (ProcDOT)
- Regshot

---
- SysTracer
- Regshot-advanced
- Windows Sandbox


# Forensic
- ✅Autopsy [https://www.autopsy.com/]
- EnCase [https://e-forensic.ca/category/hardware-computers/]
- (business Email) FTK Imager [https://www.exterro.com/ftk-product-downloads/ftk-imager-4-7-3-81]
- ✅Volatility 3 [https://github.com/volatilityfoundation/volatility3]
- WinPmem
- KAPE

- ✅Dumpit
- Velociraptor [https://docs.velociraptor.app/]
- Magnet RAM Capture [https://www.magnetforensics.com/resources/magnet-ram-capture/]
- WinDbg
- Comae Platform
- MemProcFS [https://github.com/ufrisk/MemProcFS]

## Registry Explorer
- Eric Zimmer [https://ericzimmerman.github.io/#!index.md]
- Autopsy
- FTK / EnCase
- X-Ways Forensics

## Log
- Elastic Stack

# Detection
- RITA [https://www.activecountermeasures.com/free-tools/rita/]
---

# Honeypot
- pentbox

# Remove
- Revo Uninstaller

# Encrypt

# Baseline

# VM
- Joe Sandbox
- Cuckoo Sandbox

---

# IDS/IPS
- Host
    - ossec
- **Snort**
- Suricata
- Security Onion

# SIEM
- Security Onion

# File Integrity Monitoring
- Tripwire
- OSSEC

# User (and Enity) Behavior Analytics
- Splunk User Behavior Anayltics
- IBM QRader User Behavior Analytics
- Rapid7

# Mobile Device Management
- Apple MDM
- Android Enterprise
- Microsoft Intune
- VMware AirWatch
- IBM MaaS360

# DNS Filtering
- Cisco OpenDNS, Quad0, CleanBrowsing
- Pi-Hole
- ADGuard

# Static Code Analysis
- SonarQube
- Coverity
- Fortify

# Vulnerable scanning
- Greenbone OpenVAS
- Tenable Nessus

# SBOM
- CycloneDX [https://cyclonedx.org/]
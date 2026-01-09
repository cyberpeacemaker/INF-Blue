# Log
| Log Type        | Usage | Example |
|-----------------|-------|---------|
| System Logs | The system logs can be helpful in troubleshooting running issues in the OS. These logs provide information on various operating system activities. | System Startup and shutdown events<br>Driver Loading events<br>System Error events<br>Hardware events |
| Security Logs | The security logs help detect and investigate incidents. These logs provide information on the security-related activities in the system. | Authentication events<br>Authorization events<br>Security Policy changes events<br>User Account changes events<br>Abnormal Activity events |
| Application Logs | The application logs contain specific events related to the application. Any interactive or non-interactive activity happening inside the application will be logged here. | User Interaction events<br>Application Changes events<br>Application Update events<br>Application Error events |
| Audit Logs | The Audit logs provide detailed information on the system changes and user events. These logs are helpful for compliance requirements and play a vital role in security monitoring. | Data Access events<br>System Change events<br>User Activity events<br>Policy Enforcement events |
| Network Logs | Network logs provide information on the network’s outgoing and incoming traffic. They play a crucial role in troubleshooting network issues and incident investigations. | Incoming Network Traffic events<br>Outgoing Network Traffic events<br>Network Connection Logs<br>Network Firewall Logs |
| Access Logs | The Access logs provide detailed information about access to different resources. | Webserver Access Logs<br>Database Access Logs<br>Application Access Logs<br>API Access Logs |

#  Windows Registry Hives Overview

| Hive Name | Contains | Location |
| --- | --- | --- |
| **SYSTEM** | Services, Mounted Devices, Boot Configuration, Drivers, Hardware | `C:\Windows\System32\config\SYSTEM` |
| **SECURITY** | Local Security Policies, Audit Policy Settings | `C:\Windows\System32\config\SECURITY` |
| **SOFTWARE** | Installed Programs, OS Version and other info, Autostarts, Program Settings | `C:\Windows\System32\config\SOFTWARE` |
| **SAM** | Usernames and their Metadata, Password Hashes, Group Memberships, Account Statuses | `C:\Windows\System32\config\SAM` |
| **NTUSER.DAT** | Recent Files, User Preferences, User-specific Autostarts | `C:\Users\username\NTUSER.DAT` |
| **USRCLASS.DAT** | Shellbags, Jump Lists | `C:\Users\username\AppData\Local\Microsoft\Windows\USRCLASS.DAT` |

# Key Windows Registry Artifacts

| Registry Key Path | Importance / Forensic Value |
| --- | --- |
| **HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist** | Stores info on recently accessed apps launched via GUI. Often **ROT13 encoded**; includes run counts and last execution time. |
| **HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\TypedPaths** | Stores the last 25 paths/URLs manually typed or pasted into the File Explorer address bar. |
| **HKLM\Software\Microsoft\Windows\CurrentVersion\App Paths** | Maps executable names to their full file system paths, allowing them to be run from the "Run" dialog without the full path. |
| **HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery** | Contains the search terms a user has typed into the search box in File Explorer. |
| **HKLM\Software\Microsoft\Windows\CurrentVersion\Run** | System-wide startup programs that launch for **every user** who logs into the machine. |
| **HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs** | Tracks recently opened or saved files/folders, categorized by file extension. |
| **HKLM\SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName** | Stores the active NetBIOS hostname of the computer. |
| **HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall** | A master list of installed applications, including version numbers, install dates, and uninstall strings. |


---

An offline attack means that the attacker has managed to obtain a database of password hashes, such as %SystemRoot%\System32\config\SAM, %SystemRoot%\NTDS\NTDS.DIT (the Active Directory credential store) or /etc/shadow
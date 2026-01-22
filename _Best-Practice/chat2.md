Great — below is a **ready-to-run PowerShell hardening script** followed by a **concise Windows 11 network hardening checklist**.
Everything here is safe for **security-focused home users, developers, and most corporate endpoints** (with notes where things may break).

---

# 🔐 PowerShell Script: Disable UPnP, mDNS, Discovery (Win11)

### ✅ What this script does

* Disables UPnP + SSDP services
* Disables discovery-related services
* Blocks UPnP (UDP 1900) and mDNS (UDP 5353) via firewall
* Disables Network Discovery
* Optional Bonjour removal
* Shows verification output

---

## ▶️ How to run

1. **Open PowerShell as Administrator**
2. Paste the script below
3. Press Enter

---

## 🧩 PowerShell Hardening Script

```powershell
Write-Host "=== Windows 11 Network Hardening Script ===" -ForegroundColor Cyan

# -------------------------------
# Disable vulnerable services
# -------------------------------
$services = @(
    "SSDPSRV",     # SSDP Discovery
    "upnphost",    # UPnP Device Host
    "FDResPub",    # Function Discovery Resource Publication
    "FDProvHost"  # Function Discovery Provider Host
)

foreach ($svc in $services) {
    Get-Service -Name $svc -ErrorAction SilentlyContinue | ForEach-Object {
        Write-Host "Disabling service: $($_.Name)"
        Stop-Service $_ -Force -ErrorAction SilentlyContinue
        Set-Service $_ -StartupType Disabled
    }
}

# -------------------------------
# Disable Network Discovery
# -------------------------------
Write-Host "Disabling Network Discovery..."
Set-NetFirewallRule -DisplayGroup "Network Discovery" -Enabled False

# -------------------------------
# Firewall rules for UPnP & mDNS
# -------------------------------
$rules = @(
    @{ Name="Block UPnP SSDP Inbound";  Port=1900; Direction="Inbound"  },
    @{ Name="Block UPnP SSDP Outbound"; Port=1900; Direction="Outbound" },
    @{ Name="Block mDNS Inbound";       Port=5353; Direction="Inbound"  },
    @{ Name="Block mDNS Outbound";      Port=5353; Direction="Outbound" }
)

foreach ($rule in $rules) {
    if (-not (Get-NetFirewallRule -DisplayName $rule.Name -ErrorAction SilentlyContinue)) {
        Write-Host "Creating firewall rule: $($rule.Name)"
        New-NetFirewallRule `
            -DisplayName $rule.Name `
            -Direction $rule.Direction `
            -Protocol UDP `
            -LocalPort $rule.Port `
            -Action Block `
            -Profile Any
    }
}

# -------------------------------
# Disable Bonjour (if present)
# -------------------------------
$bonjour = Get-Service -Name "Bonjour Service" -ErrorAction SilentlyContinue
if ($bonjour) {
    Write-Host "Disabling Bonjour Service"
    Stop-Service "Bonjour Service" -Force
    Set-Service "Bonjour Service" -StartupType Disabled
}

# -------------------------------
# Verification
# -------------------------------
Write-Host "`n=== Verification ===" -ForegroundColor Yellow
netstat -an | findstr ":1900"
netstat -an | findstr ":5353"

Write-Host "`n✔ Hardening completed. Reboot recommended." -ForegroundColor Green
```

---

# 🛡️ Windows 11 Network Hardening Checklist

Use this as a **baseline security checklist**.

---

## 🔴 High Priority (Do These)

✅ Disable UPnP (Windows + Router)
✅ Disable SSDP & Discovery services
✅ Block UDP 1900 and 5353
✅ Turn off Network Discovery
✅ Keep Windows Firewall enabled (all profiles)
✅ Disable unused network adapters (VMs, Bluetooth PAN)

---

## 🟠 Medium Priority (Recommended)

⬜ Disable SMBv1

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

⬜ Set network profile to **Public** (unless domain-joined)
Settings → Network → Your Network → Network Profile → **Public**

⬜ Disable LLMNR

```powershell
reg add "HKLM\Software\Policies\Microsoft\Windows NT\DNSClient" /v EnableMulticast /t REG_DWORD /d 0 /f
```

⬜ Disable NetBIOS over TCP/IP
(Network Adapter → IPv4 → Advanced → WINS → Disable NetBIOS)

---

## 🟡 Advanced / Enterprise Hardening

⬜ Disable WPAD (prevents proxy poisoning)

```powershell
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DisableWpad /t REG_DWORD /d 1 /f
```

⬜ Disable unnecessary inbound firewall rules:

* Remote Assistance
* Media Streaming
* Wireless Display
* Xbox services

⬜ Enable Attack Surface Reduction (Defender)

```powershell
Set-MpPreference -EnableControlledFolderAccess Enabled
```

---

## 🔍 Post-Hardening Validation

Check listening ports:

```powershell
netstat -abno
```

Check discovery traffic:

```powershell
Get-NetUDPEndpoint | Where-Object { $_.LocalPort -in 1900,5353 }
```

Run:

* **Microsoft Defender Offline Scan**
* **Nmap scan from another LAN host** (if possible)

---

## ⚠️ Expected Breakage (Normal)

| Feature                | Status |
| ---------------------- | ------ |
| Auto printer discovery | ❌      |
| Smart TV / Chromecast  | ❌      |
| AirPlay / Bonjour      | ❌      |
| Manual IP printing     | ✅      |
| VPN / Browsing         | ✅      |

---

## ✅ Recommended Security Profile

✔ No multicast discovery
✔ No auto port forwarding
✔ No LAN fingerprinting
✔ Reduced lateral movement risk

---

If you want next:

* 🔹 **Undo / rollback script**
* 🔹 **Laptop vs desktop hardening profile**
* 🔹 **Corporate GPO-based version**
* 🔹 **Router firewall hardening**

Just say the word.

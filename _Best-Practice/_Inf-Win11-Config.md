O&O ShutUp10++ [https://www.oo-software.com/en/shutup10]
Chris Titus's Windows Utility [https://github.com/ChrisTitusTech/winutil]

```powershell
Write-Host "=== Windows 11 Network Hardening Script ===" -ForegroundColor Cyan

# -------------------------------
# Disable vulnerable services
# -------------------------------
$services = @(
    "SSDPSRV",     # SSDP Discovery
    "upnphost",    # UPnP Device Host
    "FDResPub",    # Function Discovery Resource Publication
    "fdPHost"  # Function Discovery Provider Host
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
netstat -abno
Get-NetUDPEndpoint | Where-Object { $_.LocalPort -in 1900,5353 }

Write-Host "`n✔ Hardening completed. Reboot recommended." -ForegroundColor Green


# Window Feature (SMB)
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol

# Disable LLMNR
reg add "HKLM\Software\Policies\Microsoft\Windows NT\DNSClient" /v EnableMulticast /t REG_DWORD /d 0 /f

# Disable WPAD(prevents proxy poisoning)
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DisableWpad /t REG_DWORD /d 1 /f

# Enable Attack Surface Reduction (Defender)
# Set-MpPreference -EnableControlledFolderAccess Enabled

# Gaming Service
get-appxpackage Microsoft.GamingServices | remove-AppxPackage -allusers

```
| Service                                 | Recommendation |
| --------------------------------------- | -------------- |
| Function Discovery Provider Host        | Disable        |
| Function Discovery Resource Publication | Disable        |
| SSDP Discovery                          | Disable        |
| UPnP Device Host                        | Disable        |
```powershell
$services = @(
    @{ Name="Function Discovery Provider Host";        Service="fdPHost" },
    @{ Name="Function Discovery Resource Publication"; Service="FDResPub" },
    @{ Name="SSDP Discovery";                          Service="SSDPSRV" },
    @{ Name="UPnP Device Host";                        Service="upnphost" }
)

$services | ForEach-Object {
    $s = Get-Service -Name $_.Service -ErrorAction SilentlyContinue
    if ($s) {
        [PSCustomObject]@{
            ServiceName  = $_.Name
            Status       = $s.Status
            StartupType  = (Get-CimInstance Win32_Service -Filter "Name='$($_.Service)'").StartMode
            Compliant    = ($s.Status -eq "Stopped" -and
                            (Get-CimInstance Win32_Service -Filter "Name='$($_.Service)'").StartMode -eq "Disabled")
        }
    } else {
        [PSCustomObject]@{
            ServiceName = $_.Name
            Status      = "Not Found"
            StartupType = "N/A"
            Compliant   = "N/A"
        }
    }
} | Format-Table -AutoSize

```

```powershell
# Define the list of service names
$services = @(
    "RemoteRegistry",  # Remote Registry
    "Spooler",         # Print Spooler
    "TermService",     # Remote Desktop Services
    "MapsBroker",      # Downloaded Maps Manager
    "XblAuthManager",  # Xbox Live Auth Manager
    "XblGameSave",     # Xbox Live Game Save
    "XboxNetApiSvc",   # Xbox Live Networking Service
    "XboxGipSvc"       # Xbox Accessory Management Service
)

foreach ($service in $services) {
    if (Get-Service -Name $service -ErrorAction SilentlyContinue) {
        Write-Host "Disabling $service..." -ForegroundColor Cyan
        Stop-Service -Name $service -Force -ErrorAction SilentlyContinue
        Set-Service -Name $service -StartupType Disabled
    } else {
        Write-Host "Service $service not found, skipping." -ForegroundColor Yellow
    }
}

$services = @(
    @{ Name="Function Discovery Provider Host";        Service="fdPHost" },
    @{ Name="Function Discovery Resource Publication"; Service="FDResPub" },
    @{ Name="SSDP Discovery";                          Service="SSDPSRV" },
    @{ Name="UPnP Device Host";                        Service="upnphost" }
)
$servicesToVerify = @(
    "RemoteRegistry", "Spooler", "TermService", 
    "MapsBroker", "XblAuthManager", "XblGameSave", 
    "XboxNetApiSvc", "XboxGipSvc", "fdPHost", "FDResPub", "SSDPSRV", "upnphost"
)
Get-Service -Name $servicesToVerify | Select-Object Name, Status, StartType | Format-Table -AutoSize
```

## 4️⃣ Verify They Are Disabled

### Check listening ports:

```powershell
netstat -an | findstr ":1900"
netstat -an | findstr ":5353"
```

uPnP

- Services
	-UPnP Device
	- SSDP Disscovery
- Firewall
	- UDP:1900

# mDNS

- Setting > Network > advanced network setting
	- Network Discvoery
	- File and printer sharing

- Sevice (Bonjour)
- Firewall
	- UDP:5353

Note: For a deeper "system-wide" disable, you can use the Registry Editor to navigate to HKLM\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters and create a DWORD named EnableMDNS set to 0.
# Init
- devmgmt
    - wifi
    - bt
    - microphone
- sign-in option (pwd > PIN)

# Configuration
### Security
- Device (stack protection)
```powershell
# Enable Attack Surface Reduction (Defender)
Set-MpPreference -EnableControlledFolderAccess Enabled
```

### Netowrk
- TODO: turn of netBIOS over TCP
```powershell
### ARP Spoofing Prevention
# * **Static ARP:** If your router’s IP never changes, bind it manually.
# * Command: `arp -s <Router_IP> <Router_MAC>`
# * *Best Practice:* Create a `.bat` file in your Startup folder for this, as it clears on reboot.

# ### DHCP Hardening
# * **Prevent Rogue DHCP:** If you suspect someone is running a rogue server, set a **Static IP** on your Windows NIC. This prevents your PC from even asking the network for a lease, ignoring any malicious DHCP offers.

# 1. Define the DNS servers and the DoH template
$primaryDNS = "1.1.1.1"
$secondaryDNS = "1.0.0.1"
$dohTemplate = "https://cloudflare-dns.com/dns-query"
# $secondaryDNS = "8.8.8.8"
# $dohTemplate = "https://dns.google/dns-query"

# 2. Get the active network adapter (Ethernet or Wi-Fi)
# $adapter = Get-NetAdapter | Where-Object { $_.Status -eq "Up" } | Select-Object -First 1
# TODO: UI select
$adapter = Get-NetAdapter | Select-Object -First 1

if ($adapter) {
    Write-Host "Configuring DoH on: $($adapter.Name)" -ForegroundColor Cyan

    # 3. Set the DNS Server addresses
    Set-DnsClientServerAddress -InterfaceIndex $adapter.InterfaceIndex -ServerAddresses ($primaryDNS, $secondaryDNS)

    # 4. Map the IP addresses to the DoH template in Windows
    Add-DnsClientDohServerAddress -ServerAddress $primaryDNS -DohTemplate $dohTemplate -AllowFallbackToUdp $false
    Add-DnsClientDohServerAddress -ServerAddress $secondaryDNS -DohTemplate $dohTemplate -AllowFallbackToUdp $false

    Write-Host "DNS set to Cloudflare with Encrypted Only (DoH) mode." -ForegroundColor Green
} else {
    Write-Host "No active network adapter found." -ForegroundColor Red
}
```

### Firewall
```powershell
# TODO: Block all inbound
# Log
# Multicast

# Ports
$blockPorts = @(135, 445, 5040, 1900, 5353)

foreach ($port in $blockPorts) {
    Write-Host "Creating Firewall Rule to block Port $port..." -ForegroundColor Yellow
    New-NetFirewallRule -DisplayName "In - Block Port $port" `
                        -Direction Inbound `
                        -LocalPort $port `
                        -Protocol TCP `
                        -Action Block `
                        -Profile Any
    New-NetFirewallRule -DisplayName "Out - Block Port $port" `
                        -Direction Outbound `
                        -LocalPort $port `
                        -Protocol TCP `
                        -Action Block `
                        -Profile Any                        
}

# Network Discovery
Set-NetFirewallRule -DisplayGroup "Network Discovery" -Enabled False

# UPnP & mDNS
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
```

### Simplewall
- setting start up
- enable filter
- disable notification
- profile
    - DoH
    - Block-UDP

```powershell
wuauserv, wlidsvc, cryptsvc
'mousocoreworker.exe' 'taskhostw.exe', 'SmartScreen', 'mpcmdrun.exe'
```
| Component                   | Action   | Reason                                                                     |
| --------------------------- | -------- | -------------------------------------------------------------------------- |
| Windows Update (wuauserv)   | Allow    | Essential for security patches.                                            |
| Microsoft Account (wlidsvc) | Allow    | If you use a Microsoft Login/OneDrive.                                     |
| Feature Experience Pack     | Allow    | Keeps your UI and Windows apps bug-free.                                   |
| svchost.exe (Generic)       | Restrict | If no specific service is listed, only allow to known Microsoft IP ranges. |

### Edge
- disable background process
- (VPN?)
```powershell
# 1. Open Edge and type `edge://flags` in the address bar.
# 2. Search for: **Anonymize local IPs exposed by WebRTC**.
# 3. Set it to **Disabled**.
# 4. Restart the browser.
# *Note: This specific flag is often what triggers the "mDNS-In" firewall request when the browser tries to hide your local IP for privacy during video calls.*

# This will be done through HKEY
```


# Unnecessary
Advanced System Setting > [device, remote]
### Apps
```powershell
#TODO
```
### Service
```powershell
# Gaming Service
get-appxpackage Microsoft.GamingServices | remove-AppxPackage -allusers
# TODO: Check any other unnecessary service
# SMSrouter
$services = @(
    "RemoteRegistry",          # Remote access to registry (High Security Risk)
    "TermService",             # Remote Desktop Services (Attack Vector)
    "DiagTrack",               # Connected User Experiences (Telemetry/Privacy)
    "dmwappushservice",        # WAP Push Message Routing (Telemetry)
    "MapsBroker",              # Downloaded Maps Manager (Unnecessary)
    "AllJoynRouter",           # IoT Device Discovery (Unnecessary)
    "Fax",                     # Fax Services (Obsolete)
    "XblAuthManager",          # Xbox Live Auth (If not gaming)
    "XblGameSave",             # Xbox Game Save (If not gaming)
    "XboxNetApiSvc",   # Xbox Live Networking Service
    "XboxGipSvc",       # Xbox Accessory Management Service
    "Spooler",     # Print Spooler
    "SSDPSRV",     # SSDP Discovery
    "upnphost",    # UPnP Device Host
    "FDResPub",    # Function Discovery Resource Publication
    "fdPHost",  # Function Discovery Provider Host
    "Bonjour Service"
)

foreach ($service in $services) {
    if (Get-Service $service -ErrorAction SilentlyContinue) {
        Write-Host "Disabling $service..." -ForegroundColor Yellow
        Set-Service -Name $service -StartupType Disabled -Confirm:$false
        Stop-Service -Name $service -ErrorAction SilentlyContinue
    } else {
        Write-Host "Service $service not found, skipping." -ForegroundColor Gray
    }
}

Get-Service -Name $services | Select-Object Name, Status, StartType | Format-Table -AutoSize
```

# Windows Optional Feature
```powershell
# TODO: GUI [Windows Feature, System > Optional Feature]
# TODO: check unnecessary features
# Telnet Client, SMB (NTLM Relaying)
$features = @(
    "SMB1Protocol",                 # SMB 1.0/CIFS (WannaCry vulnerability)
    "Internet-Explorer-Optional-amd64", # IE11 Engine
    "MicrosoftWindowsPowerShellV2", # PowerShell 2.0 (Lacks modern logging)
    "WorkFolders-Client",           # Work Folders Client
    "TelnetClient"
)

foreach ($feature in $features) {
    Write-Host "Checking status of $feature..." -ForegroundColor Cyan
    $check = Get-WindowsOptionalFeature -Online -FeatureName $feature
    123456
    if ($check.State -eq "Enabled") {
        Write-Host "Disabling $feature..." -ForegroundColor Yellow
        Disable-WindowsOptionalFeature -Online -FeatureName $feature -NoRestart
    } else {
        Write-Host "$feature is already disabled." -ForegroundColor Gray
    }
}
```

# Registry
```powershell
# Create func, [create path, configure, valid]
# TODO: Disable WPAD (prevents proxy poisoning) (check two differernt ways)
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DisableWpad /t REG_DWORD /d 1 /f
$wpadPath = "HKLM:\Software\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp"
# Set-ItemProperty -Path $wpadPath -Name "DisableWpad" -Value 1 -Type DWord
Get-ItemProperty -Path $wpadPath

#TODO: WiFi-Direct
# * **Device Manager:** Disable "Microsoft Wi-Fi Direct Virtual Adapter."
# * **Registry:** `HKLM\Software\Policies\Microsoft\Windows\WCN\Registrars` -> Set `EnableRegistrars` to `0`.
$wifiDirectPath = "HKLM:\Software\Policies\Microsoft\Windows\WCN\Registrars"
Set-ItemProperty -Path $wifiDirectPath -Name "EnableRegistrars" -Value 0 -Type DWord
Get-ItemProperty -Path $wifiDirectPath

# SMB Signing (Prevent NTLM Relaying)
$path = "HKLM:\System\CurrentControlSet\Services\LanmanWorkstation\Parameters"
Set-ItemProperty -Path $path -Name "EnableSecuritySignature" -Value 1 -Type DWord
Set-ItemProperty -Path $path -Name "RequireSecuritySignature" -Value 1 -Type DWord
# Valid
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Services\LanmanWorkstation\Parameters" | Select-Object EnableSecuritySignature, RequireSecuritySignature

# Disable mDNS (Multicast DNS) in the Windows DNS Client
# This prevents Windows from responding to .local name queries
$mDNSPath = "HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters"
Set-ItemProperty -Path $mDNSPath -Name "EnableMDNS" -Value 0 -Type DWord
Get-ItemProperty -Path $mDNSPath

# Disable LLMNR (Link-Local Multicast Name Resolution) via Registry
$dnscachePath = "HKLM:\Software\Policies\Microsoft\Windows NT\DNSClient"
if (-not (Test-Path $dnscachePath)) { New-Item -Path $dnscachePath -Force }
Set-ItemProperty -Path $dnscachePath -Name "EnableMulticast" -Value 0 -Type DWord
Set-ItemProperty -Path $dnscachePath -Name "EnableLLMNR" -Value 0 -Type DWord
Get-ItemProperty -Path $dnscachePath

# Disable NetBIOS over TCP/IP (Reduces network footprint)
# This prevents your PC from using an old protocol to broadcast its name on the network.
$networkKeys = Get-ChildItem "HKLM:\SYSTEM\CurrentControlSet\Services\NetBT\Parameters\Interfaces"
foreach($key in $networkKeys) {
    Set-ItemProperty -Path "HKLM:\$($key.Name)" -Name "NetbiosOptions" -Value 2 -Type DWord
    Get-ItemProperty -Path "HKLM:\$($key.Name)"
}
# 1. Hard-disable Telemetry (Data Collection)
$telemetryPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows\DataCollection"
if (-not (Test-Path $telemetryPath)) { New-Item -Path $telemetryPath -Force }
Set-ItemProperty -Path $telemetryPath -Name "AllowTelemetry" -Value 0 -Type DWord
Get-ItemProperty -Path $telemetryPath

# 2. Disable Bing Search in Start Menu
$searchPath = "HKCU:\Software\Microsoft\Windows\CurrentVersion\Search"
if (-not (Test-Path $searchPath)) { New-Item -Path $searchPath -Force }
Set-ItemProperty -Path $searchPath -Name "BingSearchEnabled" -Value 0 -Type DWord
Get-ItemProperty -Path $searchPath

# 3. Prevent remote access via accounts with blank passwords
$lsaPath = "HKLM:\SYSTEM\CurrentControlSet\Control\Lsa"
Set-ItemProperty -Path $lsaPath -Name "LimitBlankPasswordUse" -Value 1 -Type DWord
Get-ItemProperty -Path $lsaPath

# Disable AutoRun/AutoPlay on all drives
$autoRunPath = "HKCU:\Software\Microsoft\Windows\CurrentVersion\Policies\Explorer"
if (-not (Test-Path $autoRunPath)) { New-Item -Path $autoRunPath -Force }
Set-ItemProperty -Path $autoRunPath -Name "NoDriveTypeAutoRun" -Value 255 -Type DWord
Get-ItemProperty -Path $autoRunPath

# Enforce Microsoft Edge "Strict Security Mode"
# $edgePath = "HKLM:\SOFTWARE\Policies\Microsoft\Edge"
# if (-not (Test-Path $edgePath)) { New-Item -Path $edgePath -Force }
# # 1 = Basic, 2 = Balanced, 3 = Strict
# Set-ItemProperty -Path $edgePath -Name "EnhanceSecurityMode" -Value 3 -Type DWord

## Disable the Media Router in Edge
# 1. Navigate to:
# `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Edge`
# *(If the "Edge" folder doesn't exist, right-click "Microsoft" > New > Key and name it Edge)*.
# 2. Right-click "Edge" and select **New > DWORD (32-bit) Value**.
# 3. Name it **EnableMediaRouter**.
# 4. Set the **Value data** to **0**.
$edgeRouterPath = "HKLM:\SOFTWARE\Policies\Microsoft\Edge"
if (-not (Test-Path $edgeRouterPath)) { New-Item -Path $edgeRouterPath -Force }
Set-ItemProperty -Path $edgeRouterPath -Name "EnableMediaRouter" -Value 0 -Type DWord
Get-ItemProperty -Path $edgeRouterPath -Name "EnableMediaRouter"

# 1. Define the path and value
$registryPath = "HKLM:\SOFTWARE\Policies\Google\Chrome"
$name = "EnableMediaRouter"
$value = 0
# 2. Create the path if it doesn't exist
if (!(Test-Path $registryPath)) {
    New-Item -Path $registryPath -Force | Out-Null
    Write-Host "Created Registry path: $registryPath" -ForegroundColor Cyan
}
# 3. Set the DWORD value to 0
Set-ItemProperty -Path $registryPath -Name $name -Value $value -Type DWord
```

# MISC
```powershell
# TODO
# Settings > Windows Update > Advanced options > Delivery Optimization.
# No auto port forwarding
# No LAN fingerprinting
```

# RESTART

# Monitor
### Integrity

# Check Listening Port
```powershell

# TODO
# Write-Host "`n=== Verification ===" -ForegroundColor Yellow
# netstat -an | findstr ":1900"
# netstat -an | findstr ":5353"
# netstat -abno
# Get-NetUDPEndpoint | Where-Object { $_.LocalPort -in 1900,5353 }

# Write-Host "`n✔ Hardening completed. Reboot recommended." -ForegroundColor Green

Write-Host "--- Scanning for Listening Network Ports ---" -ForegroundColor Green
Write-Host "Legend: 0.0.0.0 or [::] means the port is open to the WHOLE NETWORK." -ForegroundColor Yellow
Write-Host "        127.0.0.1 means it is only open to YOUR computer (Safe)." -ForegroundColor Gray
Write-Host ""

# Get all active connections that are in a 'Listening' state
$connections = Get-NetTCPConnection -State Listen | Select-Object LocalAddress, LocalPort, OwningProcess

$results = foreach ($c in $connections) {
    $processName = (Get-Process -Id $c.OwningProcess -ErrorAction SilentlyContinue).Name
    [PSCustomObject]@{
        Port        = $c.LocalPort
        Address     = $c.LocalAddress
        ProcessName = $processName
        ProcessID   = $c.OwningProcess
        Security    = if ($c.LocalAddress -eq '0.0.0.0' -or $c.LocalAddress -eq '::') {"EXTERNAL"} else {"INTERNAL"}
    }
}

$results | Sort-Object Security, Port | Format-Table -AutoSize

Get-NetUDPEndpoint
```

# Integrity
```powershell
# 1. Define the folders to monitor (Common targets for malware)
$pathsToMonitor = @(
    "$env:SystemRoot\System32\drivers\etc\hosts", # Redirects websites
    "$env:SystemRoot\System32\user32.dll",       # Core UI component
    "$env:SystemRoot\System32\kernel32.dll"      # Core OS component
)

$baselineFile = "$env:USERPROFILE\Desktop\sys_baseline.csv"

# 2. Function to create or check the baseline
function Check-Integrity {
    if (-not (Test-Path $baselineFile)) {
        Write-Host "No baseline found. Creating one now..." -ForegroundColor Cyan
        Get-FileHash -Path $pathsToMonitor | Export-Csv -Path $baselineFile -NoTypeInformation
        Write-Host "Baseline saved to your Desktop." -ForegroundColor Green
    } else {
        Write-Host "Comparing current files against baseline..." -ForegroundColor Cyan
        $baseline = Import-Csv $baselineFile
        foreach ($file in $baseline) {
            $currentHash = (Get-FileHash -Path $file.Path).Hash
            if ($currentHash -ne $file.Hash) {
                Write-Host "🚨 ALERT: File Changed! $($file.Path)" -ForegroundColor Red
            } else {
                Write-Host "✅ Verified: $($file.Path)" -ForegroundColor Gray
            }
        }
    }
}
```
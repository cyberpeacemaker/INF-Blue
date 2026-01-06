- ip, eth
- src, dst, addd

```powershell 
Get-ChildItem env: | Sort-Object -Property Name
setx SSLKEYLOGFILE "%USERPROFILE%\sslkeys.log"
Get-ChildItem env:SSLKEYLOGFILE
```
---

(Pre)-Master-Secret log filename: /path/to/ssl-key.log


```
chromium --ssl-key-log-file=~/ssl-key.log
"C:\Program Files\Google\Chrome\Application\chrome.exe" --ssl-key-log-file="C:\path\to\ssl-key.log"
"C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" --ssl-key-log-file="%USERPROFILE%\sslkeys.log"


```

---

First, after right-clicking anywhere, choose “Protocol Preferences.” From the submenu, select “Transport Layer Security.” Thirdly, click on “Open Transport Layer Security preferences.”

# TLS decrypt

1. per-session secrets && star
<!-- setx SSLKEYLOGFILE "%USERPROFILE%\sslkeys.log"
$env:SSLKEYLOGFILE = "%USERPROFILE%\sslkeys.log" -->
setx SSLKEYLOGFILE "C:\sslkeys.log"
$env:SSLKEYLOGFILE = "C:\sslkeys.log"

Start-Process "C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe"
Start-Process "C:\Program Files\Google\Chrome\Application\chrome.exe"

2. Edit > Preferences > Protocols > TLS
Set “(Pre)-Master-Secret log filename” (older Wireshark call this “RSA keys list” separately) to the path of sslkeys.log.

# Example steps summary (Windows, Edge)

1. Close Edge.
2. In PowerShell: `setx SSLKEYLOGFILE "%USERPROFILE%\sslkeys.log"`
3. Start Edge, reproduce the interaction you want to capture.
4. In Wireshark: set TLS `(Pre)-Master-Secret log filename` to `%USERPROFILE%\sslkeys.log`.
5. Capture traffic (or open pcap). Decrypted TLS application data should appear.

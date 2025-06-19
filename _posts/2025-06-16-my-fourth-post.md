---
layout: post
title:  "Powershell For Checking Malware Persistence (mega script)"
date:   2025-06-16 21:00:00 +1000
categories: project
---

### Overview
(https://github.com/lvl0socanalyst/Scripts/blob/main/malware_persistence_checker.ps1)

Script that checks for common malware persistence vectors.

Useful when performing analysis on a potentially compromised PC that has been disconnected from the internet. 

Rather than post the whole script I'll just run over and explain the functions/how to use it. 

I'll slowly add function by function. But I'll start with reg keys. Couldn't you just use autoruns? YES. But that's not fun.

Potentially suspicious findings will be dumped in CLI. All results will be whacked into a .txt file
- Suspicious findings are triggered via regex pattern matching (old school but effective)

Script gives the following options:
1. Scan Registry Persistence Keys
2. Scan Service Keys
3. Scan Scheduled Tasks
2. Exit

### Pattern matching indicators


### Registry Key Checker

Checks the follow reg paths
- HKCU:\Software\Microsoft\Windows\CurrentVersion\Run
- HKCU:\Software\Microsoft\Windows\CurrentVersion\RunOnce
- HKLM:\Software\Microsoft\Windows\CurrentVersion\Run
- HKLM:\Software\Microsoft\Windows\CurrentVersion\RunOnce
- HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options
- HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
- HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components
- HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows
- HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify

### Services


Checks HKLM:\SYSTEM\CurrentControlSet\Services

### Scheduled Tasks
Scans all scheduled tasks that don't have \Microsoft\ in the path. 

The follow pattern matching is then applied ```AppData|Temp|\.ps1|\.bat|\.vbs|http[s]?://|[A-Za-z0-9+/]{100}``` to the scheduled tasks. If any match they are flagged as suspicious and written to CLI.


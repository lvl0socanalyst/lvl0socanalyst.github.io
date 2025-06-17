---
layout: post
title:  "Script for checking windows persistence mechanisms"
date:   2025-06-16 21:00:00 +1000
categories: scripts
---

Script that checks for common malware persistence vectors.
Useful when performing analysis on a potentially compromised PC that has been disconnected from the internet.
I think outputting to a .txt file is probably the way to go. I'll included sign files as well. Because well, you never know.
I'll slowly add function by function. But I'll start with reg keys. 
Couldn't you just use autoruns? YES. But that's not fun.
--- 

<pre> ```powershell $hostname = $env:COMPUTERNAME
$logFile = "$PSScriptRoot\persistence_log${hostname}.txt"

function Write-Log {
    param (
        [string]$text,
        [switch]$IsSuspicious
    )
    Add-Content -Path $logFile -Value $text

    if ($IsSuspicious) {
        Write-Host $text
    }
}

function Show-Menu {
    Write-Host "`n=== Windows Persistence Checker ==="
    Write-Host "1. Scan Registry Persistence Keys (Show suspicious only)"
    Write-Host "2. Exit"
}

function Is-SuspiciousPath {
    param ([string]$value)

    if (-not $value) { return $false }

    $indicators = @(
        "AppData", "Temp", "Roaming", "LocalLow", "Public", "Users\\Public",
        "\.vbs", "\.js", "\.bat", "\.cmd", "\.ps1", "\.scr",
        "wscript.exe", "cscript.exe", "powershell.exe", "mshta.exe", "rundll32.exe", "cmd.exe",
        "System32\\Tasks", "Microsoft\\Windows\\CurrentVersion\\Explorer\\StartupApproved",
        "curl.exe", "wget.exe",
        "base64", "Invoke-", "DownloadString", "EncodedCommand"
    )

    foreach ($indicator in $indicators) {
        if ($value -match [regex]::Escape($indicator)) {
            return $true
        }
    }
    return $false
}

function Scan-RegistryKeys {
    Write-Log "`n--- Scanning Registry Persistence Keys ---"

    $regPaths = @(
        "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run",
        "HKCU:\Software\Microsoft\Windows\CurrentVersion\RunOnce",
        "HKLM:\Software\Microsoft\Windows\CurrentVersion\Run",
        "HKLM:\Software\Microsoft\Windows\CurrentVersion\RunOnce",
        "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options",
        "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon",
        "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components",
        "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows",
        "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify"
    )

    foreach ($path in $regPaths) {
        Write-Log "`n [Checking]: $path"

        try {
            if (Test-Path $path) {
                $items = Get-ItemProperty -Path $path -ErrorAction Stop
                $props = $items.PSObject.Properties | Where-Object { $_.Name -ne "PSPath" -and $_.Value }

                if ($props) {
                    foreach ($prop in $props) {
                        $isSuspicious = Is-SuspiciousPath $prop.Value
                        $logEntry = " -> $($prop.Name): $($prop.Value)"
                        if ($isSuspicious) {
                            Write-Log " [!!] Suspicious -> $($prop.Name): $($prop.Value)" -IsSuspicious
                        } else {
                            Add-Content -Path $logFile -Value $logEntry
                        }
                    }
                } else {
                    Write-Log " [Empty]"
                }
            } else {
                Write-Log " [Path Not Found]"
            }
        } catch {
            Write-Log " [Access Denied or Error]: $_"
        }
    }
    Write-Log "`nResults saved to $logFile"
}

function Scan-ServicesRegistry {
    Write-Log "`n--- Scanning Services Registry ---"
    $servicesPath = "HKLM:\SYSTEM\CurrentControlSet\Services"

    try {
        Get-ChildItem $servicesPath | ForEach-Object {
            $serviceName = $_.PSChildName
            $imagePath = (Get-ItemProperty -Path $_.PSPath -ErrorAction SilentlyContinue).ImagePath

            if ($imagePath) {
                $isSuspicious = Is-SuspiciousPath $imagePath
                $logEntry = " -> ${serviceName}: ${imagePath}"
                if ($isSuspicious) {
                    Write-Log " [!!] Suspicious -> ${serviceName}: ${imagePath}" -IsSuspicious
                } else {
                    Add-Content -Path $logFile -Value $logEntry
                }
            }
        }
    } catch {
        Write-Log " [Error accessing Services key]: $_"
    }
}

do {
    Show-Menu
    $choice = Read-Host "Enter selection"

    switch ($choice) {
        "1" {
            Scan-RegistryKeys
            Scan-ServicesRegistry
            Write-Host "--- Review full output in txt ---"
            Pause
        }
        "2" {
            Write-Host "Exiting..."
            break
        }
        default {
            Write-Host "Invalid selection. Try again."
            Pause
        }
    }
} while ($true)
 ``` </pre>

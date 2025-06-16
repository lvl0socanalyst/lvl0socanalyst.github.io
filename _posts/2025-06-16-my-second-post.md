---
layout: post
title:  "Useful Powershell For IR/Threat Hunting"
date:   2025-06-16 21:00:00 +1000
categories: scripts
---

Bunch of Powershell commands I have spun up. Central repo for all of them. Will likely keep updating this.

---

```powershell
# Retrieves SHA-256 hashes from a directory
$dir = Read-Host "dir path: "

Get-ChildItem -Path $dir -File -Recurse | ForEach-Object {
    $hashie = Get-FileHash $_.FullName -Algorithm SHA256
    [PSCustomObject]@{
        FileName = $_.Name
        Hash     = $hashie.Hash
    }
} | Format-Table -AutoSize

# Files owned by a user
$user = Read-Host "Enter the username to search for (e.g., MYPC\walter)"
$path = Read-Host "Enter the directory to scan (e.g., C:\Windows)"
$outputPath = Read-Host "Enter txt file name"

Write-Host "`nScanning files in $path owned by '$user'..." -ForegroundColor Cyan

$matches = @()

Get-ChildItem -Path $path -Recurse -Force -ErrorAction SilentlyContinue |
ForEach-Object {
    try {
        $owner = (Get-Acl $_.FullName).Owner
        if ($owner -eq $user) {
            $matches += $_.FullName
        }
    } catch {
    }
}

if ($matches.Count -eq 0) {
    "No files found owned by '$user'." | Out-File -FilePath $outputPath
    Write-Host "`nNo files found. Output written to: $outputPath" -ForegroundColor Yellow
} else {
    $matches | Out-File -FilePath $outputPath -Encoding UTF8
    Write-Host "`nFound $($matches.Count) files. Output written to: $outputPath" -ForegroundColor Green
}



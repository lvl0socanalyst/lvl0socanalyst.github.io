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

How is it possible?

A Background Service Worker is an event driven js file that runs in the background.

For most C2 extensions. Background.js is what makes it possible. What Chrome Extension APIs can it interact with?
- Chrome.tabs (query, update tabs)
- Chrome.cookies (read/write cookies)
- Chrome.scripting (inject scripts)
- Chrome.alarms (schedule tasks)

Service workers are used to send messages to 'Content Scripts'. Content Scripts are the squires recieving and relaying information.

Serice Workers inject the content scripts into the tabs when an event occurs.

What can a 'Content Script' do?
- Manipulate the DOM
- Interact with page's JS
- Send and receive messages from the background service worker.

Goals
1. Create an extension that parses webpages into an epic dark mode
2. Exfils local and session tokens, cookies and takes screenshots

So after about 20 minutes of LLM and chill I've got an extension that toggles DaRk MoDe for a page

Now that the extension has a functional purpose. Lets add some dodgy stuff.

Dodgy Goals
- Screenshot webpage
- Grabs session and local tokens
- Steal cookies

I've whacked up a flask server locally to test the extension

I've managed to get the extension to start exfilling data pretty nicely. 
- Cookies are sent every 2 minutes
- Local and session tokens are sent every 2 minutes
- Screenshots everytime the toggle button is pressed

![Test](https://upload.wikimedia.org/wikipedia/commons/thumb/4/47/PNG_transparency_demonstration_1.png/200px-PNG_transparency_demonstration_1.png)

-----Process-----
1. User Toggles DaRk MoDe
2. DaRk MoDe starts (epic)
3. Screenshot is sent to Flask server
4. Every 2 minutes background.js invokes the functions that capture the cookies and tokens and sent to Flask server.


### Phase 2: Make it more malicious

### Final Phase: Receive commands from Flask server
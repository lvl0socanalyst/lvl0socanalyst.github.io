---
layout: post
title:  "InfoStealer Browser Extension"
date:   2025-06-16 21:00:00 +1000
categories: project
---

<style>
  body { font-size: 13px; }
  h1 { font-size: 19px !important; }
  h2 { font-size: 17px !important; }
  h3 { font-size: 15px !important; }
</style>

## Overview

The real operating system is your browser. EDR's lack telemetry into browser activity. They are unable to inspect at the DOM level.

*What EDRs can see (Windows)*
- DNS requests from Chrome
- Browser Extensions that are loaded
- History and sites visited

*What they can't see (Windows)*
- Document Object Model (DOM) modifications
- JS execution
- Cookies and tokens
- Messaging between service worker and content script(s)

Extensions essentially have free reign to run whatever JS they want to inside your browser.

There are some writeups on polymorphic extensions that can mimick other installed extensions like 1P to obtain creds. 

Most industry leading EDRs offer options for auditing browser extensions but provide no proactive solution for blocking these extensions from loading.
- If an extension exfiled data you would essentially have no telemetry into what was exfiled with an EDR
- Extensions can steal user tokens and cookies. Therefore, hypothetically able to bypass MFA requirements for logins

-----Solution-----
- GPO (Whitelist Approved Extensions) (Legacy solution)

Without GPO. You are reliant on your trusty friends at Google and Microsoft to audit every extension installed onto the store. All it takes is a quick google to realise this isn't happening.

### Phase 1: InfoStealer (Static)

[-----DaRk MoDe (Epic Chrome Extension)-----](https://github.com/lvl0socanalyst/Chrome-Extension/tree/main/DaRk%20MoDe)

-----How is it possible?-----

Well essentially as soon as you install a browser extension you give it full permissions to stuff with anything and everything html, css and js. Which is everything you do in your browser. Depending on what browser is used there are APIs that are called that give the extensions the ability to interact with your browser. 

Common format of an extension directory
```\DaRk MoDe```
  ```manifest.json```
  ```background.js```
  ```content.js```
  ```popup.html```
  ```popup.js```

A Background Service Worker (background.js) is an event driven js file that runs in the background.

*What Chrome Extension APIs can service workers interact with?*
- Chrome.tabs (query, update tabs)
- Chrome.cookies (read/write cookies)
- Chrome.scripting (inject scripts)
- Chrome.alarms (schedule tasks)

Service workers are used to send messages to 'Content Scripts'. Content Scripts are the squires recieving and relaying information.

Serice Workers inject the content scripts into the tabs when an event occurs.

*What can a 'Content Script' do?*
- Manipulate the DOM
- Interact with webpage's JS
- Send and receive messages from the background service worker.

-----Goals-----
1. Create an extension that parses webpages into an epic dark mode
2. Exfils local and session tokens, cookies and takes screenshots

So after about 20 minutes of LLM and chill I've got an extension that toggles DaRk MoDe for a page

Now that the extension has a functional purpose. Lets add some dodgy stuff.

-----Dodgy Goals-----
- Screenshot webpage
- Grabs session and local tokens
- Steal cookies

I'll test the extension locally. To do this I've setup a flask server using python

How data is exfiled
- Cookies are sent every 2 minutes
- Local and session tokens are sent every 2 minutes
- Screenshots everytime the toggle button is pressed

![Flask Output](/images/flask_output.PNG)

-----Process-----
1. User Toggles DaRk MoDe
2. DaRk MoDe starts (epic)
3. Screenshot is sent to Flask server
4. Every 2 minutes background.js invokes the functions that capture the cookies and tokens and sent to Flask server.

-----Ramblings-----

As I was programming this it struck me how dangerous these extensions can be. Previously I've programmed keyloggers that aimed to capture Chrome and Edge browsing activity. It was great and captured sites visited, keystrokes and the lot. However, it didn't have the capability to interact with a page or invoke an action when a certain condition inside the browser occurred. The capability for extensions to be targetted for specific purposes is crazy. I can create a condition that triggers the service worker to call a content script when facebook.com is visited, to redirect the http request to my malicious facebook login page. Furthermore, I could do this for O365 logins as well. Who doesn't love conditional statements? I have a feeling at the end of this project I'm going to be sick of them.

For the next phase I'm going to add a list of conditions to exfil credential data when Facebook is visited. I can choose a field for the content script to look out for on a certain web page and steal that value when a HTTP POST request is invoked.

### Phase 2: If Facebook is visited. Steal my username and password DaRk MoDe



### Phase 3: XMRig option
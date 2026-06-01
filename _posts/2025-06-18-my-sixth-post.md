---
layout: post
title:  "Tails and Whonix for TOR"
date:   2025-06-16 21:00:00 +1000
categories: privacy
---

### Overview 
How to access TOR safely using Tails or not so safely using a Whonix VM

-----Security Discussion Points-----
1. VPN + TOR
- Introduces another point of failure. VPN provider can still see your IP address.
2. TOR + VPN 
- Again, introduces another point of failure. VPN provider can see you are connecting from a TOR node.
3. Exit Node Exposure
- Compromised exit node. Can see/modify HTTP traffic. 
- Important to enforce HTTPs everywhere. This is automatically configured for TOR on Tails and Whonix.
4. Persistent storage
- If you are using persistent storage for Tails. It is in theory recovery through forensics.
- Both Whonix and Tails use LUKS to encrypt persistent storage partitions.
5. Don't open any files in either Tails or Whonix
- PDFs have had CVEs for RCE in the past 
7. For the love of god. If a page offers free btc
- Do not enable JS

### Tails Setup (USB Boot) (Windows)
-----Considerations-----
- Most secure method for accessing TOR
- Live system. Therefore, no data is stored locally
- Secure boot
- AppArmor and Firejail sandboxes applications ran in Tails
- Built in signature checkers, PGP tools etc
- ISP can see you are using Tor

-----Additional Security Measures-----
- Boot tails on a laptop that has been wiped. Secure erase is ideal, timely but effective
- Connect to public wifi
- Do not connect through USB tethering or a mobile hotspot
- Don't use a VPN
- ProxyChains for the ultra paranoid
- Set timezone and screen resolution different each time you use tor.

-----Downloads-----

Tails img
- https://mirrors.wikimedia.org/tails/stable/tails-amd64-6.16/tails-amd64-6.16.img

Rufus
- https://github.com/pbatard/rufus/releases/download/v4.9/rufus-4.9.exe

-----Requirements-----
- USB greater than 8GB
- A brain

-----Instructions-----
1. Download Tails and Rufus - Verify PGP signatures
2. Open Rufus.exe
3. Select USB device you want to format as a bootable for tails
4. Select the tails .img
5. Hit the start button and wait for formatting to complete
6. Unplug the USB and plug it into your Windows laptop
7. Turn on laptop and open boot menu (different for each motherboard)
8. Select the USB device as the primary boot device. Save and Exit
9. Laptop will boot Tails
10. Connect to the WiFi through the top right
11. You are all connected and ready for Tor

-----Ramblings-----
- Tails doesn't protect against compromised firmware. If your machine has a backdoored BIOS through supply chain tampering or other vectors its no good.
- Whilst browsing TOR traditional OPSec is more important than ever. You can't logon to any personal accounts, upload images with EXIF and reuse passwords
- Compromised entry/exit nodes. Tor users are being backtraced through browser fingerprinting and behavior analytics. https://css.csail.mit.edu/6.858/2023/readings/tor-traffic-analysis.pdf 

-----Further Ramblings-----

I read a book called 'the art of invisibility' many years ago that kick started my interest in anonymity. 
You'll always be traceable on the internet. 
Connecting to public wifi? Security cameras? How did you get there? Shoulder surfing?
My point is there is always a way you are traceable. And everyone slips up eventually.

### Whonix VM Setup (VirtualBox)
-----Considerations-----
- If your host PC is compromised - Whonix is
- VM escape vulnerabilities
- Misconfiguration of VM issues
- Recoverable material on disk (If BitLocker password for host is known)

-----Additional Security Measures-----
- Encrypt Host OS using VeraCrypt
- Never pause/save VM state always power off
- Don't install any distro packages in Whonix
- Ensure Tor secruity settings are set to safest (e.g. noscript, https everywhere)

-----Downloads-----

VirtualBox
- https://download.virtualbox.org/virtualbox/7.1.10/VirtualBox-7.1.10-169112-Win.exe

Whonix ova
- https://www.whonix.org/download/ova/17.3.9.9/Whonix-Xfce-17.3.9.9.Intel_AMD64.ova

-----Requirements-----
- Computer not a brick 
- A brain

-----Instructions-----
1. Verify VirtualBox and Whonix PGP signatures
2. Install VirtualBox
3. Double click on Whonix .ova file
4. Whonix is made up of two parts. Whonix Gateway and Whonix Workstation. Therefore, we gotta configure both in VirtualBox.
5. Set the following settings for both VMs
6. General --> Advanced - Clipboard: Disabled - Drag and Drop: Disabled
7. System --> Motherboard - Disable EFI
8. System --> Processor - Disable Nested VT-x or AMD-V (Depending on chip)
9. Display - Disable remote display
10. Storage - Imported VDI only
11. Audio - Disable
12. USB Disabled - Shared Folders Disabled
13. Whonix Gateway Network Settings (Important bit)
14. Network --> Adapter 1 - NAT (Promiscuous mode = Deny)
15. Network --> Adapter 2 - Internal Network (Name = whonix - Promiscuous mode = Deny)
16. Whonix Workstation Network Settings (Important bit) 
17. Network --> Adapter 1 - Internal Network (Name = whonix - Promiscuous mode = Deny)
18. Boot up Whonix Workstation and Whonix Gateway
19. Ensure Whonix Gateway has finished connecting Whonix Workstation to Tor before you start browsing
20. Change your host name to generic name ```sudo hostnamectl set-hostname user```
21. You are all connected and ready for Tor

-----Ramblings-----

The whole time you are browsing TOR a beacon could be sending screenshots through to a C2 on your host and you'd have no idea. This setup
is the epitome of a false sense of security.
From a privacy perspective the amount of holes in this setup is laughable.
But for browsing breach forums or likewise I don't see the harm. Create a new account each time and go wild.
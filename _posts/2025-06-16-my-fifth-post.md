---
layout: post
title:  "Scripts for VirusTotal Free Plan API"
date:   2025-06-16 21:00:00 +1000
categories: scripts
---

<style>
  body { font-size: 16px; }
  body {font-family: 'Inter', sans-serif}
  h1 { font-size: 19px !important; }
  h2 { font-size: 17px !important; }
  h3 { font-size: 15px !important; }
</style>

### Overview
VT Premium costs an arm and a leg. With a non-paid account you can call the API about 500 times a month and 1 call per 15 seconds. Here are a few scripts that work within that tier and are somewhat automated.

### Netstat connections to VT API call

Command grabs all the IPs that the host is currently connected to and whacks sends them off to VT for a reputation check.

Useful for if you think a host could be beaconing out to a C2 but can't identify the IP(s).

[-----Netstat IPs Reputation-----](https://github.com/lvl0socanalyst/Scripts/blob/main/netstat_connections_virustotal_checker.py)

### Reads domains from .txt file and queries VT API

Have a bunch of suspicious domains pulled from proxy logs? Can't be bothered going through one by one?

[-----Domain reputation checker-----](https://github.com/lvl0socanalyst/Scripts/blob/main/virustotal_free_api_call.py)
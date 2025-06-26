---
layout: post
title:  "Proxies and ProxyChains"
date:   2025-06-16 21:00:00 +1000
categories: privacy
---

<style>
  body { font-size: 13px; }
  h1 { font-size: 19px !important; }
  h2 { font-size: 17px !important; }
  h3 { font-size: 15px !important; }
</style>

## Overview

Page details the method for anonymously purchasing proxies and how to utilise them with proxychains.

1. Purchasing Monero (XMR)
2. Buying Proxies
3. Setting up Proxy Chains
4. Ramblings

### Purchasing Monero (XMR)

XMR is still the goto crypto for privacy for transactions. 

All XMR transactions are private by default. Therefore, sender address, receiver address and transaction amounts are hidden.

Transactions are hidden through the process a called mixing. Mixing bundles a legitimate transaction in with decoy transactions, therefore, making it harder to distinguish the actual transaction. 

Unlike BTC. Where all this information is traceable. You can open up blockstream and follow some btc around transaction after transaction. 

There are a number of ways you can purchase cryptocurrency. For the sake of anonymity P2P exchanges are the best.

My recommended P2P exchange is Haveno. 

I won't go into a lot of detail here. I've written a page on how to purchase XMR on Haveno here.

(Link)

### Buying Proxies

There are tonnes of services on the clearnet and darknet that sell proxies. Of the clearnet services these are the standouts in terms of anonymity.

There are a few types of proxies you can choose from depending on what you want them for. In terms of anonymity SOCKS5 proxies are definitely the best.

Why should you use a SOCKS5 proxy?
- SOCKS5 proxies tunnel any TCP traffic, therefore, isn't limited to just web browsing like HTTP/HTTPs proxies. 
- Common tools are easily configured to route traffic through these proxies (nmap, sqlmap, hydra, dirbuster, ssh, netcat)
- Supports username/password authentication to securely access the proxy
- Cannot rewrite HTTP headers (simply just forwards traffic)
- Compatiable with TOR
- Fantastic for malware! (wait I don't mean that)

What about residential proxies? 

Residential proxies are great to blend in with traffic. Web applications are likely to think you are a legitimate user and serve standard content to you. 

Some websites will behave different depending on the nature of the IP you are connecting with.

Residential proxies are ideal to buy off the darknet. These IPs are usually sold by owners of botnets who lease out the devices as proxies to make a bit of extra cash. 

Whilst, yes this type of residential proxy is highly illegal. For anonymity sakes it's pretty good. Pentesting a government web application through Irene's Windows XP computer that's connected to a router that's 20 years old is pretty neat. 

Of course, this is traceable and comes at a cost. All your trust goes into the seller.

On top of that residential proxies are significantly more expensive than SOCKS5 proxies. 

Residential proxies that you buy on the clearnet very typically restrict pentesting and port scanning activities, hence why a lot of them keep logs and ban your account.

Why would you use a residential proxy?
- Web scraping
- Avoid captchas
- Simulate real traffic
- Pentesting on a big budget

So now we've been over the two proxy types you should care about. Here's where you can buy them and some requirements.

-----Requirements for Proxy Sellers-----
- No logging
- Accept XMR payments
- No mobile number required for sign up
- Not owned by Tencent

-----Recommendations for purchasing-----
- OS (Tails or Whonix)
- Tor
- Decentralised/privacy focused form of payment (XMR preferred)

Clearnet proxy stores

[Proxy-Seller](https://proxy-seller.com)
- Accepts XMR and other crypto as payment
- HTTP/SOCKS5, IPv4 and residential proxies
- No logs
- Whitelist IPs

[IPRoyal](https://iproyal.com)
- Very popular and reliable
- Slightly more expensive than usual
- Accept XMR and other crypto as payment
- Whitelist IPs
- Logs for residential proxies

Darknet proxy store
- Digital Goods by ProxyStore - digitazyyxyihwwzudp5syxxyn3qhcd63wqcha2dxpfqiyydmrgdiaad[.]onion

### Setting up Proxy Chains

ProxyChains in short essentially does what it suggests. Chains proxies. No but seriously, think of it like onion routing. Minus the encryption and relay nodes.

Each hop to a proxy makes it harder to trace back where the first hop originated from. The more the merrier. 

The key note with chaining proxies is the first proxy can see where the packet came from (src ip).

### Ramblings
-----Issues with proxies-----
- Free proxies
- Residential proxies
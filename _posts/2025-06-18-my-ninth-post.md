---
layout: post
title:  "Proxies and ProxyChains"
date:   2025-06-16 21:00:00 +1000
categories: privacy
---

<style>
  body { font-size: 16px; }
  body {font-family: 'Inter', sans-serif}
  h1 { font-size: 19px !important; }
  h2 { font-size: 17px !important; }
  h3 { font-size: 15px !important; }
</style>

## Overview

Page details the method for anonymously purchasing proxies and how to utilise them with ProxyChains.

1. Purchasing Monero (XMR)  
2. Buying Proxies  
3. Setting up ProxyChains  
4. Ramblings  

### Purchasing Monero (XMR)

XMR is still the go-to crypto for privacy in transactions. 

All XMR transactions are private by default. Therefore, sender address, receiver address, and transaction amounts are hidden.

Transactions are hidden through a process called mixing. Mixing bundles a legitimate transaction in with decoy transactions, making it harder to distinguish the actual transaction. 

Unlike BTC, where all this information is traceable. You can open up Blockstream and follow some BTC around transaction after transaction. 

There are a number of ways you can purchase cryptocurrency. For the sake of anonymity, P2P exchanges are the best.

My recommended P2P exchange is Haveno. 

I won't go into a lot of detail here. I've written a page on how to purchase XMR on Haveno here.  
(Link)

### Buying Proxies

There are tonnes of services on the clearnet and darknet that sell proxies. Of the clearnet services, these are the standouts in terms of anonymity.

There are a few types of proxies you can choose from depending on what you want them for. In terms of anonymity, SOCKS5 proxies are definitely the best.

Why should you use a SOCKS5 proxy?  
- SOCKS5 proxies tunnel any TCP traffic, so they aren't limited to just web browsing like HTTP/HTTPS proxies.  
- Common tools are easily configured to route traffic through these proxies (nmap, sqlmap, hydra, dirbuster, ssh, netcat).  
- Supports username/password authentication to securely access the proxy.  
- Cannot rewrite HTTP headers (simply just forwards traffic).  
- Compatible with TOR.  
- Fantastic for malware! (wait, I don't mean that)

What about residential proxies?  

Residential proxies are great to blend in with traffic. Web applications are likely to think you are a legitimate user and serve standard content to you. 

Some websites will behave differently depending on the nature of the IP you are connecting with.

Residential proxies are ideal to buy off the darknet. These IPs are usually sold by owners of botnets who lease out the devices as proxies to make a bit of extra cash. 

Whilst yes, this type of residential proxy is highly illegal, for anonymity’s sake it's pretty good. Pentesting a government web application through Irene's Windows XP computer that's connected to a router that's 20 years old is pretty neat. 

Of course, this is traceable and comes at a cost. All your trust goes into the seller.

On top of that, residential proxies are significantly more expensive than SOCKS5 proxies. 

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

-----Clearnet proxy stores-----  

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

-----Darknet proxy store-----  

[Digital Goods by ProxyStore](digitazyyxyihwwzudp5syxxyn3qhcd63wqcha2dxpfqiyydmrgdiaad.onion)  
- Tor hosted for the ultra paranoid  

### Technical breakdown of ProxyChains

Have you ever had a Christmas present wrapped in tonnes of paper? Well, ProxyChains is kind of like that. Except, you are only allowed to open one layer before you pass it to the next sibling to have a go at ripping off another layer. You are only allowed to open the layer that is addressed to your name (IP + Port).

ProxyChains injects 'libproxychains.so' into the memory space of the process you are running (e.g. nmap). It does this through the use of 'LD_PRELOAD', which is an environment variable on Linux that lets you load custom libraries before others. 

'libproxychains.so' intercepts the `connect()` function from the process you are running and says, "hey, follow my `connect()` function." 

That `connect()` function reads your proxychains config file, establishes a connection with your first proxy, and explains to that proxy how to connect to the next. 

So if you had the time and really cared, you could run a `.pcap` on each of your proxy servers and watch the unwrapping occur.

### Setting up ProxyChains

ProxyChains, in short, essentially does what it suggests — chains proxies. No, but seriously, think of it like onion routing. Minus the encryption and relay nodes.

Each hop to a proxy makes it harder to trace back where the first hop originated from. The more the merrier. 

The key note with chaining proxies is the first proxy can see where the packet came from (source IP).

Anyway, there are three different types of chains you can configure:  
- dynamic_chain (top to bottom - one fails - chain reconfigures)  
- strict_chain (defined order - one fails - chain fails)  
- random_chain (chooses random order - one fails - chain fails)  

Lastly, ProxyChains gives an option to choose whether or not to route DNS traffic through ProxyChains. The answer is always yes. 

-----Requirements-----  
- Kali Linux OS  
- A brain  

-----Considerations-----  
How you set up your Kali Linux OS is entirely up to you. In an ideal world, if you were going to do some dodgy stuff on it, make it a fresh install. Therefore, your first connection to the internet is behind ProxyChains and your IP is masked. 

Paying for a Kali Linux EC2 instance on AWS is also a piece of cake. A more expensive alternative but great for easily setting up home labs. Good for AD.  

-----Instructions-----  
1. Open config `sudo nano /etc/proxychains.conf`  
2. Uncomment/comment in the chain type you want  
3. Remove the `#` for `proxy_dns`  
4. Add proxies to the config. Follow this format:  
   `proxy type "socks5" ip "192.168.1.101" port "8080"`  
5. Save and exit  
6. Determine what your exit proxy is to test if working:  
   `proxychains curl ifconfig.me`  

### Ramblings

-----Why you should never use a free proxy-----  
How does a free proxy make its money? Ask yourself that question. Same goes for a free VPN. Why on earth would a free VPN not hand over your data if they were audited legally?

Say Bob wants to access some weird stuff. Sets up redirect to a dodgy proxy. That dodgy proxy sees all the traffic from Bob's host.

To cut costs, I have paid for one proxy, set that as my first hop, and then had a bunch of freebies for the remainder of the chain (`random_chain`). But, it's still not great.

Just pay for proxies and claim it on your tax return.

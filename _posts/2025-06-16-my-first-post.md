---
layout: post
title:  "AWS Hosted MISP"
date:   2025-05-11 21:00:00 +1000
categories: project
---

<style>
  body { font-size: 16px; }
  body {font-family: 'Inter', sans-serif}
  h1 { font-size: 19px !important; }
  h2 { font-size: 17px !important; }
  h3 { font-size: 15px !important; }
</style>

Had the thought to deploy a MISP server at home. But, having the server running all the time has its struggles. So instead I'm going to pay my trusty friends at Amazon to host it for me.

---

Current Setup
- EC2 Instance (T3.medium - Ubuntu 22.04 Server - Spot Instance - Asia AZ)
- Elastic Block Storage (60GB)
- Security Group (Deny all traffic but my IP - SSH, HTTP(s))
- Private/Public Subnet
- VPC
- Cost Per Day ($0.67)

---

I stupidly left this on. And I had a big old bill. I'm going to start taking a snapshot of this everytime I stop using it. 

## Stage 2

Well given the cost. I'm gonna set this up again, but this time on a VPS. Because a lvl0 soc analyst can't afford to be buying Jeff Bezos another superyacht.


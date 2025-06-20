---
layout: post
title:  "AWS Hosted MISP"
date:   2025-06-16 21:00:00 +1000
categories: project
---

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

To-Do
- Start ingesting feeds
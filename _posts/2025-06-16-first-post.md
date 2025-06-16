---
layout: post
title:  "AWS Hosted MISP"
date:   2025-06-16 21:00:00 +1000
categories: Project
---

Had the thought to deploy a MISP server at home. But, having the server running all the time has its struggles. So instead I'm going to pay my trusty friends at Amazon to host it for me.

---

**bold** Current Setup
- EC2 Instance (T3.medium - Ubuntu 22.04 Server - Spot Instance - Asia AZ)
- Security Group (Deny all inbound but my IP - SSH, HTTP(s))
- Private/Public Subnet
- VPC

---

**bold** To-Do
- Start ingesting feeds
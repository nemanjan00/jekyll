---
layout: post
title:  "Daily security tips 1: hostname"
published: true
author: nemanjan00
show_sidebar: true
---

When you connect your computer to new network, what it does to acquire networking settings like Gateway, DNS, network mask, etc. is it uses DHCP protocol. 

If you ever logged into router settings or tried sniffing DHCP traffic, what you would see is there you can see hostname of computer that is connecting. 

Why is that a bad thing? Well, does everyone on network has to know who you are, when you, for example, connect to local coffee shop WiFi? 

So, what is the solution? 

For linux users (if your distro is using dhcpclient), just comment out ``hostname`` line in your ``/etc/dhcpcd.conf``. 


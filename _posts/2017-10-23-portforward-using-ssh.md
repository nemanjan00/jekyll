---
layout: post
title:  "Portforward using ssh"
published: true
author: nemanjan00
show_sidebar: true
---

If you ever had to do portforwarding on network that was not yours, you know it can be pain in the ass sometimes and you most probbably do not want to deal with it. o

This method is very easy to do and will allow you to expose your port to public and even stop local computers from sniffing data to and from that port. 

## How is this done in theory

Well, if you ever used SSH, you probbaly know it is just encrypted tunnel... 

What you maybe did not know is that it can carry a lot more than text. (you cna use it to copy files, to open graphical application, as a SOCKS proxy, etc.)

One of things a lot of people do not know about is that you can also use SSH to forward ports from one side of tunnel to another... 

Lets say your computer can access some port and someone in network you are connecting to can not... 

What you can do is specify port forwarding in SSH configuration. 

Let's say you want to forward local port to WAN... 

What happens is remote machine you are connected to will listen on specified port and when someone connect to that port, your computer will connect to local port and data will be tunneled inside SSH. 

## How to do this in practice

This is actually very simple

```bash
ssh -R 221:localhost:22 server
```

This will make server listen on port 221 and when someone connects to it, you will connect to localhost:22 and connection will be established. 

One small trick is that in OpenSSH (implementation of SSH cost often used) server will listen only on loopback interface (localhost) and not on other interfaces. 

To solve this, we need to add: 

```
GatewayPorts yes
```
to the end of ``/etc/ssh/sshd_config`` and restart SSH server. (``systemctl restart sshd`` for SystemD or ``service ssh restart``)


---
layout: post
title:  "Daily security tips 3: Open Ports"
published: true
author: nemanjan00
show_sidebar: true
---

Imagine each and every port that is open on your computer as a potential door to your computer, for malicious person. 

Every single door more is one more potential door your attacker can open. 

So, what can you do after this? 

# 1. Know your computer networking

To see all of TCP the ports open on your computer, use this command: 

```bash
netstat -tlpn
```

For UDP ports, use: 

```bash
netatal -ulpn
```
## 1.1 What to look for? 

First thing you need to look at is at which interface program is listening at. 

First figure out which IP is on which interface. 

For example: 

```bash
$ ifconfig
enp0s25: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether 3c:97:0e:0f:5c:6b  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device memory 0xe1600000-e1620000  

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 1099393  bytes 2018987222 (1.8 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1099393  bytes 2018987222 (1.8 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

tun0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1304
        inet6 fc15:9d75:e614:a0e1:17a0:61d4:341d:b5dd  prefixlen 8  scopeid 0x0<global>
        inet6 fe80::c39b:ac2a:eac6:6ab9  prefixlen 64  scopeid 0x20<link>
        unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 500  (UNSPEC)
        RX packets 32  bytes 2728 (2.6 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 69  bytes 4400 (4.2 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlp2s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.17  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::95de:a8ca:ddb9:ac84  prefixlen 64  scopeid 0x20<link>
        ether 1e:93:85:2d:6a:b7  txqueuelen 1000  (Ethernet)
        RX packets 3893447  bytes 3986568321 (3.7 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2923905  bytes 733067221 (699.1 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wwp0s20u4i6: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether 02:15:e0:ec:01:00  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

This is my ``ifconfig`` output. 

If you do not have ``ifconfig`` command, you can also use ``ip addr``. 

```bash
$ ip addr            
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s25: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
    link/ether 3c:97:0e:0f:5c:6b brd ff:ff:ff:ff:ff:ff
3: wlp2s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 1e:93:85:2d:6a:b7 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.17/24 brd 192.168.1.255 scope global dynamic noprefixroute wlp2s0
       valid_lft 49655sec preferred_lft 49655sec
    inet 192.168.1.254/24 brd 192.168.1.255 scope global secondary noprefixroute wlp2s0
       valid_lft forever preferred_lft forever
    inet6 fe80::95de:a8ca:ddb9:ac84/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
4: wwp0s20u4i6: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
    link/ether 02:15:e0:ec:01:00 brd ff:ff:ff:ff:ff:ff
19: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1304 qdisc fq_codel state UNKNOWN group default qlen 500
    link/none 
    inet6 fc15:9d75:e614:a0e1:17a0:61d4:341d:b5dd/8 scope global 
       valid_lft forever preferred_lft forever
    inet6 fe80::c39b:ac2a:eac6:6ab9/64 scope link stable-privacy 
       valid_lft forever preferred_lft forever
```

So, in my case, I am connected to wifi and my IP is ``192.168.1.17``. 

I am also connected to VPN and IP is ``fc15:9d75:e614:a0e1:17a0:61d4:341d:b5dd``. 

If application is listening at ``0.0.0.0`` or ``::``, that means it is listening on all interfaces. 

If application is listening at ``127.0.0.1`` or ``::1``, that means it is listening only on a virtual loopback device. 

To figure out which application it is, take a look at ``PID/Program name`` row. 

If there is no name for program, run command as root. 

If PID is 1, that probbably means systemd is forwarding file socket to TCP/UDP port. 

## 1.2 What do you want to accomplish? 

You probably do dot want applications listening on any other interface except loopback if it does not have to. 


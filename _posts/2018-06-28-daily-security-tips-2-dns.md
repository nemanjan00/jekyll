---
layout: post
title:  "Daily security tips 2: DNS"
published: true
author: nemanjan00
show_sidebar: true
---

When you open your web browser and type ``pornhub.com`` into your address bar, what happens in most networks is, your PC asks your router: "Hey, router, what is the address of pornhub.com? " In case your router knows the address, it will send the response to you. If it does not know, it will ask your ISPs DNS server. 

In short, admin of your network, anyone sniffing your network and yourt ISP know you have just visited pornhub... 

And, in the case someone is doing MITM attack, they can even change the response to your query. 

What the best solution currently is to use [dnscrypt](https://github.com/jedisct1/dnscrypt-proxy). 

If you are Arch linux user like myself, you can just install it from the repo: 

```
sudo pacman -S dnscrypt-proxy
```

After that, You need to enable its socker. 

```
sudo systemctl enable dnscrypt-proxy.socker
```

NOTE: Keep in mind that at the momment I am writing this blog post, socket is brocken in the latest version of dnscrypt-proxy, so, you will need to use service instead or to install dnscrypt 2.0.14-2... 

When you have enabled dnscrypt-proxy.socker, now, you need to make your computer use DNSCrypt DNS as main DNS. 

On arch, we use resolvconf to generate ``resolv.conf``. 

To set your DNS to localhost, you need to edit ``/etc/resolvconf.conf`` and set: 

```
name_servers=127.0.0.1
```

Only one more thing to do is to change your DNSCrypt DNS servers. 

I love to use opennic ones, so, I set them to: (``/etc/dnscrypt-proxy/dnscrypt-proxy.toml``): 

```
server_names = ['opennic-luggs', 'fvz-anyone', 'opennic-famicoman']
```


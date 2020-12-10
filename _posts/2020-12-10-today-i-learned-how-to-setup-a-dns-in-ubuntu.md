---
layout: post
title:  "Today I learned how to setup a DNS in Ubuntu"
date:   2020-12-09 23:14:00 +0100
---

Chose the DNS that you want to add:

I wanted to block as many ads as possible so I thought of giving a try to [AdGuard DNS](https://adguard.com/en/adguard-dns/overview.html). AdGuard DNS provides two IPv4 default server addresses:

```
94.140.14.14
94.140.15.15
```

Append the addresses to the `/etc/resolvconf/resolv.conf.d/head` file:

```bash
$ echo "# AdGuard DNS\nnameserver 94.140.14.14\nnameserver 94.140.15.15" >> /etc/resolvconf/resolv.conf.d/head
```

Restart the `resolvconf` service:
```bash
$ sudo systemcl restart resolvconf.service
```

Update the `resolv.conf` file with the following command:
```bash
$ sudo resolvconf -u
```

Now, the IPv4 addresses have been added to the `resolv.conf` file as you can see:
```bash
$ less /etc/resolv.conf
```

To double check that the DNS configuration is actually used, you can use the `nslookup` command:
```bash
$ nslookup wikipedia.org
Server:		94.140.14.14
Address:	94.140.14.14#53

Non-authoritative answer:
Name:	wikipedia.org
Address: 103.102.166.224
Name:	wikipedia.org
Address: 2001:df2:e500:ed1a::1
```

As you can see, the `94.140.14.14` IP address from AdGuard DNS is there!

Credit to: [https://www.tecmint.com/set-permanent-dns-nameservers-in-ubuntu-debian/](https://www.tecmint.com/set-permanent-dns-nameservers-in-ubuntu-debian/)

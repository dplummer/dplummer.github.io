---
layout: post
title:  "DHCPCD timeouts on new archlinux installs"
date:   2013-08-17 08:00:00
categories: networking
---

In installing a new Cisco RV320 router at the office, I ran into some trouble
with the DHCP server. My desktop could get an IP address, but my laptop
couldn't. The request (with `sudo dhcpcd -d -4 eth0`) would just timeout
without a response.

I compared the `/etc/dhcpcd.conf` file between machines and found that newer
Archlinux installs configure the `duid` option. Disabled that and *viola*,
dhcp works!

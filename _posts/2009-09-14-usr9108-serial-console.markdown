---
layout: post
title:  "USRobotics USR9108 serial console access"
date:   2009-09-14 19:40:00
tags: 3.3V BCM6348 Broadcom CFE OpenWrt pinout serial-console TTL UART USR9108 USRobotics
disqus: true
---

I had to debug my **USRobotics router** recently (a good old **USR9108**) as it ceased to respond completely to any network traffic. The **CFE** was working fine (192.168.1.1 could be pinged), so I thought a **tftp firmware flash** would do the trick, but to no avail (I got timeouts all the time). Accessing the **serial console** seemed to be the only way left, so I had to practise some PCB soldering the hard way.

A good tutorial would have been really reassuring at the moment. There's a lot of material on **Linksys routers** (due to its **OpenWrt popularity**), but I couldn't find much about **USR9108**, so I had to pick and guess the **proper pinout**. Now I know, doing it with a properly working multimeter (mine is probably appropriate for battery testing only) would have been much easier.

The **USR9108 has a 4-pin pad next to the Broadcom BCM6348 CPU**. It's **3.3V transistor-transistor logic (TTL)**, so a **special USB converter** (or some reworked USB Nokia data cable) **is a must** (mine is **FT232R** based, and works flawlessly). The proper connection sequence is _**TX, RX, NC, GND**_ as seen below:

<a href="/images/usr9108-uart.jpg"><img src="/images/usr9108-uart-th.jpg" alt="USR9108 UART" title="USR9108 UART" />
<a href="/images/usr9108-pinout.jpg"><img src="/images/usr9108-pinout-th.jpg" alt="USR9108 pinout" title="USR9108 pinout" />

Router's _TX_ and _RX_ have to be connected with _RX_ and _TX_ on the PC side correspondingly, and _GND_ with _GND_ (it's **data/signal ground,** so just grounding it is not the best idea). **Connection settings** which worked in my case are as follows:

**Baud rate:** 115200  
**Data bits:** 8  
**Stop bits:** 1  
**Parity control:** none  
**Flow control:** software (XON/XOFF)

I was having some problems with **RealTerm** (though I don't know why), but **PuTTy** worked a treat.

More technical details about the router can be found in the [OpenWrt wiki](http://wiki.openwrt.org/oldwiki/openwrtdocs/hardware/usrobotics/9108).
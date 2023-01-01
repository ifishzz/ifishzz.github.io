---
title: scapy流量分析
date: 2022-03-24
categories: ["防","流量分析"]
tags: ["流量分析"]
---



# scapy流量分析

关于流量分析，在某些上了手段的场景，主动或者被动获取到了流量，这个时候可以用来威胁猎杀，捞出你想要的信息

scapy就是这样一个适合单兵作战的库，但是如果有es之类的流量分析平台就更好不过了，他们把分层规则都写好了，而且搜索速度极快

```
rdpcap()：读取pcap文件
show()：展示当前类型包含的属性及值
haslayer()：判断当前流是否含有某层数据
getlayer()：根据条件获取数据

http_request = p[HTTPRequest].fields 直接获取提取好的字典形式的http数据用fields

还有一个属性payload可以不断进下一个层
比如现在有四个层 Ether,IP,TCP,RAW
IP 可以表示为p.payload, 想要IP层的src可以用p.payload.src
```

## demo

scapy主要是分为Ethernet、IP、TCP、Raw这四层，每一层都有每一层的关键字，可以利用键值对的方式直接读取相应的内容。


```
import scapy_http.http
try:
    import scapy.all as scapy
except ImportError:
    import scapy
    
    
def parse_http_pcap(pcap_path):
    pcap_infos = list()
    packets = scapy.rdpcap(pcap_path)
    for p in packets:
        print "----"
        # 判断是否包含某一层，用haslayer
        if p.haslayer("IP"):
            src_ip = p["IP"].src
            dst_ip = p["IP"].dst
            print "sip: %s" % src_ip
            print "dip: %s" % dst_ip
        if p.haslayer("TCP"):
            # 获取某一层的原始负载用.payload.original
            raw_http = p["TCP"].payload.original
            sport = p["TCP"].sport
            dport = p["TCP"].dport
            print "sport: %s" % sport
            print "dport: %s" % dport
            print "raw_http:\n%s" % raw_http
        if p.haslayer("HTTPRequest"):
            host = p["HTTPRequest"].Host
            uri = p["HTTPRequest"].Path
            # 直接获取提取好的字典形式的http数据用fields
            http_fields = p["HTTPRequest"].fields
            http_payload = p["HTTPRequest"].payload.fields
            print "host: %s" % host
            print "uri: %s" % uri
            print "http_fields:\n%s" % http_fields
            print "http_payload:\n%s" % http_payload
            
            
parse_http_pcap("test.pcap")

```


## 参考
https://www.jianshu.com/p/805075ca9d53

https://blog.csdn.net/Yuberhu/article/details/64123516

https://www.cnblogs.com/17bdw/p/10562213.html#_label4

https://www.xmanblog.net/python-pcap/
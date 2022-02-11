---
title: windows evtx日志解析
date: 2022-02-09
categories: ["防","Window日志分析"]
tags: ["Window日志分析"]
---

# windows evtx日志解析

在溯源过程中使用Log Parser,Event Log Explorer之类的工具需要熟悉工具的语法,有的还要收费,遇到大文件打开还会卡死

还是解析了在匹配所需关键信息较为方便


```
import Evtx.Evtx as evtx
import Evtx.Views as e_views


def main():
    import argparse

    parser = argparse.ArgumentParser(
        description="Dump a binary EVTX file into XML.")
    parser.add_argument("--evtx", type=str,
                        help="Path to the Windows EVTX event log file")
    args = parser.parse_args()

    try:
        with evtx.Evtx(args.evtx) as log:

            with open('windows_log.xml', 'w') as f:
                f.write(e_views.XML_HEADER)
                f.write("<Events>")
                for record in log.records():
                    print(record.xml())
                    f.write(record.xml())
                f.write("</Events>")
    except:
        pass


if __name__ == "__main__":
    main()

```
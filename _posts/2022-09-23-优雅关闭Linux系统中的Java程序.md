---
layout: post
title: "优雅关闭Linux系统中的Java程序"
date: 2022-09-23 15:01:39
categories: Linux
tags: Linux 关闭程序
author: NanKe
---

* content
{:toc}
> 优雅关闭Linux系统中的Java程序



# 问题描述

`优雅关闭Linux系统中的Java程序`

当使用命令`nohup java -jar news.jar > /dev/null 2> /dev/null &`运行Java程序后，需要优雅关闭程序

---

# 解决方案：
>查看Java程序的PID，根据PID使用kil命令关闭程序

1. 使用 lsof -i :8888 命令查询8888端口的进程 PID

2. 使用 kill -15 PID 命令发送 SIGTERM 信号给该进程，该信号可以让 Java 程序优雅地关闭，允许它完成任何当前处理

3. 如果在一段时间内程序没有关闭，则可以使用 kill -9 PID 命令强制终止该进程

`注意`

强制终止进程可能会导致数据丢失或不一致，因此应尽量避免使用

`安装lsof`

>lsof 是 Linux 中的一个命令，它的全称是 List Open Files。它的主要作用是查询某个进程打开的文件，也可以查询某个文件被哪个进程占用。可以通过指定参数，实现对网络连接、端口、文件等的查询。因此，在实际的系统管理或调试过程中，lsof 命令非常有用。

1. Debian/Ubuntu：`sudo apt-get install lsof`
2. Fedora/CentOS：`sudo yum install lsof`

在安装完成后，执行 "lsof -i :8888" 命令，如果正确安装了 "lsof" 命令，它将显示在 8888 端口上运行的程序的详细信息。接下来，你可以使用 "kill" 命令结束该程序的运行，例如：kill pid，其中 pid 是运行该程序的进程 ID

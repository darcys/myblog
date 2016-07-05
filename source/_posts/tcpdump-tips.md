title: tcpdump-tips
date: 2016-06-28 11:11:05
tags:
---

<!-- toc -->
## tcpdump是经常会用到的工具，但是每次用都不太记得命令，要去google，所以现在每次使用都把用法记录下来，方便后面查找

## 使用方法

### 抓某个端口
tcpdump tcp port 22

### 抓某个端口同时filter掉某台机器，不抓这台机器
tcpdump tcp port 22 and not host 10.10.16.27

### 保存为cap文件，每个分片文件大小100m
tcpdump  tcp port 5222 -C 100m -w xmpp.cap

<!-- more -->

---
title: RAID到HDFS,从水平伸缩到水平伸缩(大数据的存储)
date: 2018-11-08 9:00:00
tags: hdfs
comments: true
categories: 分布式
---

### RAID(独立磁盘 _冗余_ 阵列)
        RAID技术是将 多块普通磁盘 组成一个阵列,共同对外提供服务.主要是为了改善磁盘的存储容量,读写速度,增强磁盘的可用性和容错性.

- 常用的RAID技术

![raid.png](https://static001.geekbang.org/resource/image/54/af/54e170b7438fe3b8f8196dbfbc943baf.jpg)


### HDFS(分布式文件系统)
        通过添加更多的 服务器 实现数据更大,更快,更安全的存储与访问.

- HDFS架构

![HDFS.png](http://wx1.sinaimg.cn/mw690/006pTdaLgy1fx0e0cn555j30zk0k0wft.jpg)
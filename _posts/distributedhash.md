---
title: hash算法在分布式系统中的应用
data: 2018-11-09 22:00:00
tags: 分布式 
commnents: true
categories: 数据结构
---

### Hash算法的应用:
- 安全加密(信息摘要  AES  DES  MD5  SHA )
- 数据校验(文件下载,BT下载 等 P2P下载)
- 唯一标识
- 散列函数

****

### Hash算法在分布式系统中的应用

- 负载均衡

负载均衡算法有很多,比如轮询、随机、加权轮询等.

- 数据分片



- 分布式存储
        
海量数据存储问题,为了提高数据的读取,写入能力,一般采用分布式方式存储数据,如分布式缓存
        如何决定及哪个数据放到哪个机器上,可以借助数据分片思想,通过Hash算法对数据取Hash值. 
        对机器个数取模,这个最终值就是应该存储的缓存机器编号.当数据增多,原来的机器(假设10台)已经无法承
        受,需要扩容,当扩容到11个机器时,就产生麻烦了.
            
原来数据13在当机器为10台时,存储在编号为3(13%10)的机器上,当增加一台则机器为11台时,数据13则存储在编号为2(13%11)的机器上了.因此,每当机器的数量发生改变时,所有的数据都需要重新计算哈希值.这相当于缓存中的数据全都失效.
 由于缓存的失效,所有的数据请求不会去找缓存,而是直接去请求数据库.这样就可能发生雪崩效应,压垮数据.
如何解决机器数量的改变而导致的缓存数据失效问题?

#### 一致性Hash算法(集群中机器数量发生变化时,不需要做大量的数据搬移)
基本思想:
        假设有k台机器,数据的Hash值范围是[0,MAX].我们将整个范围划分成m个小区间(m远大于k),每个机器负责m/k个小区间.当有新机器加入的时候,我们就将某几个小区间的数据,从原理的机器中搬移到新的机器中.
        这样,既不用全部重新哈希,搬移数据,也保持了机器上数据数量的负载均衡.
- Redis集群就是应用的一致性哈希算法.










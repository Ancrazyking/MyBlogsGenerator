---
title: HDFS原理篇(Hadoop Distributed File System)
data: 2018-11-10 20:00:00
tags: 分布式
commnents: true
categories: 大数据
---

### HDFS
Hadoop Distributed File System,即[Hadoop](http://hadoop.apache.org/)分布式文件系统.

#### 概述
****
1. HDFS集群的两大角色:NameNode(主) 和 DataNode(从). [大部分分布式系统都是基于主从架构的]
2. NameNode负责管理整个文件系统的元数据(元数据:描述数据的数据.如一个文件的创建时间,权限等)
3. DataNode负责管理用户文件数据块(block).[Hadoop 2.x默认块大小为128MB]
4. 文件会按固定大小(128MB)切成若个块后分布式存储在若干个DataNode上
5. 每个文件块有多个副本(冗余存储),并存放在不同的datanode上
6. DataNode会定期向NameNode汇报自身所保存的文件块信息, __NameNode__ 负责保持文件的副本数量
7. Hadoop客户端请求访问HDFS都是通过向 __NameNode__ 申请来进行的.


#### HDFS写数据流程
****
- 流程
1. 客户端向HDFS写数据,首先要跟NameNode通信以确认可以写文件并获得接收文件块的DataNode信息.
2. 客户端按顺序将文件逐个块传递给相应的DataNode,并由接收到块的DataNode负责向其它DataNode复制块的副本.

- 图解(某马老师画的图)

![hdfs写数据流程.png](http://wx3.sinaimg.cn/mw690/006pTdaLgy1fx223hnpdzj30ox0fxgmj.jpg)


- 客户端向HDFS写数据流程
1. Client与NameNode交互,请求上传文件,NameNode检查目标文件是否存在
2. NameNode返回Client处理结果,是否可以上传
3. Client请求第一个block该上传到哪些服务器上
4. NameNode返回可以上传的DataNodes
5. Client与DataNodes中最近的一台建立连接传输数据(RPC),DataNodes之间会相互建立连接(PipeLine).
6. Client开始往连接的DataNode上传,以Packet为单位.各个DataNode之间也会相互传输.
7. Client的第一个block传输完成之后,Client会再次请求NameNode上传第二个block的服务器.



#### HDFS读数据流程
****
- 流程
1. 客户端将要读取的文件路径发送给NameNode,NameNode找到该文件的元信息(block的存放位置)并返回给客户端,客户端根据返回信息找到相应的DataNode.
2. 客户端与相应的DataNode建立连接,在本地进行数据追加合并从而获得整个文件.



- 图解

![hdfs读数据流程.png](http://wx3.sinaimg.cn/mw690/006pTdaLgy1fx223kyey3j30se0dsjrw.jpg)


- 客户端向HDFS读数据流程
1. Client与NameNode交互,找到请求文件块所在的DataNode服务器
2. 与最近的DataNode服务器建立连接,请求建立(Socket流)
3. DataNode发送数据(以Packet为单位进行校验)
4. Client以Packet单位接收,本地缓存合并


#### NameNode工作机制
****
- NameNode工作机制,元数据管理机制.

- NameNode职责
1. 负责响应客户端的请求
2. 元数据的管理(查询,修改)


- __元数据管理__
NameNode对数据的管理采用了三种存储形式:
1. 内存数据(NameSystem)
```
内存中有一份完整的元数据(meta data)
```
2. 磁盘元数据镜像文件
```
磁盘有一个完整的元数据镜像(fsimage)文件
```
3. 数据操作日志文件(通过记录用户的操作日志)
```
用于衔接内存元数据和持久化元数据镜像fsimage之间的操作日志(edits文件)
```

- 元数据的checkpoint

每隔一段时间,会由Secondary NameNode将NameNode上积累的所有edits和一个最新的fsimage下载到本地,并加载到内存中进行合并merge(这个过程称为CheckPoint)

![Secondary NameNode的合并过程.png](http://wx2.sinaimg.cn/mw690/006pTdaLgy1fx23jgdfs8j30kj0b4mxm.jpg)




- SecondaryNameNode的CheckPoint

NameNode和Secondary NameNode的工作目录存储结构完全相同,所以当NameNode故障退出需要重新恢复时,可以从Secondary NameNoe的工作目录中将fsimage拷贝到NameNode的工作目录,以恢复NameNode的元数据.



#### DataNode工作机制
****

- DataNode工作职责
1. 存储管理用户文件块数据
2. 定期向NameNode汇报自身所持有的block信息(心跳信息)


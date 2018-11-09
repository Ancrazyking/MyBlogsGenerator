---
title: Storm 概念 组件 与编程模型
date: 2018-11-07 18:09:59
tags: storm
comments: true
categories: 大数据实时计算
---



- OLTP(on-line transaction processing)联机事务处理(传统关系数据库) 
- OLAP(on-line analytical  processing)联机分析处理(数据仓库系统) 数据量较大


#### Storm是由什么语言编写的?
- 核心逻辑是Clojure,其他还有Python,Java.
- Clojure是一种运行在JVM上的Lisp(函数式编程)方言



 #### Storm用来实时处理数据
- 低延迟 高可用 分布式 可扩展  分区容错(数据不丢失)

1. Flume实时采集   低延迟
   
2. Kafka消息队列   低延迟

3. Storm实时计算   低延迟 

4. Redis实时存储   低延迟




 #### Storm与Hadoop的区别
- Storm用于实时计算,Hadoop用于离线计算.
- Storm处理的数据保存在内存中,源源不断;Hadoop处理的数据在磁盘(文件系统),一批一批.
- Storm的数据通过网络传输进来;Hadoop的数据保存在磁盘(文件系统).
- Storm与Hadoop __编程模型__ 类似.

编程模型:

![storm.png](http://wx4.sinaimg.cn/mw690/006pTdaLgy1fwyc9nxqyxj30by08175j.jpg)

1. Nimbus      灵气,(神头上的)光轮   主节点

2. Supervisor  监督人               从节点

3. Topology    拓扑

4. Spout       喷口,喷出                        (Task任务类型  Spout  线程)

5. Bolt       突然的,筛选,突然说出,脱口说出       (Task任务类型  Bolt  线程)

#

 #### Storm应用场景及行业案例
Storm用来实时计算源源不断产生的数据,如同流水线生产.

##### 应用场景
- 日志分析
    
    从海量日志中分析出特定数据,将分析的结果存入数据库
- 管道系统
    
    将一个数据从一个系统传输到另外一个系统,如将数据库同步到Hadoop
- 消息转换器

    将接受到的消息按照某种格式进行转化,存储到另外一个系统如消息中间件


##### 典型案例
![](http://wx1.sinaimg.cn/mw690/006pTdaLgy1fwyckegszhj30v20ho41h.jpg)


 #### Storm核心组件

![](http://wx2.sinaimg.cn/mw690/006pTdaLgy1fwycmq4l5tj30xe0pin3r.jpg)
- nimbus只有一个,supervisor有多个,一个supervisor可以有多个worker.
- nimbus通过zookeeper与supervisor交互


 #### Storm编程模型

![Storm编程模型.png](http://wx4.sinaimg.cn/mw690/006pTdaLgy1fwycqzo7akj30w80nq42j.jpg)

- nimbus: 负责资源分配和任务调度
- supervisor: 负责接受nimbus分配的任务,启动和停止属于自己管理的worker进程(一个端口号对应一个进程).
- worker: 运行具体处理组件逻辑的进程.worker的任务类型有两种(Spout任务,Bolt任务).
- task: worker中每个spout/bolt的线程称为一个task.




 #### 流式计算一般架构图
![流式计算架构图.png](http://wx4.sinaimg.cn/mw690/006pTdaLgy1fwycqulsrnj30wz051q4v.jpg)

- flume用来获取数据(可用于日志收集)
- Kafka用来临时保存数据(消息队列)
- Storm用来计算数据(实时计算)
- Redis用来保存数据(内存数据库) 持久化


#
#### Storm编程模型总结
1. 编程模型
        
        DataSource:外部数据源
        Spout: 接收外部数据源的组件,将外部数据源转换成Storm内部的数据,以 Tuple(元组) 为基本的传输单元下发给Bolt.
        Bolt: 接收Spout发送的数据,或上游的Bolt发送的数据.根据业务逻辑进行处理,发送给下一个Bolt或者是存储到某个介质上,介质可以是Redis或MySql等.
        Tuple: Storm内部中数据传输的 基本单元 ,里面封装的List对象,用来保存数据.

 StreamGrouping:数据分组策略(7种)
 - ShuffleGrouping      (Random函数)
 - NonGrouping          (Random函数)
 - FiledGrouping        (Hash取模)
 - Local or ShuffleGrouping (本地或随机 本地优先)
- ...



2. 并发度

        用户指定的一个任务,可以被多少线程执行,并发度等于线程的数量.一个任务的多个线程,会被运行在多个Worker(JVM)上.
        平均算法的负载均衡策略,尽可能减少网络IO,类似于Hadoop中的MapReduce中的本地计算.

3. 架构

        Nimbus: 任务分配
        Supervisor: 接收任务,启动worker.worker的数量根据端口号的配置.
        Worker:执行任务的具体组件(一个JVM进程),可以执行两种类型的任务,Spout任务和Bolt任务.
        Task:(Spout任务或Bolt任务)
        ZooKeeper: 保存信息,任务分配,心跳信息,元数据信息等.
        Tepology: 一个Strom应用程序.

4. Worker与Topology

        一个worker只属于一个topology
        一个topology要求的worker数量如果不被满足,集群在任务分配时,根据现有的worker先运行topology.如果当前集群中worker数量为0,那么最新提交的topology将只会被标识active,不会运行.只有当集群有了空闲资源,才能够运行.



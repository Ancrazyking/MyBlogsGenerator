---
title: Storm
date: 2018-11-07 18:09:59
tags: storm
comments: true
categories: 大数据实时计算
---



- OLTP(on-line transaction processing)联机事务处理(传统关系数据库) 
- OLAP(on-line analytical  processing)联机分析处理(数据仓库系统) 数据量较大


Storm是由什么语言编写的?
- 核心逻辑是Clojure,其他还有Python,Java.
- Clojure是一种运行在JVM上的Lisp(函数式编程)方言



> #### Storm用来实时处理数据
- 低延迟 高可用 分布式 可扩展  分区容错(数据不丢失)

> Flume实时采集   低延迟
>   
> Kafka消息队列   低延迟
>
> Storm实时计算   低延迟 
>
> Redis实时存储   低延迟




> #### Storm与Hadoop的区别
- Storm用于实时计算,Hadoop用于离线计算.
- Storm处理的数据保存在内存中,源源不断;Hadoop处理的数据在磁盘(文件系统),一批一批.
- Storm的数据通过网络传输进来;Hadoop的数据保存在磁盘(文件系统).
- Storm与Hadoop __编程模型__ 类似.

编程模型:

![](http://wx4.sinaimg.cn/mw690/006pTdaLgy1fwyc9nxqyxj30by08175j.jpg)

> Nimbus 
>
> Supervisor  监督人
>
> Topology    拓扑
>
> Spout       喷口,喷出
>
> Bolt        突然的,筛选,突然说出,脱口说出


> #### Storm应用场景及行业案例
Storm用来实时计算源源不断产生的数据,如同流水线生产.

##### 应用场景
- 日志分析
> 从海量日志中分析出特定数据,将分析的结果存入数据库
- 管道系统
> 将一个数据从一个系统传输到另外一个系统,如将数据库同步到Hadoop
- 消息转换器
> 将接受到的消息按照某种格式进行转化,存储到另外一个系统如消息中间件


##### 典型案例
![](http://wx1.sinaimg.cn/mw690/006pTdaLgy1fwyckegszhj30v20ho41h.jpg)


> #### Storm核心组件

![](http://wx2.sinaimg.cn/mw690/006pTdaLgy1fwycmq4l5tj30xe0pin3r.jpg)



> #### Storm编程模型

![Storm编程模型.png](http://wx4.sinaimg.cn/mw690/006pTdaLgy1fwycqzo7akj30w80nq42j.jpg)




> #### 流式计算一般架构图
![流式计算架构图.png](http://wx4.sinaimg.cn/mw690/006pTdaLgy1fwycqulsrnj30wz051q4v.jpg)

- flume用来获取数据(可用于日志收集)
- Kafka用来临时保存数据(消息队列)
- Storm用来计算数据(实时计算)
- Redis用来保存数据(内存数据库) 持久化









---
title: MapReduce数据合并与连接机制(Shuffle)
data: 2018-11-14 22:00:00
tags: 分布式 
commnents: true
categories: 分布式编程框架
---

### MapReduce计算框架是如何运作的



一个WordCount程序例子

![WordCount.png](http://wx1.sinaimg.cn/mw690/006pTdaLgy1fx91ne046yj313u08c3zw.jpg)



- 如何为每个数据块分配一个Map计算任务,代码是如何发送到数据块所在服务器的,发送后是如何启动的,启动以后如何知道自己需要计算的数据在文件什么位置(即BlockID是什么)

- 处于不同服务器的map输出的<Key,Value>,如何把相同的Key聚合在一起发送给Reduce任务进行处理.




#### MapReduce作业启动和运行机制(以Hadoop1为例,Hadoop2则变为ResourceManager和NodeManager)
MapReduce运行过程涉及三类关键进程
1. 大数据应用进程.即启动MapReduce程序的主入口,主要指定Map和Reduce类,输入输出文件路径,提交作业到Hadoop集群,也就是JobTracker进程.

2. JobTracker进程.根据要处理的输入数据量,命令的TaskTracker进程启动相应数量的Map和Reduce进程任务.管理整个作业生命周期的任务调度和监控.JobTracker进程在整个Hadoop集群中唯一

3. TaskTracker进程.这个进程负责启动和管理Map进程以及Reduce进程.因为每个数据块都有对应的map函数,TaskTracker进程通常和HDFS的DataNode进启动在同一个服务器.Hadoop集群中绝大多数服务器同时运行DataNode进程和TaskTracker进程.

<p style="text-indent:2em">JobTracker进程和TaskTracker进程是主从关系,主服务器通常只有1台(或者有另一台备份主服务器提供高可用,运行时只有1台服务器对外提供服务提供).从服务器可能有成百上千台,所以从服务器听从主服务器的控制和调度安排.
</p>
<p style="text-indent:2em">
主服务器负责为应用程序分配服务器资源及作业执行的调度,而具体的计算操作则在从服务器上完成.
</p>
<p style="text-indent:2em">
这样说来, <b>一主多从</b> 是大数据领域最主要的架构模式.主服务器只有一台(另一台备份保证高可用),掌控全局(资源调度,任务分配);从服务器多台,负责具体的任务.将很多台服务器有效组织起来,对外表现出一个统一又强大的计算能力.
</p>

![Hadoop1.x的MapReduce作业启动和运行机制.png](http://wx3.sinaimg.cn/mw690/006pTdaLgy1fx91nua21rj31200madih.jpg)





#### MapReduce数据合并与连接机制--Shuffle
<p style="text-indent:2em">
在map输出与reduce输入之间,MapReduce计算框架处理数据合并与连接操作,这个操作叫<b>Shuffle</b>.
</p>

![MapReduceShuffle过程.png](http://wx2.sinaimg.cn/mw690/006pTdaLgy1fx91nyzdk4j310k0lgq4z.jpg)

<p style="text-indent:2em">每个Map任务的计算结果都会写入<b>本地文件系统</b>,等Map任务快要计算完成的时候,MapReduce计算框架会启动shuffle过程,在Map任务进程调用一个Partitioner接口,对Map产生的每个(Key,Value)进行Reduce分区选择,然后通过HTTP通信发送给对应的Reduce进程.
</p>
<p style="text-indent:2em">
    这样不管Map位于哪个服务器节点,相同的Key一定会被发送给相同的Reduce进程.Reduce任务进程对收到的(Key,Value)进行排序和合并.将相同的Key放在一起,组成一个(Key,Value集合)传递给Reduce任务执行.
</p>

<p style="text-indent:2em">
map输出的(Key,Value)shuffle到哪个Reduce分区是这里的关键,它是由Partitioner来实现(用户可以自定义Partitioner)来实现对Reduce的分区.<b>默认使用Key的哈希值对Reduce任务数量取模,</b>相同的key一定会落在相同的Reduce任务ID上.
<p>

```
public int getPartition(K2 key,V2 value,int numReduceTasks){
    return (key.hashCode() & Integer.MAX_VALUE%numReduceTasks);
    //hashCode()&Integer的最大值对ReduceTasks的数量取模.
}
```
总的来说,分布式计算将不同服务器上的相关数据合并到一起进行下一步计算,这就是 __shuffle__.

<p style="text-indent:2em">
Shuffle是大数据计算过程中最神奇的地方,不管是MapReduce还是Spark,只要是大数据批处理计算,一定会有 __Shuffle__ 过程.
</p>



















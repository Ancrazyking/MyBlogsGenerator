---
title: MapReduce
data: 2018-11-14 22:00:00
tags: 分布式 
commnents: true
categories: 分布式编程框架
---
### 1.MapReduce概述
****
<p style="text-indent:2em">MapReduce是一个分布式运算程序的编程框架,是用户开发的 __"基于Hadoop的数据分析应用"__ 的核心框架.
</p>
<p style="text-indent:2em">
MapReduce核心功能是将用户编写的业务逻辑代码和自带的默认组件整合成一个完整的分布式运算程序,并在Hadoop集群上运行.
</p>

### 2.MapReduce框架的结构及核心运行机制
****
一个完整的MapReduce程序在分布式运行时有三类实例进程:
1. MRAppMaster: 负责整个程序的过程 __调度及__ 状态 __协调__ .
2. MapTask:负责map阶段的整个数据处理流程.
3. ReduceTask:负责reduce阶段的整个数据处理流程.

#### 2.1 完整流程

![mapreduce程序运行流程.png](http://wx2.sinaimg.cn/mw690/006pTdaLgy1fx7vkj3zj5j30xy0kmq42.jpg)

#### 2.2 流程解析
1. 一个mr程序启动时,最先启动MRAppMaster, __MRAppMaster__ 启动后根据本次job的描述信息,计算出需要的MapTask实例的数量,然后向集群申请机器启动相应数量的MapTask进程.
2. MapTask进程启动之后,根据给定的数据切片范围进行数据处理,主体流程为:
```
    a) 利用客户指定的inputformat来获取RecordReader读取数据,形成输入KV对
    b) 将输入KV对传递给MR程序定义的map()方法,做逻辑运算,并将map()方法输出的KV对收集到缓存
    c) 将缓存中的KV对安装K( __Key__ )分区排序后不断 __溢写__ 到磁盘文件
```
3. MRAppMaster监控到所有MapTask进程任务完成之后,会根据MR程序指定的参数启动相应数量的ReduceTask(setReduceTaskNum)进程,并告知reducetask进程要处理的数据范围(指定分区Partitioner,默认是Hash).
4. Reducetask进程启动后,根据MRAppMaster告知待处理数据所在位置,从若干台MapTask运行所在机器上获取若干个MapTask输出结果文件,并在本地进行重新归并排序,然后按照相同key的KV为一组,用MR程序定义的reduce()方法进行逻辑运算,并收集运算输出的结果KV,让后调用客户指定的outputformat将结果数据输出到外部存储.

#### 2.3 MapTask并行度决定机制
<p style="text-indent:2em">一个job的map阶段并行度由客户端在提交job时决定;
而客户端对map阶段并行度的规划的基本逻辑为:
</p>

```
    将待处理数据执行逻辑切片(即按照一个[特定切片]大小,将待处理数据划分成逻辑上的多个split),然后每一个split分配一个MapTask并行实例处理.
```
<p style="text-indent:2em">这段逻辑及形成的切片规划描述文件,由FileInputFormat实现类的getSplits()方法完成,其过程如下图:
</p>

![map任务分片机制.png](http://wx1.sinaimg.cn/mw690/006pTdaLgy1fx7ydh6l3nj30rq0h00tg.jpg)


```
    FileInputFormat切片机制默认块为128M
    假设待处理数据有两个文件:
            file1.txt  320M
            file2.txt  10M
    经过FileInputFormat的切片机制运算后,形成的切片信息如下:
            file1.txt.split1   0-128
            file2.txt.split2   128-256
            file3.txt.split3   256-320
            file2.txt.split1   0-10
```

#### 2.4 ReduceTask并行度决定机制
<p style="text-indent:2em">
ReduceTask的并行度与MapTask的并行度决定不同,ReduceTask数量的决定是可以直接手动设置:
</p>

```
    //默认值是1,手动设置为4
    job.setNumReduceTasks(4);

    ReduceTask数量并不是任意设置,还需考虑业务逻辑需求,有些情况下,需要技术全局汇总结果时,
就只能有1个ReduceTask;

    尽量不要运行太多的ReduceTask.对于大多数job来说,最好Reduce的个数最多和集群中的Reduce持平,或者比集群的Reduce Slots小.

```

 __如果数据分配不均匀,就可能在reduce阶段产生数据倾斜__.


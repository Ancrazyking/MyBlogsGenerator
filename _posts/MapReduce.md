---
title: MapReduce过程
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
```
    
    Map阶段为每个数据块分配一个Map计算任务;  //<word,1>
    
    然后将所有map输出的key进行合并;         //<word,<1,1,1,1,1>>

    相同的Key及对应的Value发送给同一个Reduce任务去处理.  //word 5
```



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

 __如果数据分配不均匀,就可能在reduce阶段产生数据 _倾斜___.


#### 2.5 MapReduce编程

```
1)三个部分:Mapper Reducer Driver(可以提交运行mr程序的客户端)
2)Mapper的输入数据是Key-Value(Key,Value类型可以自定义)
3)Mapper的输出数据是Key-Value(Key,Value类型可以自定义)
4)Mapper的业务逻辑写在map()方法中
5)map()方法(MapTask进程)对每个<Key,Value>调用一次
6)Reducer的输入类型对应Mapper的输出数据类型,也是Key-Value
7)Reducer的业务逻辑写在reduce()方法中
8)ReduceTask进程对每一组相同的Key的组调用一次reduce()方法
9)Mapper和Reducer需要继承各自的父类
10)整个程序需要一个Driver来提交,是一个描述Job必要信息的对象
```


#### 2.6 MapReduce中的Combiner
- Combiner是MR程序中的Mapper和Reducer之外的一种组件
- Combiner的父类是Reducer
- Combiner和Reducer区别于运行的位置
```
        Combiner是在每一个MapTask所在的节点运行
        Reducer是接收全局所有MapTask的输出结果
```
- Combiner的意义对每一个MapTask的输出结果局部汇总,减少网络传输
- Combiner应用的前提是不能影响业务逻辑,Key-Value需与Reducer的Key-Value对应

### 3. MapReduce中的 _Shuffle_ 机制(介于Map任务输出与Reduce任务输入之间)
****
- Shuffle:在MapReduce中,Map阶段处理的数据如何传递给Reduce阶段.是MapReduce框架中最关键的一个流程,这个流程就是shuffle.
- Shuffle:(核心机制: 数据分区  排序   缓存 )
- Shuffle就是将 _MapTask输出的处理结果数据_ ,分发给ReduceTask,并在分发的过程中,对数据按 __key__ 进行分区和排序.


#### 3.1 Shuffle主要流程
```
1. 分区partition
2. Sort根据key排序
3. Combiner进行局部Value的合并
```

#### 3.2 详细流程
1. MapTask收集map()方法中输出的Key-Value对,放入内存缓冲区中
2. 从内存缓冲区不断 _溢出本地磁盘文件_ ,可能会溢出多个文件
3. 多个溢出文件会被合并成大的溢出文件
4. 在溢出及合并过程中,都要调用partitioner进行分组和针对key进行排序
5. ReduceTask根据自己的分区号,去各个MapTask机器上取相应的结果分区数据
6. ReduceTask会取到同一个分区的来自不同MapTask的结果文件,ReduceTask会将这些文件再进行合并(归并排序)
7. 合并成大文件后,Shuffle的阶段完成,后面进入ReduceTask的逻辑运算.(具体逻辑运算过程)
```
        从文件中取出一个一个键值对,调用用户自定义的reduce()方法
```
<p style="text-indent:2em">
Shuffle中的缓冲区会影响到MapReduce程序的执行效率,缓冲区越大,则磁盘的IO次数越少,执行速度则越快.</p>
 <p style="text-indent:2em"> 缓冲区大小可以调整,参数:io.sort.mb  默认为 100M.
</p>

#### 3.3 详细流程示意图

![shuffle.png](http://wx2.sinaimg.cn/mw690/006pTdaLgy1fx8jg4j28hj31kw0fbmzc.jpg)

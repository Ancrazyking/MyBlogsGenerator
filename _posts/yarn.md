---
title: 资源调度框架Yarn（Yet Another Resource Negotiator）
data: 2018-11-18 9:00:00
tags: 分布式 
commnents: true
categories: 分布式
---
#### 概述
****
Hadoop主要由三部分组成:分布式文件系统HDFS、分布式计算框架MapReduce,和分布式集群资源调度框架Yarn。
<p style="text-indent:2em">
在MapReduce应用程序的启动过程中，最重要的是把MapReduce程序分发到大数据集群的服务器上。在Hadoop1中，这个过程主要是通过TaskTracker和JobTracker通信来完成。
</p>

<p style="text-indent:2em">
这个方案有什么缺点？服务器集群资源调度管理和MapReduce执行过程耦合在一起，如果想在当前集群中运行其他计算任务，如Spark或Storm，就无法统一使用集群中的资源了。
</p>

Hadoop 1.x时的资源分配，JobTracker和TaskTracker
![Hadoop1.png](http://wx2.sinaimg.cn/mw690/006pTdaLgy1fxc2muz4yrj30dh0amq3a.jpg)





<p style="text-indent:2em">
Hadoop 2之后, 把MapReduce的资源管理和计算框架分离，并且将Yarn从MapReduce中分离处理，称为一个 <b>独立的资源调度框架。</b>
</p>

#### Yarn的架构
****
![Yarn.png](http://wx4.sinaimg.cn/mw690/006pTdaLgy1fxc2mzgut5j31du0u011d.jpg)

Yarn包含两部分：资源管理器（ResuorceManager）和节点管理器(NodeManager),这也是Yarn的两种主要进程。
- ResourceManager（主）：负责整个集群的资源管理调度，通常部署在独立的服务器上。

- NodeManager（从）：负责具体服务器上的资源和任务管理，在集群的每一台计算服务器上都会启动，基本上与HDFS的DataNode进程一起出现。
- Container: Yarn进行资源分配的单位，由NodeManager进程启动和管理。
- ApplicationMaster： 
<p style="text-indent:2em">
运行在Container里，每个应用程序启动后先启动ApplicationMaster，ApplicationMaster根据应用程序的资源需求进一步向ResourceManager进程申请容器资源，得到容器后就会分发应用程序代码到容器上启动，进而开始分布式技术。
</p>


- 具体来说，资源管理器又包括调度器和应用程序管理器。
<p style="text-indent:2em">    调度器：一个资源分配算法，根据应用程序提交的资源申请和当前服务器集群的资源状况进行资源分配。Yarn内置了几种资源调度算法，包括Fair Scheduler,Capacity Scheduler等，用户可以自定义资源调度算法在Yarn上使用。
</p>
  <p style="text-indent:2em">  Yarn进行资源分配的单位是容器（Container），每个容器包含一定量的内存、CPU等计算资源，默认配置下，每个容器包含一个CPU核心。容器由NodeManager进程启动和管理，NodeManager进程会监控本节点上容器的运行状况并向ResourceManager进程汇报。</p>

- 调度器
```
1.FIFO Scheduler:先进先出 （按照作业提交时间先后顺序）

2.Fair Scheduler: 公平调度器 （负载均衡）

3.Capacity Schedule:能力调度器  (多用户调度器)

```



<p style="text-indent:2em">
    应用程序管理器负责应用程序的提交，监控应用程序运行状态等。应用程序启动后需要运行一个ApplicationMaster,ApplicationMaster也需要运行在容器里面。每个应用程序启动后会先启动自己的ApplicationMaster，由ApplicationMaster根据应用程序的资源进一步向ResourceManager进程申请容器资源，得到容器以后就会分发自己的应用程序代码到容器上启动，进而进行分布式计算。
    </p>


#### 以一个MapReduce程序为例，Yarn的整个工作流程
****
1. 向Yarn提交编写的MapReducey应用程序，包括MapReduce ApplicationMaster、MapReduce程序以及MapReduce Application启动命令。

2. ResourceManager进程和Node进程通信，根据集群资源，为用户分配第一个容器，并将MapReduce ApplicationMaster分发到这个容器上面，Container里启动ApplicationMaster。

3. MapReduce ApplicationMaster启动后向ResourceManager注册，并为自己的应用程序 __申请容器资源__。

4. MapReduce ApplicationMaster申请到需要的容器后，立即和相应的NodeManager通信，将MapReduce程序分发到NodeManager进程所在服务器，在服务器容器中运行。

5. Map或Reduce任务在运行期和MapReduce ApplicationMaster通信，汇报自己的运行状态。

#### Yarn框架实现资源的统一调度管理
****
<p style="text-indent:2em">
MapReduce如果向在Yarn上运行，就需要遵循Yarn规范的MapReduce ApplicationMaster。
</p>
<p style="text-indent:2em">
其它大数据框架也可以遵循Yarn规范的ApplicationMaster，这样在一个Yarn集群中可以同时并发执行各种不同的大数据计算框架，实现资源的统一调度管理。
</p>
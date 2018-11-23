---
title: MapReduce优化Combiner和Partitioner
data: 2018-11-19 16:00:00
tags: 分布式 
commnents: true
categories: 分布式编程框架
---

#### 概述
****
##### Combiner（Map阶段对MapTask进行局部汇总）
1. Combiner是MapReduce程序中Mapper和Reducer之外的一种组件。
2. Combiner组件的父类是Reducer。Combiner extends Reducer。
3. Combiner和Reducer的区别在于运行的位置：
```
        Combiner是在每个MapTask所在的节点运行
        Reducer是接收全局所有Mapper的输出结果
```
4. Combiner的意义是对 _每一个MapTask的输出进行局部汇总_ ，以减少 __网络传输量__。
```
具体的实现步骤：
        1.自定义一个Combiner继承Reducer,重写reduce方法
        2.在job中设置:job.setCombinerClass(xxxxCombiner.class).
```
5. Combiner能够应用的前提是不能影响最终的业务逻辑，Combiner的输出Key-Value需要与Reducer的输入Key-Value类型对应。


##### Partitioner（分发到的Reduce分区，可自定义分区）
1. MapReduce会将map输出的Key-Value对，按照相同key分组，然后分发给不同的ReduceTask,默认分发规则为:按key的hashcode%reducetask数来分发。
```
  public int getPartition(K2 key, V2 value,int numReduceTasks) {
    return (key.hashCode() & Integer.MAX_VALUE) % numReduceTasks;
  }
```
2. 如果需要按照我们自己的需求进行分组，则需要改写数据分发组件Partitioner自定义Partitioner继承抽象类:Partitioner,重写getPartition方法。
```
class MyPartitioner extends Partitioner{

    @Override
    public int getPartition(Text key,xxx value,int numPartitions){
    }
}
```
3. 然后在job中设置自定义partitioner:
```
job.setPartitionerClass(xxxxPartitioner.class);
```


#### 代码实现
****
- 自定义Combiner
```
/**
 * @author afeng
 * @date 2018/11/19 17:43
 * <p>
 * Combiner是在map阶段对MapTask任务进行局部汇总
 **/
public class WordCountCombiner extends Reducer<Text, IntWritable, Text, IntWritable>
{

    IntWritable v = new IntWritable();

    @Override
    protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException
    {

        int sum = 0;
        for (IntWritable value : values)
        {
            sum += value.get();
        }
        v.set(sum);
        context.write(key, v);
    }
}
```


#### Combiner的优点
****
- 对每一个MapTask进行局部汇总，减少网络IO传输数量。
```
一个Map任务执行完毕，Key-Value为:
        <hadoop,1> <hadoop,1> <hadoop,1> <hadoop,1>

如果不用Combiner，传给Reducer的则是：
        <hadoop,1> <hadoop,1> <hadoop,1> <hadoop,1>

用了Combiner，则Map阶段输出的是:
        <hadoop,4>   //显然网络传输的数据量减少了
```
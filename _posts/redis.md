---
title: Redis入门
data: 2018-11-18 15:00:00
tags: NoSQL
commnents: true
categories: 数据库
---

### Redis概述
1. Redis是单线程（读写内存）模型。
2. 线程是用来读写内存的。
3. Redis持久化的方式:rdb(内存快照)和aof（追加日志）。
4. Redis的数据结构:

<p style="text-indent:2em">
<i>Redis所有的数据结构都是以唯一的key字符串作为名称</i>，然后通过这个<b>唯一的key值</b>来获取相应的value数据。不同类型的数据结构的差异就在于<b>value的结构不一样</b>。



#### string（字符串）
<p style="text-indent:2em">
Redis最简单的数据结构。
</p>


```
set name wangafeng
set age  18
exists name
del    name
del age
```


<p style="text-indent:2em">
缓存用户信息，将用户信息使用JSON序列化为字符串，存入Redis来缓存。当用户从Redis中取数据时，会经过一次反序列化过程。
</p>


****

#### list(列表)

<p style="text-indent:2em">
类似于Java中的LinkedList，是链表，则插入和删除操作非常快。当列表弹出最后一个元素之后，该数据结构被自动删除，内存被回收。
</p>

右进左出：队列(先进先出)
```
rpush books python java golang
llen books      //3
lpop books      //python
lpop books      //java
lpop books      //golang
```
右进右出：栈（先进后出）
```
rpush books python java golang
rpop books     //golang
rpop books     //java
rpop books     //python
```


****
#### set(集合)
<p style="text-indent:2em">
相对于Java中的HashSet，它内部的键值对是无序且唯一的。内部实现是一个特殊的自动，字典中的所有value的值都是null。
</p>

```
sadd books python
sadd books java
sadd books golang

smembers books    //由于是无序的，所有插入于查看并不一致

sismemeber books java //查询某个value是否存在

scard books     //获取长度

spop books          //弹出一个

```
##### set集合的应用场景
<p style="text-indent:2em">
去重功能，可以用来存储活动中奖用户ID,去重，可以保证同一个用户不会中奖两次。</p>


****

#### zset（有序集合）（跳跃链表实现的有序集合，score进行排序）

<p style="text-indent:2em">
类似于Java中的SortedSet和HashMap的集合体，一方面它是set，保证了内部value的唯一性。另一方面，它可以给每个value赋予一个score,代表这个value的排序权重。

<b>zset</b>内部实现是一种叫做<b>跳跃链表</b>的数据结构。
</p>

```
zadd key score  value   //score代表一个权重，排序的权重

zadd student 100 "afeng"
zadd student 99  "shanji"
zadd student 98  "zhanpeng"
zadd student 97  'shihao'

zrange student 0 -1   //默认升序排列
zrevrange student 0 -1 //降序排列
```
<p style="text-indent:2em">
zset，可排序去重集合，可以用来存储粉丝列表，value值是粉丝的用户ID,score则是关注时间，可以对粉丝列表按关注时间进行排序。
</p>

##### zset有序集合的应用场景
<p style="text-indent:2em">
zset还可以用来存储学生的成绩，value是学生的id,score是他们的考试成绩。可以按分数进行排序得到他们的名次。
</p>

****


#### hash(字典)
<p style="text-indent:2em">
类似于Java中的HashMap，无序字典。不同的是，Redis中字典的值只能是字符串。
</p>

```
hset  books java "think in java"
hset  books golang "concurrency in go"
hset  books python "python cookbook"

# 获取所有字典
hgetall books

#长度
hlen  books 

#通过value中的key获取value中的value
hget books java
hget books python
hget books golang


# 批量设置
hmset books java "effective java" python "learning python"

```


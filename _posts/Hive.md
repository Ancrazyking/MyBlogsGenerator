---
title: Hive数据仓库
data: 2018-11-18 15:00:00
tags: hadoop
commnents: true
categories: 分布式
---
### 概述
****
<p style="text-indent:2em">
Hive是<b>基于Hadoop</b>的一个数据仓库工具，可以将结构化的数据文件映射为一张数据库表，并提供完整的sql查询功能。
</p>
<p style="text-indent:2em">
Hive定义了简单的类SQL查询语言，可以将<b>sql语句转换为MapReduce任务</b>进行运行，不必专门的MapReduce。让不熟悉MapReduce开发人员也能编写数据查询语言。
</p>

#### 数据仓库
<p style="text-indent:2em">
<b>历史数据的存储</b>，如关系型数据库里以前的数据。用来做<b>数据分析的</b>。
如电商存储的历史订单，存入数据仓库，通过对历史数据的分析，查看用户的喜好，进一步对该用户进行推荐等处理。
</p>

<p style="text-indent:2em">
数据仓库容量很大，宽表。冗余数据。不需要遵循数据库三范式。
</p>

#### 数据模型
1. 星型模型



2. 雪花模型

### Hive的安装
****
MySql等其他可以用于保存Hive的元数据，如Hive的位置信息，url等。
Hive默认的保存元数据信息的是单用户的，只能连接一个客户端。

```

# 然后可以通过将文件上传至该数据库的表目录下
create table student(id int,name string)
row format delimited        #按行切分
fields terminated by ',';   #每行字段按，分隔

#扫描表，不跑MapReduce
select * from student;
select * from student where id>3;


# 会开启一个MapReduce
select count(*) from student;

```

Hive可以作为一个thrift服务，可以通过客户端（beeline）去连接。


### Hive基本操作
****
#### DDL操作(create alter drop)
- 创建表 语法
```
CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name 
   [(col_name data_type [COMMENT col_comment], ...)] 
   [COMMENT table_comment] 
   [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)] 
   [CLUSTERED BY (col_name, col_name, ...) 
   [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS] 
   [ROW FORMAT row_format] 
   [STORED AS file_format] 
   [LOCATION hdfs_path]
```
- 修改表 语法
```
alter table student add partition(country='China');
```



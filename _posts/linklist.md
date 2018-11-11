---
title: 数据结构-链表
data: 2018-11-09 21:00:00
tags: 数据结构-线性表
commnents: true
categories: 数据结构
---
注:图片源自极客时间的[数据结构与算法之美专栏](https://time.geekbang.org/)
### 链表的定义
与数组需要连续空间不同,链表不需要内存中连续的内存空间,而是通过指针将一组零散的内存块串联起来使用.
****
#### 单链表
****
链表通过指针将一组零散的内存块串联在一起,其中,我们把内存块称为链表的"结点".
![SingleLinkedList.jpg](https://static001.geekbang.org/resource/image/b9/eb/b93e7ade9bb927baad1348d9a806ddeb.jpg)

单链表中有两个结点是比较特殊的,第一个结点和最后一个结点.
```
头结点用来记录链表的基地址
尾结点的next指针域指向一个空地址NULL.
```
链表支持数据的查找、 插入和删除操作
![linkedlist.jpg](https://static001.geekbang.org/resource/image/45/17/452e943788bdeea462d364389bd08a17.jpg)




#### 循环链表
****
一种特殊的单链表,与单链表唯一的区别就是尾结点.
单链表的尾结点指向NULL空地址,而循环链表的 _尾结点指向链表的头结点_.
![CircularLinkedList.jpg](https://static001.geekbang.org/resource/image/86/55/86cb7dc331ea958b0a108b911f38d155.jpg)
```
与单链表相比,循环链表的可以处理具有环形结构的数据(约瑟夫环的问题).
```

#### 双(向)链表
****
单链表只有一个方向,结点只有一个后继指针next指向后面的结点.而双向结点有两个指针,前驱指针 __previous__ 和后继指针 __next__ .
![DoubleLinkedList.jpg](https://static001.geekbang.org/resource/image/cb/0b/cbc8ab20276e2f9312030c313a9ef70b.jpg)

#### 双向循环链表
****
![CircularDoubleLinkedList.jpg](https://static001.geekbang.org/resource/image/d1/91/d1665043b283ecdf79b157cfc9e5ed91.jpg)



### 链表与数组的性能
- 数组
```
插入删除 O(n)  
随机访问 O(1)
```
- 链表
```
插入删除 O(1)
随机访问 O(n)
```


### [部分代码实现(Java语言)](https://github.com/Ancrazyking/DataStructureAndAlgorithm)


























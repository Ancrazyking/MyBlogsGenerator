---
title: 锁,可重入锁与分布式锁
data: 2018-11-17 15:00:00
tags: 并发编程
commnents: true
categories: 并发编程
---
### 1. 概述
****
<p style="text-indent:2em">Java中的synchronized关键字和ReentrantLock可重入锁在代码中是经常见的,一搬用于多线程环境中控制对资源的并发访问。随着分布式的发展,本地的加锁往往不能满足我们的需要,于是出现了各种各样的分布式锁。
</p>

### 2. Java中的锁及同步机制
****
#### 2.1 内置锁synchronized关键字
****
<p style="text-indent:2em">
Java提供一种内置的锁机制来支持原子性:同步代码块。同步代码块包含两部分:一个作为锁的对象引用,一个作为由这个锁保护的代码块。
每个Java对象都可以作为一个实现同步的锁,这些锁被称为内置锁(Intrinsic Lock)或监视器(Monitor Lock)。
</p>
<p style="text-indent:2em">
线程在进入同步代码块之前自动获得锁,并在退出同步代码块时自动释放锁。
或得内置锁的 <i>唯一路径</i> 就是 <b>进入由这个锁包含的同步代码块或方法</b> 。
</p>
<p style="text-indent:2em">
Java内置锁是一种互斥锁,最多只有一个线程能持有这种数据。例如当线程A尝试获取一个由线程B持有的锁时,线程A必须等待或阻塞。直至线程B释放这个锁。如果B永远不释放锁，那么A将永远等下去。
</p>


- 重入
<p style="text-indent:2em">
当某个线程请求一个有其他线程持有的锁时，发出请求的线程就会阻塞。然而，由于内置锁是可重入的，因此如果某个线程 <i>试图获得一个已经由它自己持有的锁</i>时，这个请求就会成功。
</p>
<p style="text-indent:2em">
<b>synchronized内置锁是可重入的。</b>
</p>


#### 2.2 volatile变量（防止指令重排序，对所有线程的可见性）
****
<p style="text-indent:2em">
Java语言提供的一种稍弱的同步机制，即volatile变量，用来确保将<b>变量的更新操作通知到其他线程</b>。
</p>
<p style="text-indent:2em">
volatile是一种比synchronized关键字更加轻量级的同步机制。
</p>
<p style="text-indent:2em">
<b>内置锁等加锁机制既能保证可见性又可以保证原子性，而volatile变量只能确保可见性。</b>
</p>

- JMM线程工作内存模型
![Java内存模型.jpg](http://wx2.sinaimg.cn/mw690/006pTdaLgy1fxb3gx4tv7j30ew052t8t.jpg)
- 主内存（内存）
<p style="text-indent:2em">
类似于计算机中的内存，主存被所有线程共享（如静态变量，堆内存中的实例）。
</p>

- 工作内存（缓存，解决cpu与内存速度不匹配）
<p style="text-indent:2em">
类似于CPU中高速缓存，对应一个共享变量来说，工作内存存储了该 <b>共享变量的副本</b>。
</p>

<p style="text-indent:2em">
线程对共享变量的所有操作必须在工作内存中进行，而不能直接读取主内存中的变量。由于线程私有的栈，不同线程之间无法访问彼此的工作内存，变量的值传递只能通过主内存进行。
</p>

- 原子性
<p style="text-indent:2em">
一些了不可被中断的操作。这一些操作要么全成功，要么全失败。如事务的提交与回滚。
</p>

- （内存）可见性
<p style="text-indent:2em">一个线程更新了某个共享变量的值，对所有线程都是可见的。</p>

-  指令重排序
<p style="text-indent:2em">
JVM在编译Java代码的时候，或者CPU在执行JVM字节码时，对现有的指令顺序进行重排序。编译器优化代码的执行顺序，使得代码能够更快的执行下去。
</p>


volatile关键字保证了 __内存可见性__， __阻止了指令重排序__ （内存屏障）。

关于指令重排序及内存屏障，这篇文章讲的很好。[漫画：什么是volatile关键字？（整合版）](https://mp.weixin.qq.com/s/DZkGRTan2qSzJoDAx7QJag)




#### 2.3 显示锁ReentrantLock
****
<p style="text-indent:2em">
Java5.0后增加了一种新的机制：ReentrantLock。ReentrantLock并不是一种替代内置加锁的方法，而是当内置锁synchronized机制不适用时，作为一种可选择的高级功能。
</p>
<p style="text-indent:2em">
与内置加锁机制不同的是，Lock提供了一种无条件的、可轮询的、定时的以及可中断的锁获取操作，所有加锁和解锁的方法都是显式（调用）的。
</p>

- Lock接口
```
public interface Lock{
    void lock;
    void lockInterruptibly() throws InterruptedException;
    boolean tryLock();
    boolean tryLock(long timeout,TimeUnit unit) throws InterruptedException;
    void unlock();
    condition newCondition();
}
```
- ReentrantLock实现了Lock接口
<p style="text-indent:2em">
提供了与synchronized相同的互斥性和内存可见性，而且提供了<b>可重入的加锁语义</b>。
</p>
<p style="text-indent:2em">
ReentrantLock提供了一种更加灵活的加锁机制。
</p>

```
Lock lock=new ReentrantLock();
...
lock.lock();
try{
    //更新对象状态
    //捕获异常
}finally{
    lock.unlock();
}
```


### 3. 分布式锁
****


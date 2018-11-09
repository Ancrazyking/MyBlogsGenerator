---
title: 睡眠排序 - 还有这种操作?好用就完事了!
data: 2018-11-10 15:00:00
tags: 算法
commnents: true
categories: 算法
---
##### 睡眠排序
 给定一组元素集合,根据集合中的每一个元素,开启一个线程.线程睡眠的时间等于该元素的值.
 - Java 代码的实现(需要实现Runnable接口)
 ```
public  class SleepSort implements Runnable{

private int num;

public SleepSort(int num){
    this.num=num;
}


@Override
public void run(){
    Thread.sleep(num);
    //睡眠结束后打印该值
    System.out.println(num);
}

public static void main(String[] args){
    int[] arr={1,10,50,500,10000,13,29,54,87,168,256};
    for(int a:arr){
        new Thread(new SleepSort(a)).start;
    }
}
}
 ```
- 睡眠排序
1. 运行时间取决于最大元素的值
2. 原理是构造n个线程,和元素中n个数一一对应.
    
    初始化后,线程们开始睡眠,睡眠完成后,则输出对应的元素.

3. 当集合中的元素过多时,线程数量会很多,系统资源开销会很大.

4. 想出这个排序算法的人


![genius.png](http://wx2.sinaimg.cn/mw690/006pTdaLgy1fx1t608bbfj31hc0u0adj.jpg)







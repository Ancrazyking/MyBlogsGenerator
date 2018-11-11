---
title: JVM进程启动时会启动哪些线程?
data: 2018-11-10 15:00:00
tags: JVM
commnents: true
categories: Java
---

### JVM进程启动时,必然会创建的线程
- JVM进程启动的触发条件:每当使用java命令执行一个带main方法的类.
- 启动JVM进程时必然会创建的线程
1. main         
```
主线程,执行启动类的main方法
```
2. Reference Handler
```
处理引用的线程
```
3. Finalizer
```
调用对象的finalize方法的线程,即垃圾回收的线程
```
4. Signal Dispatcher
```
分发处理发送给JVM信号的线程
```
5. Attach Listener
```
负责接收外部的命令的线程
```

### 编程查看JVM启动时创建的所有线程
```
package sort;

import java.lang.management.ManagementFactory;
import java.lang.management.ThreadInfo;
import java.lang.management.ThreadMXBean;

/**
 * @author afeng
 * @date 2018/11/9 22:13
 **/
public class JVMThread
{
    public static void main(String[] args)
    {
        ThreadMXBean threadMXBean= ManagementFactory.getThreadMXBean();
        ThreadInfo[] threadInfos=threadMXBean.dumpAllThreads(false,false);
        for(ThreadInfo threadInfo:threadInfos){
            System.out.println(threadInfo.getThreadId()+"-"+threadInfo.getThreadName());
        }
    }
}

```
- 输出结果如下

```
6-Monitor Ctrl-Break
5-Attach Listener
4-Signal Dispatcher
3-Finalizer
2-Reference Handler
1-main

Process finished with exit code 0
```


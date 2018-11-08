---
title: Scala 
date:  2018-11-07 21:30:00
tags:  函数式编程
comments: true
categories: Scala
---

### Scala函数和闭包

##### 方法
        定义函数最通用的方法是作为某个对象的成员.这种函数被称为方法(Method).
```
import scala.io.Source
object LongLines{
    def processFile(filename:String,witdh:Int){
        val source=Source.fromFile(filename)
        for(line <- source.getLines){
            processLine(filename,width,line)
        }
    }

    //
    private def processLine(filename:String,width:Int,line:String){
        if(line.length>width){
            println(filename+":"+line.trim)
        }
    }
}
```


##### 本地函数
        Scala提供一种方式,可以把函数定义在别的函数之内.
```
def processFile(filename:String,width:Int){
    //函数的嵌套
    def processLine(filename:String,width:Int,line:String){
        if(line.length>width)
            print(filename+":"+line)
    }
    val source =Source.fromFile(filename){
        for(line <- source.getLines>){
            processLine(filename,width,line)
        }
    }
}
```

##### Scala中的函数
        不仅可以定义和调用函数,还可以写成匿名的字面量(literal),并把他们作为值传递.

```
var increase=(x:Int)=> x+1   //     increase(int x)
increase(10)                //11
increase(11)                //12


val someNumbers=List(-11,-10,-5,0,5,10)

someNumbers.foreach((x:Int) => println(x))
someNumbers.foreach((x:Int)=> x>0)
```
##### 占位符用法
```
someNumbers.foreach(println(_))
someNumbers.filter(_ > 0)
```


##### 闭包(closure)
```
val addMore=(x:Int) => x + more  //编译不通过  more未定义


var more=1
val addMore=(x:Int) => x + more  //如果more变化,则函数也随之变化
```

##### 可变参数
```
def echo(args:String*){     //Java中 void echo(String... args)
    for(arg <- args>){
        println(arg)
    }
}
```
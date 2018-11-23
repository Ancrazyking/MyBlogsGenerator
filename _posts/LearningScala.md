---
title: Learning Scala
data: 2018-11-15 12:00:00
tags: Scala
commnents: true
categories: 函数式编程
---

### Scala概述


#### 1. Scala中变量,基本数据类型
****
- val 与 var
- 变量类型推断,编译器自动判断变量的类型
```
    //val修饰的变量赋值后不可改变
    val name="wangafeng"
    val name:String="wangafeng"
    //var修饰的变量赋值后可以改变
    // name="afengwang"  编译器报错

    var name="wangafeng"
    var name:String="wangafeng"
    name="afengwang"
```
- Scala中与Java中的数据类型一致,区别是无 _基本类型和包装类型_ 的区别.
- Scala中的数据类型首字母必须大写
- 所有基本类型的父类是 __AnyVal__ , 所有类的父类是 __Any__ (类似Java中的Object),AnyVal的父类也是Any.

- 隐式转换
```
val name="wangafeng"
println("my name is ${name}") //隐式转换  底层调用s方法,idea会在字符串前加s

//懒加载
lazy val name="wangafeng"
println(name)

```

- return 返回值:Scala中表达式的返回值就是最后一行代码的执行结果
- lazy  懒加载:在第一次使用时才会去初始化

##### 1.1 Scala中的操作符
与java类似,特殊的地方如下:
1. 每个操作符都是一个函数
2. 不支持三元运算符
3. 不支持 --  ++
4. 除了:操作符,其他都是从左往右进行连接
```
//每个操作符都是一个函数
val a=10
val b=20
val c=a.+(b) //val c=a+b   
```

#### 2. 程序控制流程结构
****

##### 2.1 条件判断 if - else
```
    if(Boolean_expression){

    }else if(Boolean_expression){

    }else{

    }
```

##### 2.2 循环 while ,do while ,for
- while
```
while(condition){
    conditional code;
}
```
- do-while
```
    do{


    }while(condition)
```

- for
```
for( i <- 1 to 10){
    println(i)
}


//for循环的守护模式
for(i <- 1 to 10> if i%2==0){
    println(i)
}

//打印九九乘法表
for(i <- 1 to 9;j <- 1 to i){
    print("...")
}


```

- break,continue,Scala中不支持.需要引入 __Breaks__ 类
```
import scala.util.control.Breaks
object App{
    def main(args:Array[String]){
        var a=0
        val numList=1 to 10
        val loop=new Breaks
        loop.breakable{
            for( a <- numList ){
                println("Value of a: ${a}")

            }
            if(a == 4){

                loop.break
            }
        }
        println("After the loop")
    }
}
```



- 循环表达式
```
    Range是左闭右开区间;[ )    //Range是一个类
    until是左闭右开区间;[ )    //until是一个方法
    to是左闭右闭区间;   [ ]    //to是一个方法
```
![循环表达式.png](http://wx1.sinaimg.cn/mw690/006pTdaLgy1fx91o7jmo7j30s607emxa.jpg)

- yield关键字,基于一个已有的集合来构建一个新的集合
```
       val arrays=Array("hadoop","strom","spark")
   
       val arraysUpper= for(array <- arrays) yield{
            array.toUpperCase
        }

      println(arraysUpper.mkString("\t"))
```


#### 3. 集合(Array List Set Map) 不变(默认)与可变集合
****
##### 3.1 Tuple元组
<p style="text-indent:2em">元组可以装着多个不同类型的值,下标是从1开始的,最多支持22元组(22中类型元素)
<B>获取元组中的值:a._1  a._2 a._n</B>
</p>

```
val tuple=("Hadoop","Storm","Spark",1,10,100)
println(tuple._1)  //下标从1开始
println(tuple._2)  
println(tuple._3)

//一元组,一种类型元素
val tuple1=Tupl1.apply(2)


//二元组,二种类型元素
val tuple2=("wangafeng","21")

通过._+下标访问(下标从1开始)

```

##### 3.2 Array数组
- 不变数组 Array
```
val arr=new Array[Int](8)
println(arr)
println(arr.toBuffer)

val arr3=Array("hdaoop","storm","spark")
println(arr3(2))   //与java访问数组中元素不同的是,是通过()来访问的.
```

- 可变数组 _ArrayBuffer_
```
val arrayBuffer=ArrayBuffer[Int]()

arrayBuffer +=1                 //尾部追加一个元素

arrayBuffer +=(2,3,4,5)         //追加多个元素

arrayBuffer ++=Array(6,7)       //追加一个数组
```

##### 3.3 List集合
- 不变集合List
```
val list=List(1,2,3,4,5)  //双向链表? 
list.head
list.tail

val list1=1:Nil //将1添加到Nil集合前面
val list2=2:list1 

```

- 可变集合 _ListBuffer_
```
val mutableList=scala.collection.mutable.ListBuffer[Int]()
mutable +=1
mutable +=2
mutable +=(3,4,5)
mutable ++= List(6,7,8,9,10)
```




##### 3.4 Set集合(无重复元素,无索引)

```
val set=Set(1,2,3,4,5)

//判断该数据是否存在集合中
val flag=set(10)   //false
val flag1=set(5)   //true

//基于数据判断规则,可以模拟数据的增删
set(123)=true
set(1)=false


Option类型:有值为Some,无值为None

```


##### 3.5 Map集合
```


//遍历Map集合
for(item <- map){
    val key=item._1
    val value=item._2
    println("key=${key},value=${value}")
}

for((key,value) <- map){
    println("key=${key},value=${value}")
}


//模式匹配
map.map{
    case(key,value) => {
         println("key=${key},value=${value}")
    }
}
```





##### 3.6 集合常用API说明 (map  reduce)
1. foreach() 遍历每一个元素
```
val list=(1 to 20).toList
list.foreach(e=>println(e)) //换行打印每个元素
``` 
2. map()
```
list.map(item => item*2) //list中每个元素值*2
```

3. flatMap()   //将集合压扁为多个元素
```
list.flatMap( item => (0 to item).toList)  //返回多个值,最小单位
```
4. filter()

```
list.filter(_ %2==0) //找到所有能被2整除的数
```

5. reduce()  //聚合

```
list.reduce((a,b)=>a+b )  //a表示是累加的值,b是当前元素的值

```
6. fold()  //聚合,需要指定第一个初始值
```
list.fold(0).reduce((a,b)=>a+b //210
list.fold(90).reduce((a,b)=>a+b) //300
```
7. sorted() //排序
```
val list=List(10,8,9,6,7,4,5,3,1,2)
list.sorted()
```

8. sortBy()
```
list.sortBy(item => item.toString) //按照数字进行排序1,10 2,20 3,31,32
```

9. groupBy() //按照给定的函数对数据进行分组,根据函数返回类型进行分组

```
list.groupBy(item => item%2==0)
//Map(false -> List(1,3,5,,7,9),true -> List(2,4,6,8,10)
```








#### 4. Scala函数式编程
****
##### 4.1 基本函数
注意:def定义的只是方法 可以通过方法名_ 进行方法向函数的转换

```
// 定义方法的关键字def  后面的max则是方法名  括号内则是参数
def max(x:Int,y:Int):Int={   //定义一个max的方法
    if(x>y)
        x
    else
        y
}

//f:T => R  f:函数名称 T参数类型 R返回数据类型
max:Int => Int
```
函数与方法的区别?
<p style="text-indent:2em">
如果一个函数在类或者对象中,那么称之为方法.
</p>
方法与函数的转换?(下划线的转化)  
<p style="text-indent:2em"><b>语法规定,将函数赋值给变量时,必须在函数后面加上空格和下划</b><p>
<p style="text-indent:2em">
val a =max _ ; 通过_将函数作为值进行传递
</p>



- 局部函数
<p style="text-indent:2em">出现在函数内部的函数,作用域只能是函数内部</p>

```
def f():Unit{
    g

    def g():Unit{
        println("内部函数")
    }

    println("外部函数")
}
```

##### 4.2 匿名函数:没有名称的函数,定义的时候不需def,需要=>定义方法体
Scala中定义匿名函数的语法规则就是:(参数名:参数类型)=>函数体
<p style="text-indent:2em">
    val sayHelloFunc=(name:String)=>println("Hello,"+name)//通过将它赋值进行调用
</p>


```
//一个匿名函数的例子,没有函数名字的函数,可以通过赋值调用该匿名函数
//匿名函数通常是用 => 来定义的
(x:Int,y:Int) => {
    if(x>y) x else y
}
```


##### 4.3 高阶函数(函数参数类型是匿名函数的)
- 可以将某个函数传入其他函数,作为参数.
- 接收 __其他函数作为参数的函数__ ,被称作高阶函数

```
//定义一个高阶函数
def greeting(name:String,func:String=>Unit)={
    func(name)
    //方法体里面调用了参数中的方法
}


def main(args:Array[String]):Unit{
    def sayHelloFunc(a:String):Unit{
        println("name=${a}")
    }

    //调用高阶函数
    greeting(sayHelloFunc_)
}


//定义一个高阶函数用于两个数的+-*/运算
def opera(x:Int,y:Int,func:(x:Int,y:Int)=>Int){
    println(result+"${func(x,y)}")
}
opera(1,2,(x,y)=>x+y)
opera(1,2,(x,y)=>x-y)
opera(1,2,(x,y)=>x*y)
opera(1,2,(x,y)=>x/y)

//可以用_符号代表一个数
opera(1,2,_+_)
opera(1,2,_-_)
opera(1,2,_*_)
opera(1,2,_/_)


//Scala常用高阶函数
Array(1,2,3,4,5).map(2*_)
(1 to 9).map("*"*_).foreach(println_)
(1 to 20).filter(_%2==0)
(1 to 9).reduceLeft(_ * _)   //1*2*3*4*5*6*7*8*9
```



#### 5. 面向对象编程
- Java

```
class       类 
            属性
            方法
            静态属性
            静态方法
interface   接口

enum        枚举
```

- Scala(无static关键字)
```
class      类,和java是一样的,区别是无静态方法和关键字
        
object     单例,为了解决class没有static修饰的问题

trait      特质,类似与java的接口来使用
           >>  可以包含已经实现的方法或者属性
           >>  一个类可以继承(实现)多个特质
```
##### 5.1 Scala中类修饰符,作用域
- public:    默认,公共的
- protected:  受保护的,当前包中可以使用.scala中不推荐使用
- private:      私有的
```
    private[this] > private private class  > private[scala]包
```
##### 5.2 class中的构造方法
1. 默认有一个无参构造函数   class Person(){}  //无参构造函数,默认
2. 可以有多个辅助构造函数
3. 主构造函数,类中所有可执行的代码 class Person(name:String,age:Int) //主构造函数
4. 主构造函数中定义的参数,最终作为class的属性存在
5. 辅助构造函数: _以this命名的函数_ , 无返回值,第一行必须调用主构造函数
```
class Person(var name:String,age:Int){

//辅助构造函数,方便实例化
def this(){
    this("张三",8)
}
def this(n:Int){
    this()
    this.age=n
}
}
```

6. 对象构建初始化过程
```
a) 首先初始化父类
b) 然后初始化子类
c) 首先初始化主构造函数
d) 然后初始化辅助构造函数
```
##### 5.3 object伴生对象
<p style="text-indent:2em">如果有一个class,还有一个与class同名的object,那么就称这个object是class的伴生对象,class是object的伴生类.
</p>
<p style="text-indent:2em">
<b>可以互相访问私有的方法和属性</b>
</p>

- apply方法:快速构建实例化

<p style="text-indent:2em"> 定义在object中,表示提供了一种便捷的对象创建的方式,创建对象是apply方法返回的数据类型.
</p>

```
val array=Array(1,2,4)  //调用Array伴生对象的apply方法,快捷的对象创建方式
```
<p style="text-indent:2em"> 定义在class中,方便获取数据的值
</p>

```
val array=Array(1,2,4)
println(array(1)) //1
```

- update方法
    定义在class中,便捷的数据插入,更新方式

##### 5.4 case class 案例类
<p style="text-indent:2em">
class与object的整合,便捷的创建对象
</p>

```
case class student(name:String,age:Int)  //不需要写apply方法
//默认生成伴生对象
//以下代码是默认生成的
object student{
    def apply(name:String,age:Int)=new Student(name,age)
}

```

##### 5.4 trait特质
<p style="text-indent:2em">
Scala中的class是单继承的(extends),可以实现多个trait(with 不同java中的implements).
</p>

- override关键字,重写trait的方法
```
trait A{
}
trait B{
}
trait C{
}
class D extends A with B with C with D{
}
```

#### 6. 模式匹配(match)
****
<p style="text-indent:2em">
类似与java中的switch ... case;
</p>

1. __match__ 关键字
2. 类型匹配

```
object MatchCase{
    def f(n:Int):Unit={
    n match
        case 1 => println(1)
        case 2 => println(2)
        case _ => prinln(n)
      }   
    }


    //类型匹配
    def f2(throwable:Throwable)={
        throwable match{
            case e:IllegalArgumentException => println("参数异常")
            case e:IOException  => println("IO异常")
            case _ => println("其他异常")
        }
    }
}

    //类匹配
    case class student(name:String,age:Int)
    object student{
    }

```


#### 7. 类型系统(泛型)
****
- 泛型
<p style="text-indent:2em">与Java一致，区别是Java中使用<>,scala中使用[ ]。
泛型可以加在类和方法后面。
</p>

- 上下界
<p style="text-indent:2em">
[A1 > : A]    => A是A1的子类，
</p>
<p style="text-indent:2em">
[A1 < : A]    => A1是A的子类，
</p>

- 协变/逆变
 <p style="text-indent:2em">
</p>
 <p style="text-indent:2em">
</p>
 <p style="text-indent:2em">
</p>



#### 8. 隐式转换
****
 <p style="text-indent:2em">
类本身没有的方法，可以通过隐式转换获取其它类的方法。
</p>
 <p style="text-indent:2em">
只能转一次。
A->B，
B->C，
A->C （不可以）。
</p>

定义隐式转换函数(implicit)
```
implicit def inToString(n:Int){
    n.toString
}


//一个隐式转换的例子

def top(n:Int)(implicit ord:Ordering[T])

result.top(5)(ord=new scala.math.Ordering[String,Int](){
    val t1=x._2.compare(y._2)
    if(t1!=0){
        t1
    }else{
        val t2=x._1.compare(y._1)
        t2
    }
})
```






---
title: Spring相关
data: 2018-11-10 15:00:00
tags: Java
commnents: true
categories: 框架
---
### Spring Bean
在Spring中,那些组成应用程序主体及有Spring IOC容器所管理的对象,被称为 __bean__ .
简单来说,bean就是由IOC容器初始化,装配及管理的 __对象__.

Spring中的bean默认是单例(Singleton)的,即每个JVM中只有一个实例.


#### bean的作用域(@Scope("singleton"))
- singleton(默认)   ->     单例,每个Spring IOC容器仅存在一个Bean实例
- prototype         ->     原型,每次从容器中调用getBean()时,返回不同的实例
- request           ->     Http请求,每次Http请求都会创建一个新的Bean
- session           ->     HttpSession,同一个HttpSession共享一个Bean,不同Session使用不同Bean.
- globalsession    ->    


#### bean的生命周期

可通过bean的配置文件中配置  init-method 和 destory-method
1. Spring容器初始化
2. 从容器中获取Bean
3. Spring容器关闭
****

### Spring IOC与AOP

#### IOC(控制反转)
将Java对象的创建权交给Spring容器,而不是由程序员自己创建,对象的生命周期由Spring管理.
- 如何实现

```
//读取xml文件,通过工厂模式和Java的反射机制,实现程序的解耦.
// xml文件
<bean id="userService" class="xx.xx.xx.UserServiceImpl"/>

//测试代码
@Test
public void testIOC(){
   ApplicationContext applicationContext=new ClasspathXmlApplicationContext("applicationContext.xml");
   UserService userService=(UserService)applicationContext.getBean("userService");
   userService.save();
}
```

- Spring IOC初始化过程

![IOCInit.png](https://raw.githubusercontent.com/Ancrazyking/MdImage/master/IOCInit.png)

#### AOP(面向切面编程)
用于增强方法,在 __不修改源代码__ 的情况下,对其代码进行增强.
- 思想
```
AOP思想的实现是基于代理模式,Java中一般采用动态代理模式.
JDK动态代理模式只能代理接口而不能代理类,Spring AOP同时支持CgLib,Aspectj,JDK动态代理.

> 如果目标对象的实现类实现了接口,Spring AOP将会采用JDK动态代理来实现AOP代理类.
> 如果目标对象的实现类没有实现接口,Spring AOP将会采用Cglib来生成AOP代理类.

```
- 5种类型的通知
```
Before
After
After-returning
After-throwing
Around
```

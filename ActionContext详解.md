---
title: ActionContext详解
date: 2016-05-26 01:15:57
tags:

	- Struts2
	- Ssh

---



## ActionContext

ActionContext是Action的上下文，Struts2自动在其中保存了一些在Action执行过程中所需的对象，比如session, parameters, locale等。Struts2会根据每个执行HTTP请求的线程来创建对应的ActionContext，即一个线程有一个唯一的ActionContext。因此，使用者可以使用静态方法ActionContext.getContext()来获取当前线程的ActionContext，也正是由于这个原因，使用者不用去操心让Action是线程安全的。

<!--more-->

无论如何，ActionContext都是用来存放数据的。Struts2本身会在其中放入不少数据，而使用者也可以放入自己想要的数据。ActionContext本身的数据结构是映射结构，即一个Map，用key来映射value。所以使用者完全可以像使用Map一样来使用它，或者直接使用Action.getContextMap()方法来对Map进行操作。



Struts2本身在其中放入的数据有ActionInvocation、application（即ServletContext）、conversionErrors、Locale、action的name、request的参数、HTTP的Session以及值栈等。完整的列表请参考它的Javadoc。



由于ActionContext的线程唯一和静态方法就能获得的特性，使得在非Action类中可以直接获得它，而不需要等待Action传入或注入。需要注意的是，它仅在由于request而创建的线程中有效（因为request时才创建对应的ActionContext），而在服务器启动的线程中（比如fliter的init方法）无效。由于在非Action类中访问其的方便性，ActionContext也可以用来在非Action类中向JSP传递数据（因为JSP也能很方便的访问它）。



## ValueStack与ActionContext的联系和区别：

相同点：它们都是在一次HTTP请求的范围内使用的，即它们的生命周期都是一次请求。

不同点：值栈是栈的结构，ActionContext是映射（Map）的结构。

联系：ValueStack.getContext()方法得到的Map其实就是ActionContext的Map。查看Struts2的源代码可知（Struts2.3.1.2的org.apache.struts2.dispatcher.ng.PrepareOperations的第79行，createActionContext方法），在创建ActionContext时，就是把ValueStack.getContext()作为ActionContext的构造函数的参数。所以，ValueStack和ActionContext本质上可以互相获得。

注意：在一些文档中，会出现把对象存入“stack‘s context”的字样，其实就是把值存入了ActionContext。所以在阅读这些文档时，要看清楚，到底是放入了栈结构（即值栈），还是映射结构（值栈的context，即ActionContext）。



## 如何获得ActionContext：

在自定义的拦截器中：使用ActionInvocation.getInvocationContext()或者使用ActionContext.getContext()。

在Action类中：让拦截器注入或者使用ActionContext.getContext()。

在非Action类中：让Action类传递参数、使用注入机制注入或者使用ActionContext.getContext()。注意：只有运行在request线程中的代码才能调用ActionContext.getContext()，否则返回的是null。

在JSP中：一般不需要获得ActionContext本身。



## 如何向ActionContext中存入值：

在拦截器、Action类、非Action类等Java类中：使用ActionContext.put(Object key, Object value)方法。
在JSP中：标签< s:set value="..."/>默认将值存入ActionContext中（当然，< s:set>标签还可以把值存到其他地方）。另外，许多标签都有var属性（以前用的是id属性，现在id属性已被弃用），这个属性能向ActionContext存入值，key为var属性的值，value为标签的value属性的值。（有些文档写的是向ValueStack的context存入值，其实是一样的）

## 如何从ActionContext中读取值：

在拦截器、Action类、非Action类等Java类中：使用ActionContext.get(Object key)方法。

在JSP中：使用#开头的Ognl表达式，比如< s:property value=" #name"/>会调用ActionContext.get( "name")方法。注意：如果某标签的属性默认不作为Ognl表达式解析，则需要使用 %{ }把表达式括起来，于是就会出现类似"% { # name } "的表达式 。

总之，在JSP中使用ActionContext一方面是由于它是映射结构，另一方面是能读取Action的一些配置。当你需要为许多Action提供通用的值的话，可以让每个Action都提供getXXX()方法，但更好的方法是在拦截器或JSP模板中把这些通用的值存放到ActionContext中（因为拦截器或JSP模板往往通用于多个Action）。



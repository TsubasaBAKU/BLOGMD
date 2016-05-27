---
title: ActionContext详解2
date: 2016-05-26 01:42:34
tags:
	- Struts2
	- Ssh
categories:
	- SSH框架
---

## ActionContext

web标准的存放数据的范围有


* pageContext域

* request域
<!--more-->

* session域

* application域（servletcontext）



Struts2自己又定义了一个容器来存放数据，即ActionContext。
* ActionContext是个map集合，它持有了4个标准域，通过它可以获取四个标准容器，不仅如此，它还引用了其它Struts2自己定义的对象：

![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/TsubasaBAKU.github.io/master/img/ActionContext.png)

## ActionContext的几个特点

* ActionContext是Struts2新设计的存储数据的容器，是个map集合

* 我们可以通过ActionContext方便的获取到其它容器，如同一个快捷方式，是Struts2的数据中心

* ActionContext是threadloacl线程绑定的，当Struts2收到请求后，会马上创建一个actionContext，然后各个位置都可以访问到它

* 意思是action运行的上下文，负责存储action运行时产生的数据

* ActionContext.getContext():获取ActionContext里的大map集合，ActionContext.getContext().getValueStack()获取值栈
---
title: JVM内存分析
date: 2016-06-01 23:14:48
tags:
	- Java
	- JVM
categories:
	- JAVA
---

# JVA体系结构

JVM体系结构，老话题了。
<!--more-->
![!cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/BLOGIMG/master/JVM.png)

如上图所示，JVM主要包括两个子系统和两个组件。

**两个子系统：**

* Class Loader子系统：根据类的全限定类名来将类加载到运行时数据区的METHOD AREA中。我们可以通过extends java.lang.ClassLoader来编写自己的ClassLoader。

* Excution Engine子系统：执行引擎。执行classes中的指令。所有JDK的核心都是Excution Engine。不同JDK好坏主要取决于它们各自实现Excution Engine的好坏。

**两个组件**

* Native Interface组件：与native libraries交互，是与其他编程语言的接口。当调用native方法的时候，就进入了一个不受JVM限制的区域。

* Runtime Data Area：运行时数据区，就是我们常说的JVM内存。主要包括五个部分：

1. Heap：java堆，一个JVM实例只存在一个堆空间。
2. Stack：java栈，JVM对java栈只有两种操作：以帧为单位压栈、以帧为单位出栈。
3. Method Area：方法区，被装载的class信息存放在这个区域。当JVM装载某个类型时，它使用类装载器定位到相应的class文件，读入文件内容并传输到JVM中。
4. Program Counter：程序计数器。每个线程在启动时都会创建一个pc，总是指向下一条要执行的指令。
5. Native Method Stack：本地方法栈。保存的进入本地方法的地址。

**只有堆和方法区是线程共享的，栈，PC，本地方法栈都是以线程为粒度的，每个线程都拥有自己的部分。**


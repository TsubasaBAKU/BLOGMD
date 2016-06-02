---
title: Java类加载回顾
date: 2016-06-02 12:17:56
tags:
	-  Java
	-  类加载
categories:
	- JAVA
---

# 类加载过程

Java类加载有三个过程：装载，链接，初始化。链接又分为验证，准备和解析三个阶段，如下图：
![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/BLOGIMG/master/%E7%B1%BB%E5%8A%A0%E8%BD%BD%E8%BF%87%E7%A8%8B.png)
<!--more-->
(1)装载：查找并加载类的二进制信息。
(2)链接：
1. 验证：检验被加载类的信息的正确性。
2. 准备：为静态变量分配内存，并执行默认初始化。
3. 解析：将符号引用转化为直接引用

(3)初始化：为类的静态变量赋予正确的值

验证是为了保证字节码文件的正确性。由编译器生成的字节码文件肯定是符合JVM格式的，那如果有高手自己写一个字节码文件让JVM加载并运行呢？

# 类的初始化

类什么时候才会被初始化呢？有以下几种情况：
1. 创建类的实例，也就是new一个对象。
2. 访问某个类或接口的静态变量，或者为静态变量赋值。
3. 调用类的静态方法。
4. 发射。
5. 初始化一个类的子类，会先初始化它的父类。
6. JVM启动时表明的那个类，即和文件名相同的那个类。

类的初始化步骤：
1. 如果类还没有装载和链接，则先进行装载和链接操作。
2. 假如这个类存在父类，而父类还没有被初始化，那么先初始化父类。
3. 加入类中存在的初始化语句（如static变量和static块），并依次执行这些初始化语句。

# 类的加载

类的加载指的是将类的class文件二进制数据读入到内存中，并将其放在运行时数据区的方法区内，然后在堆区建立一个这个类的java.lang.Class对象，用来封装类在方法区的对象。
![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/BLOGIMG/master/JVM%E8%BF%90%E8%A1%8C%E6%97%B6%E6%95%B0%E6%8D%AE%E5%8C%BA.png)
![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/BLOGIMG/master/类加载堆区和方法区.png)

类加载的最终产品是位于堆区的Class对象

---
title: pageContext对象
date: 2016-05-26 00:28:36
tags:
	- Jsp
categories:
	- JAVA WEB

---

## pageContext对象

这个对象代表页面上下文，该对象主要用于访问JSP之间的共享数据。
<!--more-->
pageContext是PageContext类的实例，使用pageContext可以访问page、request、session、application范围的变量。

getAttribute(String name)：取得page范围内的name属性。

getAttribute(String name,int scope)：取得指定范围内的name属性，其中scope可以是如下4个值：

（1）PageContext.PAGE_SCOPE：对应于page范围。

（2）PageContext.REQUEST_SCOPE：对应于request范围。

（3）PageContext.SESSION_SCOPE：对应于session范围。

（4）PageContext.APPLICATION_SCOPE：对应于application范围。

## page范围

只在一个页面保存属性，跳转之后无效。page范围的对象被绑定到javax.servlet.jsp.PageContext对象中。


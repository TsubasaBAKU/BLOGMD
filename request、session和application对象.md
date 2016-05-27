---
title: request、session和application对象
date: 2016-05-25 23:48:18
tags:
	- Jsp
categories:
	- JAVA WEB
---

## 作用域：
 * request < session < application
<!--more-->

## 各自对应的接口：
 * request对象: javax.servlet.ServletRequest其子接口：javax.servlet.http.HttpServletRequest

 * session对象：javax.servlet.http.HttpSession

 * application对象：javax.servlet.ServletContext

## 三者共有的方法：
 * setAttribute(String name,Object object)

 * getAttribute(String name)

## 简单分析三者：
 * requset:调用上面两个方法，只有在一次请求上生效，当把信息返回给客户端时，该对象就销毁。

 * session:是服务器的行为，用于跟踪客户的状态。当用户去访问某个站点时，服务器端就会为客户产生一个sessionID，以cookie的方式返回给客户端，当客户去访问该站点的其他服务时，就会带着当前sessionID一起发出请求，以识别是哪个用户，一个用户就好比一个session对象，互不干扰。

![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/TsubasaBAKU.github.io/master/img/session.png)

session失效原因
(1) session过期
(2) 服务器端调用了invalidate(),让其失效
(3) <%@ page session="false" %>

session重要方法：
1) getId():获取session的id，每个id号都不同
(2) isNew():判断当前session是不是新的
3) invalidate():让当前session失效（登出）
4) setMaxInactiveInterval(int interval):设置session处于不活动的时间间隔，以秒为单位，超过该间隔，session失效。如果是负数，或者0，则不限制session活动时间间隔，默认一般是30分钟。（登录，购物车）

 * application:一个服务器就一个application对象，所有用户共享一个application，当服务器停止时，application被销毁（网站访问次数）

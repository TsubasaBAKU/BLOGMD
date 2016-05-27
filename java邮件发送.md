---
title: java邮件发送
date: 2016-05-24 20:16:20
tags:
	- Java
	- 邮件
categories:
	- JAVA
	- 编程功能模块
---
# 1.概述

## 1.1 邮件发送和接收用到的协议

 * SMTP:简单邮件传输协议（Simple Mail Transfer Protocol），属于TCP/IP协议簇，它是一组用于由源地址到目的地址传送邮件的规则。

 * POP3：邮局协议（Post Office Protocol - Version 3），属于TCP/IP协议簇，主要用于支持使用客户端远程管理在服务器上的电子邮件。
<!--more-->
## 1.2 邮件发送接收过程

![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/TsubasaBAKU.github.io/master/img/mail1.png)

# 2.建立邮件服务器

## 2.1 软件安装

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们使用软件来作为邮件服务器。这里我们使用[易邮](http://www.ymailsoft.com)来作为邮件服务器。推荐安装在c盘，避免出现问题。软件安装完成后如图所示：
![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/TsubasaBAKU.github.io/master/img/mail2.png)

## 2.2 软件设置

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;首先点击菜单栏中的“工具”-“服务器设置”打开服务器设置窗口，在“单域名”中输入自己的域名，这里我输入“shop.com”。
![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/TsubasaBAKU.github.io/master/img/mail3.png)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;点击菜单栏中的“账号”-“新建账号”，新建一个邮箱账号，并将这个账号作为发送邮件的账号，这里我们新建一个账号“service”，则邮箱名为“service@shop.com”。为了测试方便，我们新建一个接收邮件的账号“receive”，邮箱名为“receive@shop.com”。
![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/TsubasaBAKU.github.io/master/img/mail4.png)

至此，我们的邮件服务器搭建完成！

# 3 邮件客户端

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;为了能够接收邮件，我们需要使用一个邮件客户端，当然也可以直接使用网页版，但是为了方便本地测试，我们使用[foxmail客户端](http://www.foxmail.com/)。
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;软件的安装不再赘述，安装完成以后，点击菜单栏的“邮箱”-“新建邮箱账户”来新建一个邮箱账户，我们在这里将我们刚刚在服务器中设置的两个邮箱添加进来。
![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/TsubasaBAKU.github.io/master/img/mail5.png)

# 4 Java程序编写

在开始编写代码之前，我们需要引入两个jar包：activation.jar和mail.jar。
大致需要如下几个步骤：
 * 获得一个Session（javax.mail）对象
 * 创建一个代表邮件对象的Message
 * 发送邮件Transprot

## 4.1 创建Session对象

创建session对象不能直接使用new关键字，需要调用Session的静态方法getInstance。
```java
  Properties props = new Properties();
		props.setProperty("mail.host", "localhost");
		Session session = Session.getInstance(props, new Authenticator() {
			@Override
			protected PasswordAuthentication getPasswordAuthentication() {
				return new PasswordAuthentication("service@shop.com", "123");
			}
		});
```

## 4.2 创建message对象

```java
  Message message = new MimeMessage(session);
		message.setFrom(new InternetAddress("service@shop.com"));
		message.addRecipient(RecipientType.TO, new InternetAddress("receive@shop.com"));
		message.setSubject("来自service的一封邮件");
		message.setContent("<h1>这是一封测试邮件</h1>","text/html;charset=UTF-8");
```

## 4.3 发送邮件

```java
  Transport.send(message);
```

# 5 测试

运行程序，我们可以在foxmail客户端中看到如下效果：
![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/TsubasaBAKU.github.io/master/img/mail6.png)




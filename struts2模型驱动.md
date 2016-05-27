---
title: struts2模型驱动
date: 2016-05-25 00:38:47
tags:
	- Struts2
	- Ssh
	- Java
categories:
	- JAVA
	- SSH框架
---
 ## 1. Struts2属性驱动
Struts2的属性驱动指的是在action中的JSP页面的每一个form中的name都对应在action中的一个属性。有如下代码：
```html
<form action="register.do" name="RegisterForm" method="post">
	Username:<input type="text" name="uname"><br>
    Password:<input type="password" name="upass"><br>
</form>
```
以上的表单中包含最基本的两个信息：用户名和密码。那么它对应的Action也要有这两个属性（其实是属性对应的setter方法）。看实际的Action定义类：
<!--more-->
```java
public class RegisterAction extends ActionSupport{
	private String uname;
    private String upass;
    public String getUname(){
        return uname;
    }
    public void setUname(String uname){
        this.uname = uname;
    }
    public String getUpass(){
        return upass;
    }
    public void setUpass(String upass){
        this.upass = upass;
    }
    @Override
    public String execute() throws Exception{
        return ActionSupport.SUCCESS;
    }
}
```
可以看到RegisterAction的三个属性和Form中的name属性名字一样，这就是Struts2的属性驱动，Struts2会根据表单的name属性调用Action中的setter方法赋值。

## 2. 模型驱动
在Struts2中，每一个Action需要提供一个POJO对象，用来封装表单属性，依旧有如下代码：
```html
<form action="register.do" name="RegisterForm" method="post">
	Username:<input type="text" name="uname"><br>
    Password:<input type="password" name="upass"><br>
</form>
```
下面是POJO代码：
```java
public class User {
    private String uname;
    private String upass;
    public String getUname(){
        return uname;
    }
    public void setUname(String uname){
        this.uname = uname;
    }
    public String getUpass(){
        return upass;
    }
    public void setUpass(String upass){
        this.upass = upass;
    }
}
```
其实就是一个普通的JavaBean。但是Action中代码就不一样了:
```java
public class RegisterAction extends ActionSupport implements ModelDriven<User>{
	private User user;
    public User getUser(){
    	return user;
    }
    public void setUser(User user){
    	this.user=user;
    }
    public String execute() throws Exception{
    	return ActionSupport.SUCCESS;
    }
    //模型驱动必须实现的方法，也是ModelDriven接口中唯一的方法
    public User getModel(){
    	return user;
    }
}
```

## 3. 总结
* 模型驱动中的Action必须实现ModelDriven接口，而且有泛型约束。
* 实现ModelDriven的getModel方法，其实就是简单的返回一个对象。
* 在Action中提供一个泛型的私有对象，Action就会自动去调用setter方法将表单中name的属性值赋值给相应属性，而Action的后续处理的JSP页面或者是Servlet就可以使用User对象了。
---
title: OGNL表达式笔记
date: 2016-05-27 16:03:25
tags:
	- Struts2
	- OGNL
categories:
	- JAVA WEB
---

# OGNL

OGNL是Struts2中使用的一种表达式语言，它可以用于JSP的标签库中，以便能够方便的访问各种对象的属性；它用于界面将参数传递到Action（并进行类型转换）中；它还可以用于struts2的配置文件中！所以，非常有必要理解OGNL的基本机制。
<!--more-->

OGNL称为对象图导航语言。所谓对象图，就是以任一个对象为跟，通过OGNL能够访问到与这个对象相关联的其他对象。比如:

```java
public class User{

    private String username;
    private Group group;

    public String getUsername() {
       return username;
    }
    public void setUsername(String username) {

       this.username = username;

    }
    public Group getGroup() {
       return group;
    }
    public void setGroup(Group group) {
       this.group = group;
    }
}

public class Group{

	private String name;
    private Organization org;

    public String getName() {
       return name;
    }
    public void setName(String name) {
       this.name = name;
    }
    public Organization getOrg() {
       return org;
    }
    public void setOrg(Organization org) {
       this.org = org;
       }
}

public class Organization{

    private String orgId;

    public String getOrgId() {
       return orgId;
    }
    public void setOrgId(String orgId) {
       this.orgId = orgId;
    }
}
```

上面三个类，描述了通过一个User对象，可以导航到Group对象，进而导航到Organization对象，以User为根，一个对象图如下：

User（root）
&nbsp;&nbsp;-username
&nbsp;&nbsp;-group
&nbsp;&nbsp;&nbsp;&nbsp;-name
&nbsp;&nbsp;&nbsp;&nbsp;-org
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-orgId

在实际情况中，这个导航图可能极其复杂，但通过基本的getter方法，都能够访问到与该对象关联的一个对象。（对象图的导航，必须通过getters方法进行导航）

我们创建一系列的对象：
```java
	User user = new User();
	Group g = new Group();
    Organization o = new Organization();
    o.setOrgId("ORGID");
    g.setOrg(o);
    user.setGroup(g);
```

如果通过java代码进行导航，访问到Organization的orgId属性，需要通过getter方法，如下：
```java
	user.getGroup().getOrganization().getOrgId();
```

**注意：导航的目的，是为了获取/设置某个对象的值，或者调用某个对象的方法。OGNL的真正目的，是为了在那些不能写java代码的地方执行java代码，或者是为了更方便的执行java代码**

利用OGNL导航代码如下：
```java
	//第一个参数是OGNL表达式，第二个参数是表达式中需要用的root对象
	String value = (String)Ognl.getValue("group.org.orgId",user);
```

# Context对象

在OGNL表达式中，有可能需要访问到多个毫不相关的对象，这时候，我们需要给OGNL传递一个Map类型的对象，把表达式中需要用到的对象放到Map中即可，这个Map对象，称之为context。

要在表达式中访问到contex中的对象，需要使用“#对象名称”的语法规则。
```java
public void testOgnl04() throws Exception{
       User user = new User();
       user.setUsername("张三");
       Group g = new Group();
       Organization o = new Organization();
       o.setOrgId("ORGID");
       g.setOrg(o);
       user.setGroup(g);

       User user2 = new User();
       user2.setUsername("李四");

       Map context = new HashMap();
       context.put("u1",user);
       context.put("u2",user2);

       String value = (String)Ognl.getValue("#u1.username"+"#u2.username",context,new Object());

```

**context其实就是一个Map类型的对象，在OGNL中，不支持多个root对象，那么如果在表达式中需要访问多个毫不相关的对象是，只能通过一个Map将这些对象统一传递给OGNL。**

**在表达式中需要通过“#对象名”来访问context中的对象，如果表达式中没有用到根对象，可以使用任意一个对象来代表root对象。**

我们还可以：
```java
	Ognl.setValue("username",user,"张三");
    Ognl.setValue("#u.username",context,new Object());
    Ognl.setValue("method(a,b)",user);
```







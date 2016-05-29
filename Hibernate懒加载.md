---
title: Hibernate懒加载
date: 2016-05-29 16:44:13
tags:
	- Hibernate
	- 懒加载
categories:
	- SSH框架
---

# 延迟加载

延迟加载（load）是Hibernate为了提高程序执行效率而提供的一种机制，即只有真正使用该对象的数据时才会创建对象。
<!--more-->

延迟加载的过程：通过代理机制来实现延迟加载。Hibernate从数据库获取一个对象数据时、获取一个对象的集合属性时、获取与某一个对象关联的另一个对象时，由于没有使用该对象的数据，Hibernate并不从数据库加载真正的数据，而只是为该对象创建一个代理对象来代表这个对象，这个对象的所有属性都为默认值；只有在真正使用该对象的数据时才创建真正的对象，真正从数据加载它。

当调用Session上的load（）方法加载一个实体时，当Session加载某个实体时，会对该实体的集合属性进行延迟加载，会对该实体单端关联的另一个实体对象延迟加载。

# 关闭延迟加载

在加载单个对象时，可以使用get（）方法来关闭延迟加载。

对于实体中的集合属性，可以在映射中添加属性lazy=“false”来关闭延迟加载。

对于单端关联，可以在< one-to-one >，< many-to-one >添加属性lazy=“false”来关闭延迟加载。

# Hibernate中默认采用延迟加载的情况有以下几种

* 当调用Session（）中的load（）方法加载一个实体时会采用延迟加载。

* 当Session加载某个实体时，会对这个实体的集合属性采用延迟加载。

* 当Session加载某个实体时，会对这个实体单端关联的另一个实体对象延迟加载。

# 能够懒加载的对象都是被改写过的对象

当相关联的session没有关闭时，访问这些懒加载对象的属性，Hibernate会初始化这些代理对象。当相关联的session关闭后，访问懒加载对象会出现异常。

# 抓取策略

通过配置抓取策略来直接影响session（）的get（）和load（）方法的查询效果。

**单端关联的抓取策略（< many-to-one >< one-to-one >）:**

可以给单端关联的映射元素添加fetch属性。
select：延迟加载。
join：在同一条select语句中使用内连接来获得对象数据和它关联的对象，此时延迟加载失效。

**集合属性的抓取策略：**

select：延迟加载。
join：在同一条select语句使用内连接来获得关联集合。
subselect：另外发送一条查询语句。

# 实例

**单个实体调用load（）方法**

```java

@Test
publicvoid testGet(){

Session session = HibernateUtil.getSession();
User user = (User) session.get(User.class,1);
// System.out.println(user.getName());
HibernateUtil.close();
//这里要注意的是：即使关闭了session，使user处于托管状态，仍然可以可以使用user对象;这是因为虽然处于托管状态，但是这个对象是存在属性值的对象，并没有把他删除，只是隔绝了它与数据库的打交道的通道。  
System.out.println(user.getName());

}
//测试load()方法；不执行sql语句，用到的时候才执行
@Test
publicvoid testLoad(){

Session session = HibernateUtil.getSession();
User user = (User) session.load(User.class,1);
//这里输出id也不会执行sql语句，直接从上面你传进去的id中获取id，没有从数据库中查找，所以也不执行sql语句
System.out.println(user.getId());
//而输出name就不一样了，这时其实就是实例化了代理对象，这时代理对象有了name的属性，这时即使你关闭了session，也可以通过这个对象获取到name；如果注释这句，即不实例化代理对象，又在关闭session后执行输出name属性，这时会报错：could not initialize proxy
// System.out.println(user.getName());
HibernateUtil.close();
System.out.println(user.getName());
}
}
```

**SET集合取消懒加载，在映射文件中将set集合的懒加载属性设为false**
```java
@Test
publicvoid find(){
Session session = HibernateUtil.getSession();
Customer cus = (Customer) session.get(Customer.class,3);
System.out.println(cus.getCname());
//用到下面这种方法输出会出现两次sql语句，而且是分开的;如果用到懒加载会出现先输出两条sql语句，在输出结果
//这里不能直接方法链式输出cus.getOrd().getOname()；因为cus.getOrd()返回的是一个set集合
Set<Orders> orders = cus.getOrd();
System.err.println(orders.size());
HibernateUtil.close();
}
```

**< one-to-one >,< many-to-one > 取消懒加载**
```java
publicvoid find(){
//默认使用懒加载，即用着一条sql语句就输出一条;如果设置延时加载为false后输出两条sql语句，将不需要的顾客信息也查出来s
Session session = HibernateUtil.getSession();
Orders ord = (Orders) session.get(Orders.class,3);
System.out.println(ord.getOname());
HibernateUtil.close();
}
```

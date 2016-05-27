---
title: HibernateTemplate
date: 2016-05-25 00:01:47
tags:
	- Hibernate
	- Ssh
	- Java
categories:
	- SSH框架
---


# 1. getHibernateTemplate()与getSession()区别
* 使用getSession()方法需要sessionFactory，而使用getHibernateTemplate()方法需要继承HibernateDaoSupport，当然也包括 sessionFactory
* getSession()方法是没有经过Spring包装的，Spring会把最原始的session给你，在使用完之后必须自己调用相应的close()方法，而且也不会对声明式事务进行管理，一旦没有及时关闭连接，就会导致数据库连接池的连接数溢出。而getHibernateTemplate()方法是经过了Spring包装的，例如添加相应的声明式事务，由Spring管理相应的连接。
<!--more-->
# 2. HibernateTemplate的一些常用方法

### get/load存取单条数据
```java
return (classtype)this.hibernateTemplate.get(classtype,id);
return (classtype)this.hibernateTemplate.load(classtype,id);
```

### find/iterate查询操作
```java
List list=(List)this.hibernateTemplate.find("from User u where uid = ?",123);

Iterator iterator = this.hibernateTemplate.iterte("from User u where u.age > ?",30);
return iterator;
```

### save/update/saveOrUpdate/delete
```java
this.hibernateTemplate.save(user);
this.hibernateTemplate.update(user);
this.hibernateTemplate.saveOrUpdate(user);
this.hibernateTemplate.delete(user);
```

### bulkUpdate批量更新或删除
```java
this.hibernateTemplate.bulkUpdate("delete User where name = ? and age = ?", new Object[]{name,age});
```
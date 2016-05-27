---
title: Hibernate三种查询方式
date: 2016-05-25 00:01:22
tags:
	- Hibernate
	- Ssh
	- Java
categories:
	- SSH框架
---

## 1. HQL查询
HQL（Hibernate Query String）查询：是一种面向对象的查询语言，没有表和字段的概念，只有类、对象和属性的概念，是一种使用较为广泛的查询方式。
语法：
```sql
[select/update/delete...] from Entity [where...][group by...] [having...][order by..]
```
<!--more-->
* 在没有Spring框架时的用法：
```java
SessionFactory sessionFactory = new Configuration().configure().bulidSessionFactory();
session = sessionFactory.openSession();
String hql = "from User";
Query query = session.createQuery(hql);
List<User> list = query.list();
```

* 在有Spirng框架时的用法：
```java
String queryString = "from User";
List<User> list = getHibernateTemplate.find(queryString);
```

## 2. Criteria查询
Criteria查询采用面向对象方式封装查询条件，又称为对象查询。就是对SQL语句进行封装，采用对象的方式来组合各种查询条件，由Hibernate自动产生SQL查询语句。

* 没有Spring框架的写法：
```java
SessionFactory sessionFactory = new Configuration().configure().bulidSessionFactory();
session = sessionFactory.openSession();
Criteria criteria = session.createCriteria(User.class);
List result = criteria.list();
```

* 使用Spring框架的写法：
```java
import org.hibernate.criterion.DetachedCriteria;

DetachedCriteria cirteria = DetachedCriteria.forClass(ObjectEntity.class);
criteria.add(Restrictions.eq("propertyName",propertyValue));
List result = getHibernateTemplate().findByCriteria(criteria);
```

## 3. SQL语句查询

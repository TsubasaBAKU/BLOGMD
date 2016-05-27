---
title: ValueStack笔记
date: 2016-05-27 17:04:50
tags:
	- ValueStack
	- Struts2
categories:
	- JAVA WEB
---
# ValueStack
ValueStack实际上就是对OGNL的封装，Struts2正是通过ValueStack来进行赋值和取值的！
<!--more-->
ValueStack是一个接口，OgnlValueStack是Struts2中的缺省实现。ValueStack中的数据分两部分存放：root和context，与OGNL中的概念一致，同时ValueStack暴露相关的接口：

* void setValue(String expr,Object value);
* Object findValue(String expr);

通过OGNL表达式来对ValueStack中的数据进行操作。

ValueStack中的root对象是CompoundRoot，继承了ArrayList，提供了额外的方法：pop（）和push（），用来对root对象中所包含的数据进行存取。

```java
public class CompoundRoot extends ArrayList {

    public CompoundRoot() {}
    public CompoundRoot(List list) {
        super(list);
    }
    public CompoundRoot cutStack(int index) {
        return new CompoundRoot(subList(index, size()));
    }
    public Object peek() {
        return get(0);
    }
    public Object pop() {
        return remove(0);
    }
    public void push(Object o) {
        add(0, o);
    }
}
```

OGNL不支持多个root对象，而Struts2能够支持多个root对象，对OGNL做了扩展。

如果某个OGNL表达式被传递给ValueStack，而表达式中包含有对root对象的访问操作，ValueStack将依次从栈顶往栈底搜索CompoundRoot对象中所包含的对象，看哪个对象中有相应的属性，找到之后立刻返回。

在Struts2中，一个请求在最终到达Action的方法之前，Action对象本身会被压入ValueStack，放到ValueStack的CompoundRoot对象中，所以Action对象就是CompoundRoot中的一个元素。看下列代码：
```java
public class UserAction {

    private String username;
    private Integer age;
    private boolean valid;

    //查看用户的详细信息
    public String detail(){
       username = "张三";
       age = 18;
       valid = true;

       return "detail";
    }
```

页面代码：
```java
	username:<s:property value="username"/> <br/>

	valid:<s:property value="valid"/> <br/>

	age:<s:property value="age"/> <br/>
```

上述JSP页面能够正确地将它们的值取出。<s:property value="ognl表达式"/>。在标签中的OGNL表达式，最终会提交给ValueStack解释。username就是一个OGNL表达式，意思是调用root对象的getUsername（）方法。Struts2将自动搜索CompoundRoot中有哪些元素，检测这些元素是否有getUsername（）方法。

在这个例子中，CompoundRoot只有一个对象，就是userAction对象，而这个对象中正好有getUsername（）方法，所以值可以正确取出。

再看下列代码：
```java
public class UserAction {

    private String username;

    //查看用户的详细信息
    public String detail(){
       username = "张三";
       List list = new ArrayList();

       for(int i=0; i<10; i++){
           User user = new User();
           user.setUsername("User"+i);
           list.add(user);
       }
       ActionContext.getContext().put("users", list);
       User u = new User();
       u.setUsername("赵毅");
       ActionContext.getContext().getValueStack().push(u);
       return "detail";
    }
```
JSP:
```java
	<s:property value="username"/> <br/>
	<s:iterator value="#users">
		<s:property value="username"/>
	 	<s:property value="#root[2].username"/><br/>
	</s:iterator>
	<s:property value="username"/>
	<s:property value="#root[1].username"/>
```

根据刚才的示例，我们知道，第1行的username是“赵毅”（因为JSP在执行这行代码的时候，CompoundRoot中有两个元素：第0个是“user对象赵毅”，第1个是“userAction对象张三”），因此第1行的username将取出CompoundRoot中第0个元素的username属性：赵毅

第2行代码是iterator标签，只定义了一个value属性，iterator标签将循环访问users这个List中的User对象，并把当前循环的user对象压入到CompoundRoot中！所以，在第3行和第4行代码被执行的时候，CompoundRoot中总共有3个元素：第0个元素是被iterator标签压入的当前循环的user对象；第1个元素是“user对象赵毅”；第2个元素是“userAction对象张三”，因此第3行代码的执行结果就是输出“UserX”，即当前循环的user对象的username属性！iterator标签将会依次取出List中的user对象，并不断压入/弹出user对象（每次循环，都将执行一遍压入/弹出）。而第4行代码取第2个元素的username属性，即userAction对象的username属性：张三。

第5行代码执行完成之后，在CompoundRoot中将剩下2个元素，与第2行代码被执行之前一样。所以，第6行代码的输出和第1行代码的输出结果是一样的，而第7行代码将取出userAction对象的username属性：张三


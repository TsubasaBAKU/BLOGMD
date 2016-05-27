---
title: Servlet笔记
date: 2016-05-27 10:16:13
tags:
	- Servlet
Categories:
	- JAVA WEB
---

# 1. Java服务器

## 1.1 服务器概念

从软件功能上来讲，服务器指的就是能够提供服务的运行着的程序,即一个提供服务的进程。
<!--more-->
“服务（Service）”就是一些相关功能的集合，<font color=red>这些功能响应基于公开的接口和规范，并通过封装其实现的接口，来响应特定的请求并产生响应的结果。</font>常见的应用级协议包括HTTP、FTP、SMTP以及TELNET等，这些都是标准化的网络协议。

那么从最简单的概念上讲，<font color=red>一个服务器就是这样的一个程序：它首先能够识别出符合标准协议格式的请求信息，其次再把请求信息进行内部处理，最后再将处理结果按照标准格式发送回去。</font>

## 1.2 Java如何实现服务器架构
服务架构（Service Framework）是一个实现了服务的类的集合，这些服务使用多个处理线程来完成与客户的交互。服务器通过一个连接点描述符（ConnectionEndpoint）获得一个基于连接的服务的特定端口（ServerSocket），连接点描述符封装了一些特定的信息，例如服务所使用的监听端口号，选项和协议信息等。

<font color=red>获得了ServerSocket后，服务就可以创建自身的服务处理线程池。在处理线程池中的线程进入循环，等待来自客户的连接请求，在接收到请求后通过该连接链路处理协议。处理线程池的大小是动态变化的，当有需要临时创建新的处理线程池，单一般数量都有上下限制。</font>

核心的服务架构通常用来处理某个特定的应用协议。例如，为HTTP服务提供了一个HTTP服务基类，由其创建的处理线程就能处理HTTP协议。

服务器在启动时需要启动一个服务集合，每个服务都有自己的线程组。除了处理标准协议的服务外，服务器还要包含进行内部协调和管理的服务。

Servlet就是在Java中以实现以上服务架构所产生的。

# 2. Servlet的功能

Servlet可称为“服务器小程序”，与Java application不同，它没有main方法，而是用一些的特定的方法启动、执行和退出。通过Servlet，可以与运行与客户端的Applet交互，也可以直接和HTML进行交互。

<font color=red>在传统的CGI模式中（运行在服务器上的外部程序，功能模块），对于每一个HTTP请求，服务器都要启动一个进程，如果这个请求只是要求一个很简单的操作，那么所花费的代价几乎都表现在服务器进程的启动时间上了，而Servlet是建立在Java虚拟机上的，每一个请求对应于一个更轻量级的线程，这样在处理请求是就更具有灵活性，比如可以在若干个请求中保留对其他资源的控制等。</font>

Servlet除了具有Java本身的许多优点外，还提供一套系统的数据结构自动分析功能，可以解析HTML表单提交的参数，读取设置HTTP头部信息，处理Cookie，用户Session追踪以及相关操作。

利用Servlet可以较容易的做到一些普通CGI很难做到的事情，比如直接与网络服务器对话等，不同的Servlet之间可以进行一些资源的共享。

通过Servlet的安全模型和“沙箱”机制还可以保护系统不受破坏。

此外，由于Java本身的平台无关性，就使得基于Servlet的Java服务器又很强的适应能力。

Sun公司为Servlet程序提供了一个标准的API（ServletAPI），保证了程序开发的一致性。ServletAPI的最大优点就是它的协议无关性，因此其中并不包含在网络上传输所用的协议、Servlet的加载方式以及服务器环境等内容，这样就使其容易被嵌入到多种类型的服务器中。

# 3. Servlet开发包

## 3.1 javax.servlet包
javax.servlet包包含的是编写Servlet所需的最基本的类和接口。

定义的接口：

> * .RequestDispatcher

> * .Servlet

> * .ServletConfig

> * .ServletContext

> * .ServletRequest

> * .ServletResponse

> * .SingleThreadModel

定义的类：

> * GenericServlet

> * ServletInputStream

> * ServletOutputStream

定义的异常：

> * .ServletException

> * .UnavilableException

![cmd-markdown-logo](../../public/img/Servlet1.png)

## 3.2 接口Servlet

<font color=red>所有的Servlet一般情况下都要继承GenericServlet的子类HttpServlet。</font>

这个接口用于开发Servlet。一个Servlet就是一个Java类，它能在一个网络服务中被装载和执行。Web服务器从客户端接收请求，并对请求作出反应，然后把响应的结果返回客户。例如一个客户需要从一个数据库中获取数据或信息，就可以再服务器端写一个Servlet来完成这一任务。该Servlet接收客户的数据或信息请求，从数据库中得到数据或信息，并可能对取得的结果做出相应的处理，最后返回用户请求的数据或信息。

所有的Servlet都要实现这个接口。编写一个Servlet程序通常通过生成GenericServlet类的子类，或者进一步生成GenericServlet的子类HttpServlet的子类来实现。一般在编程是不需要实现这个接口，只有在不能够或者不需要从GenericServlet或者HttpServlet继承时，才需要实现此接口。

Servlet接口定义了几个方法用于初始化一个Servlet、接收客户的请求、响应客户的请求、以及删除一个Servlet和它所占的资源，这和Applet中的生命周期函数有很多类似的地方。一个Servlet将按照以下步骤被网络服务所调用。

> * 创建并初始化

> * 处理客户来的一个或多个服务请求

> * 删除Servlet，回收所占用的资源，终止Servlet运行

初始化一个Servlet所作的工作包括许多一次性开销大的处理，比如从一个文件中读出数据或者开始一个辅助线程等待。对于从客户端来的服务请求，Servlet使用接收请求包和发送应答包的工作模式处理，其服务质量依赖于下层也就是网络传输层所提供的服务的质量，例如排序、重复、消息的完整性和保密性等等。删除一个Servlet包括撤销它在初始化时所作的所有工作和同步Servlet内存中的当前持续状态。

除了生命周期函数之外，Servlet接口还提供了一个Servlet获取关于启动信息的方法，以及获取自身信息的方法。

以下是Servlet接口的定义：

```java
public abstract interface Servlet(){
	
    //所有的线程从此Servlet的service方法中退出或者超出了预定时间后，调用此方法，从而释放Servlet所占用的资源
    public void destroy();
    
    //返回一个ServletConfig对象，该对象包含了这个Servlet的所有初始化参数和启动设置
    public ServletConfig getServletConfig();
    
    //初始化Servlet并将其调入到服务器中，在收到任何请求之前，必须保证已经初始化完毕。
    public void init(ServletConfig config)();
    
    //执行一次来自客户的请求
    public void service(ServletRequest req,ServletResponse res)();

}
```

Servlet接口中的service方法是完成请求处理的主要方法。第一个参数代表客户请求，第二个代表对客户的响应。

只有通过service方法才能使Servlet对请求作出响应，当然前提是此Servlet已经通过init方法初始化。当一个Servlet还没有完成初始化时，Servlet将锁定对此方法的请求直到初始化完毕。同样，一旦Servlet通过调用destory方法将此Servlet从服务中移除后，就不能再调用此Servlet的service方法了。

## 3.3 接口ServletConfig

当Servlet第一次被装载时，为了向其传递服务设置信息，开发者必须实现此接口。在实现此接口是，要为Servlet写一个方法，Servlet使用该方法来获得初始化参数和它运行时的环境。此接口也可由Servlet来实现。

以下是ServletConfig的定义：

```java
public abstract interface ServletConfig{
	
    //返回指定名称的Servlet初始化参数，无则返回空值
	public String getInitParameter(String name);
    
    //返回所有初始化参数的名称
    public Enumeration getInitParameterNames();
    
    //返回服务器传递给此Servlet的运行环境
    public ServletContext getServletContext)();
}
```

## 3.4 接口ServletContext

接口ServletContext提供给Servlet一些访问它们所运行环境的方法，并允许记录一些重要事件。如果服务器支持多个虚拟主机，至少要保证对每个主机来说有唯一的ServletContext对象。

## 3.5 接口RequestDispatcher

此接口定义了一个从客户端接收请求，并能够把请求发送到任何服务器资源（比如Servelet、HTML文件或者jsp文件）的对象。
```java
public abstract interface RequestDispatcher{

	void forward(ServletRequest request、ServletResponse response)

	/*将当前servlet对象中的ServletRequest对象发送给服务器的另一资源（servlet，JSP文件或HTML文件）。*/

 	void include(ServletRequest request、ServletResponse response)

	 //包含Web服务器中的另一个资源（servlet，JSP文件或HTML文件）。

}
```

## 3.6 接口SingleThreadModel

保证servelet在同一时刻只处理一个请求。如果一个servlet实现了此接口，则可以确定不可能有一个以上的线程同时执行这个servlet的service方法。

## 3.7 javax.servlet.http包

javax.servlet.http包中定义的类和接口是为了支持HTTP协议。

定义了五个接口:

> * .HttpServletRequest

> * .HttpServletReponse

> * .HttpSession

> * .HttpSessionBindingListener

> * .HttpSessionContext

定义了四个类：

> * .Cookie

> * .HttpServlet

> * .HttpSessionBindingEvent

> * .HttpUtils

## 3.8 接口HttpSession

提供了标识同一用户或用户在Web站点上的轨迹的方法

Servlet引擎使用此接口创建session对象，session对象描述了HTTP客户端和HTTP服务端的一个会话，这个会话将保留一个预定的时间。一个会话通常对应一个用户，这个用户可能多次访问同一个站点，服务器可以通过使用Cookie或者重写URL来维持会话。

如果符合下列情况，服务器将认为这是一个新的会话：

> * 客户端还不知道这个会话

> * 此会话还未开始

> * 客户不加入这个会话。比如服务器只支持cookie而客户却拒绝了服务器发送的cookie

## 3.9 类Cookie

类Cookie用以创建一个包含少量信息的cookie，此cookie由一个servlet发送给Web浏览器，并由浏览器保存，当客户随后访问同一Web页面是将送回给服务器。一个cookie的值可以唯一的标识一个客户，因此在会话管理中经常使用。

一个cookie具有一个名称、一个值以及操作属性，比如一段注释、路径、域限定符、最大生存期和版本号等。某些Web浏览器在处理属性时有bug，因此在使用属性时应该多加注意。

servlet通过HttpServletResponse.addCookie 方法来向浏览器发送cookie，cookie被添加到HTTP的响应头之中，每次一个。浏览器应该可以接受发自同一Web服务器的20个cookie。

浏览器也是通过在HTTP请求头中添加域的方法向服务器回送cookie。可以通过HttpServletRequest.getCookies方法从请求中一次获得所有cookie。少数cookie可能具有相同的名称，但具有不同的路径属性。 

使用cookie会影响Web页的缓存。HTTP 1.0 不缓存用使用了类Cookie创建的cookie的Web页。类Cookie也不支持HTTP 1.1中定义的缓存控制。

## 3.10 类HttpServlet

类HttpServlet是一个抽象类，可以通过创建从此类派生的子类来创建HTTP servlet，用以接收Web站点的请求或向Web站点发送响应。当从类HttpServlet派生子类时，至少要重写以下方法中的一个：

1．doGet方法，支持HTTP GET 请求。

2．doPost方法，支持HTTP POST请求。

3．doPut方法，支持HTTP PUT 请求。

4．doDelete方法，支持HTTP DELETE请求。

5．init方法和destroy方法，这两个方法要同时改写以控制servlet的生命周期。

6．getServletInfo方法，此方法被servlet用来提供自身的信息。

一般情况下不需要重写service方法。service方法将按照每一个标准的HTTP请求的类型进行分发处理。

同样，也不需要重写doOptions方法和doTrace方法，service方法支持HTTP 1.1标准中的TRACE和OPTIONS请求，自动调用相应的doTrace s方法和doOption方法。

典型情况下，Servlet运行在能够处理并发请求的多线程Servlet引擎之中。所以必须保证对任何共享资源的访问同步，例如网络连接、实例变量等。关于多线程同步可参考Java语言参考中的相关内容。







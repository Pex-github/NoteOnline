#### MVC 模式

（Model-View-Controller）是软件工程中的一种软件架构模式，把软件系统分为三个基本部分：模型（Model）、视图（View）和控制器（Controller）。

控制器Controller：对请求进行处理，负责请求转发；

视图View：界面设计人员进行图形界面设计；

模型Model：程序编写程序应用的功能（实现算法等等）、数据库管理；

当下发展的模型，分成三部分（JSP,Servlet,JavaBean）

JSP：视图层，用来与用户打交道。负责接收用来的数据，以及显示数据给用户；

JavaBean：模型层，完成具体的业务工作，例如：开启、转账等。

Servlet容器：控制层，接受数据，处理请求，完成响应，负责找到合适的模型对象来处理业务逻辑，转发到合适的视图；

servlet类是单例，由我们编写，服务器来创建，且由服务器条用相应的方法。servlet在第一次访问时被创建，执行init方法，每次收到请求都会调用内部的service方法处理请求。通常在服务器关闭时servlet才会销毁调用destroy方法

ServletRequest：service() 方法的参数，它表示请求对象，它封装了所有与请求相关的数据，它是由服务器创建的；

ServletResponse：service()方法的参数，它表示响应对象，在service()方法中完成对客户端的响应需要使用这个对象



#### Cookie 

是由服务器创建，然后通过响应发送给客户端的一个键值对。客户端会保存Cookie，并会标注出Cookie 的来源（哪个服务器的Cookie）。当客户端向服务器发出请求时会把所有这个服务器Cookie 包含在请求中发送给服务器，这样服务器就可以识别客户端了！

如果请求路径包含了cookie路径，那么会在请求中包含这个cookie

默认路径是 访问资源的上一级路径，domain属性可以让网站的二级域名共享一级域名 的cookie



#### session

服务器创建，服务器保存

会话，我们可以把一个会话内需要共享的数据保存到HttpSession 对象中！会话范围是某个用户从首次访问服务器开始，到该用户关闭浏览器结束

#### 三个域对象

- HttpServletRequest：

​	一个请求创建一个request 对象，所以在同一个请求中可以共享request，例如一个请求从AServlet 转发到BServlet，那么AServlet 和BServlet 可以共享request域中的数据；

- ServletContext：

​	一个应用只创建一个ServletContext 对象，所以在ServletContext中的数据可以在整个应用中共享，只要不关闭服务器，那么ServletContext 中的数据就可以共享；

- HttpSession：

​	一个会话创建一个HttpSession 对象，同一会话中的多个请求中可以共享session 中的数据；



#### session和cookie区别

​	Session存储在服务器端，类型可以是任意的Java对象，Cookie存储在客户端，类型只能为字符串

​	session的安全性要比cookie高，重要的信息存储在session中，而把一些次要东西存储在客户端

​	cookie确切的说分为两大类分为会话cookie和持久化cookie

​	访问HTML页面是不会创建session,但是访问index.JSP时会创建session(JSP实际上是一个Servlet, Servlet中有getSession方法)

使用场景：		

将登陆信息等重要信息存放为SESSION，其他信息如果需要保留，可以放在COOKIE中，比如购物车

购物车最好使用cookie，但是cookie是可以在客户端禁用的，这时候我们要使用cookie+数据库的方式实现，当从cookie中不能取出数据时，就从数据库获取。

#### Get和Post的区别

​	1.get是从服务器上获取数据，post是向服务器传送数据，

​	2.get传送的数据量较小，不能大于2KB。post传送的数据量较大，一般被默认为不受限制。

​	3.get安全性非常低，post安全性较高。但是执行效率却比Post方法好。

​	4.在进行文件上传时只能使用post而不能是get。

#### Forword(请求转发)与Redirect(重定向)

1、从数据共享上

   Forword是一个请求的延续，可以共享request的数据

   Redirect开启一个新的请求，不可以共享request的数据

2、从地址栏

   Forword转发地址栏不发生变化

   Redirect转发地址栏发生变化

#### XML和Json的特点

 Xml特点：

　　1、有且只有一个根节点；

　　2、数据传输的载体

　　3、所有的标签都需要自定义 	

　　4、是纯文本文件

Json（JavaScript Object Notation）特点：

   json分为两种格式:  

​		json**对象**(就是在{}中存储键值对，键和值之间用冒号分隔，键 值 对之间用逗号分隔); 

​		json**数组**(就是[]中存储多个json对象，json对象之间用逗号分隔)（两者间可以进行相互嵌套）数据传输的载体之一

**区别**：

  传输同样格式的数据，xml需要使用更多的字符进行描述，

  流行的是基于json的数据传输。

  xml的层次结构比json更清晰。

**共同点**：	

  xml和json都是数据传输的载体，并且具有跨平台跨语言的特性。

#### AJAX

 异步JavaScript及 XML，一种用于创建更好更快以及交互性更强的 Web 应用程序的技术。

​	提高用户体验度(UE)，提高应用程序的性能，进行局部刷新

​	通过 AJAX，我们的 JavaScript 可使用JavaScript的XMLHttpRequest对象来直接与服务器进行通信。通过这个对象，我们的 JavaScript 可在不重载页面的情况与Web服务器交换数据，即可局部刷新

​	AJAX 在浏览器与 Web 服务器之间使用异步数据传输（HTTP 请求），这样就可使网页从服务器请求少量的信息

#### JSP9大隐视对象中四个作用域的大小与作用范围

四个作用域从大到小：appliaction>session>request>page

​	application：全局作用范围，整个应用程序共享.生命周期为：应用程序启动到停止。

​	session：会话作用域，当用户首次访问时，产生一个新的会话，以后服务器就可以记       住这个会话状态。

​	request：请求作用域，就是客户端的一次请求。

​	page：一个JSP页面。

​	以上作用范围使越来越小， request和page的生命周期都是短暂的，他们之间的区别就是：一个request可以包含多个page页(include，forward)。

#### 报错状态码

 301 永久重定向

 302 临时重定向

 304 服务端 未改变

 403 访问无权限

 200 正常

 404 路径

 500 内部错误
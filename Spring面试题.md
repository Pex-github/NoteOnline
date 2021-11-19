### 什么是IOC

**控制反转**，他是一种设计思想，**将原本手动创建对象的控制权交给Spring框架来管理**，依赖注入和控制反转是同一个概念。**IOC容器是spring实现**IOC的载体，**本质上是个Map结构，存放的是各种对象**。

**IOC让对象的创建不用去new了，可以由spring自动生产**，**使用java的反射机制**，根据配置文件在运行时**动态的去创建对象以及管理对象，并调用对象的方法的**。

IOC初始化会从xml文件读取到Resource再解析到BeanDefinition再注册到BeanFactory

- 第一个过程是Resource资源定位。这个Resouce指的是BeanDefinition的资源定位。这个过程就是容器找数据的过程，就像水桶装水需要先找到水一样。
- 第二个过程是BeanDefinition的载入过程。这个载入过程是把用户定义好的Bean表示成Ioc容器内部的数据结构，而这个容器内部的数据结构就是BeanDefition。
- 第三个过程是向IOC容器注册这些BeanDefinition的过程，这个过程就是将前面的BeanDefition保存到HashMap中的过程。

Spring的IOC有三种注入方式 ：构造器注入、setter方法注入、根据注解注入

##### 	说说你了解的IOC容器

​	一、BeanFactory：最基本的IOC容器，定义了些基本的容器 方法像getBean，containsBean等 

- xmlBeanFactory ：上面的一个实现类，建立在DefaultListablexmlBeanFactory容器基础上，添加了xml读取等其他附加功能

​	二、ApplicationContext：Spring中的高级容器。可以加载配置文件中定义的bean，管理分配bean，另外添加了企业生产需求上的功能，如下

- FileSystemxmlApplicationContext：从xml文件中加载被定义的bean，只需要提供构造器xml文件的完整路径
- ClassPathXmlApplicationContext：加载xml中定义的bean，但只需要配置正确的ClassPath环境变量
- WebXmlApplicationContext：容器会在web应用范围内加载xml文件
- 接口。支持多种信息源，国际化
- ResourcePatternResolve 接口：资源文件访问
- ApplicationEventPublisher接口。支持应用事务，为应用环境 引入了事件机制

##### 两者有什么区别

​	①BeanFactroy采用的是延迟加载形式来注入Bean的，只有在使用到某个Bean时(调用getBean())，才对该Bean进行加载实例化，这样可能存在不被提前发现的配置问题

​	②ApplicationContext，它是在容器启动时，一次性创建了所有的Bean

​	③BeanFactory通常以编程的方式被创建，ApplicationContext还能以声明的方式创建，如使用ContextLoader



##### 说说ioc容器初始化过程

```
首先Resource资源定位，将类加载到容器里，其中实例化了beanFactory，beandefinitionRead和scanner。用于将有特定注解的类或指定目录下的bean转换为beanDefinition对象，然后将类注册到容器中，这就是一个容器找数据的过程
```

```
然后执行refresh方法刷新容器，该方法进行了prepare预处理工作，添加一些组件，容器标准初始化之后，向容器注册bean的后置处理器，然后初始化messagesource组件【国际化功能】，注册监听器，最后初始化所有剩下的bean，完成容器刷新
```

​	（1）初始化Spring容器，注册内置的BeanPostProcessor的BeanDefinition到容器中：

​		① 实例化BeanFactory【DefaultListableBeanFactory】工厂，用于生成Bean对象
​		② 实例化BeanDefinitionReader注解配置读取器，用于对特定注解（如@Service、@Repository）的类进行读取转化成  BeanDefinition 对象，（BeanDefinition 是 Spring 中极其重要的一个概念，它存储了 bean 对象的所有特征信息，如是否单例，是否懒加载，factoryBeanName 等）
​		③ 实例化ClassPathBeanDefinitionScanner路径扫描器，用于对指定的包目录进行扫描查找 bean 对象
（2）将配置类的BeanDefinition注册到容器中：

（3）调用refresh()方法刷新容器：

​		① prepareRefresh()刷新前的预处理：
​		② obtainFreshBeanFactory()：获取在容器初始化时创建的BeanFactory：
​		③ prepareBeanFactory(beanFactory)：BeanFactory的预处理工作，向容器中添加一些组件：
​		④ postProcessBeanFactory(beanFactory)：子类重写该方法，可以实现在BeanFactory创建并预处理完成以后做进一步的设置
​		⑤ invokeBeanFactoryPostProcessors(beanFactory)：在BeanFactory标准初始化之后执行BeanFactoryPostProcessor的方法，即BeanFactory的后置处理器：
​		⑥ registerBeanPostProcessors(beanFactory)：向容器中注册Bean的后置处理器BeanPostProcessor，它的主要作用是干预Spring初始化bean的流程，从而完成代理、自动注入、循环依赖等功能
​		⑦ initMessageSource()：初始化MessageSource组件，主要用于做国际化功能，消息绑定与消息解析：
​		⑧ initApplicationEventMulticaster()：初始化事件派发器，在注册监听器时会用到：
​		⑨ onRefresh()：留给子容器、子类重写这个方法，在容器刷新的时候可以自定义逻辑
​		⑩ registerListeners()：注册监听器：将容器中所有的ApplicationListener注册到事件派发器中，并派发之前步骤产生的事件：
​		⑪  finishBeanFactoryInitialization(beanFactory)：初始化所有剩下的单实例bean，核心方法是preInstantiateSingletons()，会调用getBean()方法创建对象；
​		⑫ finishRefresh()：发布BeanFactory容器刷新完成事件：

### 什么是AOP

​	面向切面编程，将那些与业务代码无关，却被业务模块所共同调用的逻辑，【像事务处理，日志管理，权限控制等】封装起来。减少系统重复代码，降低耦合，便于扩展和维护

​	Spring AOP框架是基于动态代理，对于实现了某个接口的类，AOP会使用JDK proxy创建代理对象。没有实现接口的，就用Cglib生成被代理对象的子类作为代理。也可以使用AspectJ框架。

<img src="https://img-blog.csdnimg.cn/2020120700443256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2E3NDUyMzM3MDA=,size_16,color_FFFFFF,t_70" alt="img" style="zoom:80%;"  width = "50%"/>

##### 	两个框架的区别

​	SpringAOP属于运⾏时增强，⽽ AspectJ 是编译时增强。Spring AOP 基于代理(Proxying)， ⽽ AspectJ 基于字节码操作(Bytecode Manipulation)。前者简单，后者功能强大

### Spring启动流程



### Spring自动装配

基于注解的自动装配方式

使用@Autowired、@Resource注解来自动装配指定的bean。容器自动装载了一个AutowiredAnnotationBeanPostProcessor后置处理器，容器扫描到相应的注解时，自动查找需要的bean，并 装配给对象

ps：@Autowired和@Resource之间的区别

​	(1) @Autowired默认是按照类型装配注入的，默认情况下它要求依赖对象必须存在（可以设置它required属性为false）。

​	(2) @Resource默认是按照名称来装配注入的，只有当找不到与名称匹配的bean才会按照类型来装配注入。

### Spring如何解决循环依赖问题

循环依赖问题在Spring中主要有三种情况：

（1）通过构造方法进行依赖注入时产生的循环依赖问题。

（2）通过setter方法进行依赖注入且是在多例（原型）模式下产生的循环依赖问题。

（3）通过setter方法进行依赖注入且是在单例模式下产生的循环依赖问题。

前两者情况只能产生异常，情况3被解决：Spring在单例模式下的setter方法依赖注入引起的循环依赖问题，主要是通过二级缓存和三级缓存来解决的，其中三级缓存是主要功臣。解决的核心原理就是：在对象实例化之后，依赖注入之前，Spring提前暴露的Bean实例的引用在第三级缓存中进行存储。

### Spring Bean的作用域有哪些

singleton : 唯⼀ bean 实例，Spring 中的 bean 默认都是单例的。 

prototype : 每次请求都会创建⼀个新的 bean 实例。 

request : 每⼀次HTTP请求都会产⽣⼀个新的bean，该bean仅在当前HTTP request内有 效。 

session : 每⼀次HTTP请求都会产⽣⼀个新的 bean，该bean仅在当前 HTTP session 内有 效。 

global-session： 全局session作⽤域，仅仅在基于portlet的web应⽤中才有意义，Spring5已 经没有了。Portlet是能够⽣成语义代码(例如：HTML)⽚段的⼩型Java Web插件。它们基于 portlet容器，可以像servlet⼀样处理HTTP请求。但是，与 servlet 不同，每个 portlet 都有不 同的会话

##### 	单例bean存在线程安全问题吗？

​	存在，当多线程操作对象的非静态成员变量的写操作，会出现线程安全问题

​	解决方法：定义一个ThreadLocal成员变量 ，将可变成员变量保存在ThreadLocal中

##### 	bean的生命周期了解吗

1. 利用反射实例化Bean
2. 设置对象属性
3. 若实现了BeanNameAware接口，就传入Bean名字
4. 若实现了BeanClassLoaderAware接口，就传入ClassLoader对象实例，还有实现xxxAware接口，就调用相应的方法
5. 若有Bean相关BeanPostProcessor对象，执行初始化前置处理方法
6. 若Bean实现了initalizingBean接口，执行afterPropertiesSet方法
7. 若配置文件定义了init-method属性，执行指定的方法
8. 若有Bean相关的BeanPostProcessor对象，执行初始化后处理方法
9. 实例使用
10. 销毁Bean时，若实现了DisposableBean接口，执行销毁方法
11. 销毁Bean时，若配置文件中定义了包含destroy-method方法，执行指定方法

<img src="C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210515215111738.png" alt="image-20210515215111738" style="zoom:80%;" width = "50%" />

### Spring常用注解有哪些

<img src="C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210524171850816.png" alt="image-20210524171850816" style="zoom: 80%;" width = "50%" />

<img src="C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210524171924508.png" alt="image-20210524171924508" style="zoom: 80%;" width = "50%" />

##### 非持久化字段用什么注解

@Transient

##### @Component和@Bean的区别

​	作⽤对象不同: @Component 注解作⽤于类，⽽ @Bean 注解作⽤于⽅法。

​	@Component 通常是通过类路径扫描来⾃动侦测以及⾃动装配到Spring容器中，@Bean 注解通常是我们在标有该注解的⽅法中定义产⽣这个 bean。@Bean 注解⽐ Component 注解的⾃定义性更强，⽽且很多地⽅我们只能通过 @Bean 注 解来注册bean。

##### 可以将一个类声明为Spring的bean的注解有哪些

​	@Autowired	自动装配bean，下面的注解可以把类标识成可用于Autowired注解

​	@Component，标注类为Spring组件。

​	@Repository，对应持久层，用于数据库相关操作

​	@Service，对应服务层 ，

​	@Controller，对应MVC控制层，接受请求，调用服务层返回数据给前端页

### 你如何认识Spring MVC

一个基于Java的实现了MVC设计模式的请求驱动类型的轻量级Web框架，通过把Model，View，Controller分离，将web层进行职责解耦，把复杂的web应用分成逻辑清晰的几部分，简化开发，减少出错，方便组内开发人员之间的配合

##### MVC的优点

- 支持各种视图技术，不只局限于JSP
- 与spring框架集成
- 角色划分清晰前端控制器(dispatcherServlet) ，请求到处理器映射（handlerMapping)，处理器适配器（HandlerAdapter)，视图解析器（ViewResolver）
- 支持 各种请求资源的映射策略

##### MVC的常用 注解，你能说几个吗

<img src="C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210524172028427.png" alt="image-20210524172028427" style="zoom: 80%;" />

- @RequestMapping	用于处理请求 url 映射，可用于类或方法上，用于类上，则表示类中的所有响应请求的方法都是以该地址作为父路径
- @RequestBody：注解实现接收http请求的json数据，将json转换为java对象
- @ResponseBody：注解实现将conreoller方法返回对象转化为json对象响应给客户。

如何解决POST/Get请求中文乱码问题

在web.xml中配置一个CharacterEncodingFilter过滤器，设置成utf-8

##### SpingMvc中的控制器的注解一般用哪个？有没有别的注解可以替代

一般用@Controller注解，也可以使用@RestController，@RestController注解相当于@ResponseBody ＋ @Controller，表示是表现层，除此之外，一般不用别的注解代替

##### 	MVC工作流程能说说嘛？

1. 客户端发送请求给前端控制器（DispatherServlet）

2. 前端控制器调用处理器映射器（HandlerMapping）解析请求，查找对应的处理器Handler。生成处理器对象返回给前端控制器

3. 前端控制器调用 处理器适配器（HandlerAdapter）请求执行Handler的业务逻辑

4. 处理器处理完成后，返回 ModelAndView对象给前端控制器

5. 前端控制器 将对象传给视图解析器，进行解析，返回具体的View

6. 前端控制器进行视图渲染将模型数据填充到视图中，然后 响应用户 

   <img src="https://img-blog.csdn.net/20180708224853769?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2E3NDUyMzM3MDA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="img" style="zoom:80%;" />

##### MVC怎样使用 重定向和转发

​	在返回值前面加"forward:"就可以让结果转发,譬如"forward:user.do?name=method4"

​	返回值前面加"redirect:"就可以让返回值重定向,譬如"redirect:http://www.baidu.com"

​	forward/redirect:除了跳转至某个页面，也可以跳转至某个controller处理方法。并且使用时需要以/开头,默认是相对于项目的根目录的，不加就是相对于当前controller的@RequestMapping值的路径（没有这个注解就是相对于项目根目录）。

SpringMvc里面拦截器是怎么写的

一种是实现HandlerInterceptor接口，另外一种是继承适配器类，接着在接口方法当中，实现处理逻辑；然后在SpringMvc的配置文件中配置拦截器即可

##### MVC用什么对象从后台向前台传递数据的

​	通过ModelMap对象,可以在这个对象里面用put方法,把对象加到里面,前台就可以通过el表达式拿到

##### 注解的原理了解吗

​	注解本质是一个继承了Annotation的特殊接口，其具体实现类是JDK动态代理生成的代理类。我们通过反射获取注解时，返回的也是Java运行时生成的动态代理对象。通过代理对象调用自定义注解的方法，会最终调用AnnotationInvocationHandler的invoke方法，该方法会从memberValues这个Map中查询出对应的值，而memberValues的来源是Java常量池。

##### Spring MVC的异常处理 ？

答：可以将异常抛给Spring框架，由Spring框架来处理；我们只需要配置简单的异常处理器，在异常处理器中添视图页面即可。

##### 怎么样把ModelMap里面的数据放入Session里面

可以在类上面加上@SessionAttributes注解,里面包含的字符串就是要放入session里面的key

### Spring框架使用过哪些模式？

- ⼯⼚模式 : Spring使⽤⼯⼚模式通过 BeanFactory 、 ApplicationContext 创建 bean 对 象。 
- 代理模式 : Spring AOP 功能的实现。 
- 单例模式 : Spring 中的 Bean 默认都是单例的。 
- 包装器模式 : 我们的项⽬需要连接多个数据库，⽽且不同的客户在每次访问中根据需要 会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。 
- 观察者模式: Spring 事件驱动模型就是观察者模式很经典的⼀个应⽤。 
- 适配器模式 :Spring AOP 的增强或通知(Advice)使⽤到了适配器模式、spring MVC 中也是⽤ 到了适配器模式适配 Controller 。

### Spring事务，你在生产中用过吗？

​	常用的管理事务方式：声明式事务

​	声明式事务分为：1.基于XML	2.基于注解

##### 	事务的隔离级别有哪几个

​		他们定义在TransactionDefinition中

​		isolation default：默认隔离级别，和Mysql相同

​		isolation read-uncommitted：允许读取未提交的数据更变

​		isolation read-committed：允许读取已提交的数据

​		isolation serializable：最高隔离级别，事务以此逐个执行，事务间不可产生干扰

##### 	事务的传播行为有哪几种

​	传播（propagation）

- 支持当前事务，当前存在事务
  - required：存在则加入，不存在则创建一个新 事务
  - supports：存在则加入，不存在则以非事务方式运行
  - mandatory（强制的）：存在则加入，不存在则抛出异常

- 不支持当前事务
  - required new：存在则挂起，创建一个新事务 
  - not supported：存在则挂起，以非事务方式运行
  - never：存在则抛出异常，以非事务方式运行
- 嵌入事务
  - nested：存在则创建一个当前事务 的嵌入事务运行，不存在则创建一个新事务

##### 事务注解@Transactional用过吗？

​	注解在类上，所有的公共方法都具备该类型的事务属性，抛出异常就会回滚

​	注解中如果不配置rollbackfor属性，则只会遇到运行时异常才会回滚，若该属性为Exception.class的话，可以在事务遇到非运行时异常也可以回滚

### Mybatis

##### 常用注解

<img src="C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210524172206153.png" alt="image-20210524172206153" style="zoom:80%;" width = "50%" />

##### mybatis优点

​	JDBC 相比，减少了 50%以上的代码量，消除了 JDBC 大量冗余的代码，不需要手动开关连接 很好的与各种数据库兼容（因为 MyBatis 使用 JDBC 来连接数据库，所以只要 JDBC 支持的数 据库 MyBatis 都支持，而 JDBC 提供了可扩展性，所以只要这个数据库有针对 Java 的 jar 包就 可以就可以与 MyBatis 兼容），开发人员不需要考虑数据库的差异性。 提供了很多第三方插件（分页插件 / 逆向工程） SQL 写在 XML 里，从程序代码中彻底分离，解除 sql 与程序代码的耦合，便于统一管理和优 化，并可重用。 提供映射标签，支持对象与数据库的 ORM 字段关系映射

### SpringBoot

https://lianghongbin.blog.csdn.net/article/details/115106450



##### 如何理解 Spring Boot 中的 Starters？Starters是什么：

Starters可以理解为启动器，它包含了一系列可以集成到应用里面的依赖包，
你可以一站式集成Spring及其他技术，而不需要到处找示例代码和依赖包。
如你想使用Spring JPA访问数据库，只要加入springboot-starter-data-jpa启动器依赖就能使用了。Starters包含了许多项目中需要用到的依赖，它们能快速持续的运行，
都是一系列得到支持的管理传递性依赖。

Spring Boot官方的启动器都是以spring-boot-starter-命名的，代表了一个特定的应用类型。

一般一个第三方的应该这样命名，像mybatis的mybatis-spring-boot-starter。

除此之外还有其他第三方启动器

————————————————
版权声明：本文为CSDN博主「三郎君」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/m0_51684972/article/details/110928657







- Spring Boot包含许多其他功能，可帮助您在将应用程序推送到生产环境时监视和管理应用程序。
- 您可以选择使用HTTP端点或JMX来管理和监视应用程序。
- 审核，运行状况和指标收集也可以自动应用于您的应用程序。



参数校验

https://blog.csdn.net/u013934408/article/details/108872775

https://www.cnblogs.com/javaguide/p/11832752.html



springboot微信文章合集

https://mp.weixin.qq.com/mp/appmsgalbum?__biz=Mzg2OTA0Njk0OA==&action=getalbum&album_id=1322577180722872320&scene=173&from_msgid=2247485568&from_itemidx=2&count=3&nolastread=1#wechat_redirect
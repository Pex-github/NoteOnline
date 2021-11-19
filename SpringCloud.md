## CAP

- C：Consistency (强一致性)
- A：Availability (可用性)
- P：Partition tolerance （分区容错性)



## Ribbon

Spring Cloud Ribbon是基于Netflix Ribbon实现的一套**客户端负载均衡的工具**。**客户端组件**

常见的负载均衡有软件Nginx，LVS，硬件F5等。

简单的说，Ribbon是Netflix发布的开源项目，主要功能是提供**客户端的软件负载均衡算法和服务调用**。Ribbon客户端组件提供一系列完善的配置项如连接超时，重试等。

**Ribbon本地负载均衡客户端VS Nginx服务端负载均衡区别**

Nginx是服务器负载均衡，客户端所有请求都会交给nginx，然后由nginx实现转发请求。即负载均衡是由服务端实现的。
Ribbon本地负载均衡，在调用微服务接口时候，会在注册中心上获取注册信息服务列表之后缓存到JVM本地，从而在本地实现RPC远程服务调用技术。

**Ribbon就属于进程内LB**，它只是一个类库，集成于消费方进程，消费方通过它来获取到服务提供方的地址。

**一句话**			负载均衡 + RestTemplate调用

负载策略

RoundRobinRule 轮询
RandomRule 随机
RetryRule 先按照RoundRobinRule的策略获取服务，如果获取服务失败则在指定时间内会进行重
WeightedResponseTimeRule 对RoundRobinRule的扩展，响应速度越快的实例选择权重越大，越容易被选择
BestAvailableRule 会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然后选择一个并发量最小的服务
AvailabilityFilteringRule 先过滤掉故障实例，再选择并发较小的实例
ZoneAvoidanceRule 默认规则,复合判断server所在区域的性能和server的可用性选择服务器

```
===pom===
spring-cloud-starter-netflix-eureka-client自带了spring-cloud-starter-ribbon引用
===注解===
启动类上	@RibbonClient(name = "CLOUD-PAYMENT-SERVICE", configuration = MySelfRule.class)
===负载规则配置类===
@Configuration
public class MySelfRule {

    @Bean
    public IRule myRule(){
        return new RandomRule();
    }
}

```

![image-20210710200656983](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210710200656983.png)

**部分源码解析**

在IRule接口中有一个方法choose方法

轮询类RoundRobinRule用的while循环实现的这个choose方法

用的是while循环选择服务，通过LoadBalancer获取服务实体列表，然后通过循环自增来实现轮询

里面还添加了一个参数用来判断列表中是否有可用的服务，当从负载均衡器尝试10次后，没有可用的活动服务器。就会警告并返回空



**自己实现轮询策略**

自己创建一个LoadBalancer接口类，并实现，返回ServiceInstance

```java
public final int getAndIncrement(){
        int current;
        int next;
        do {
            current = this.atomicInteger.get();
            next = current >= 2147483647 ? 0 : current + 1;
        }while(!this.atomicInteger.compareAndSet(current,next));
        System.out.println("*****第几次访问，次数next: "+next);
        return next;
}
    //负载均衡算法：rest接口第几次请求数 % 服务器集群总数量 = 实际调用服务器位置下标  ，每次服务重启动后rest接口计数从1开始。
@Override
public ServiceInstance instances(List<ServiceInstance> serviceInstances){
        int index = getAndIncrement() % serviceInstances.size();
        return serviceInstances.get(index);
}
```

Controller层

```java
@GetMapping(value = "/consumer/payment/lb")
    public String getPaymentLB()
    {
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");

        if(instances == null || instances.size() <= 0){
            return null;
        }

        ServiceInstance serviceInstance = loadBalancer.instances(instances);
        URI uri = serviceInstance.getUri();

        return restTemplate.getForObject(uri+"/payment/lb",String.class);

    }
https://blog.csdn.net/u011863024/article/details/114298270
```





## Consul

Consul是一个服务网格解决方案，它提供了一个功能齐全的控制平面，具有服务发现、配置和分段功能。这些特性中的每一个都可以根据需要单独使用，也可以一起用于构建全服务网格。Consul需要一个数据平面，并支持代理和本机集成模型。Consul船与一个简单的内置代理，使一切工作的开箱即用，但也支持第三方代理集成，如Envoy。
————————————————

开源的分布式服务发现和配置管理系统，提供了微服务系统中的服务治理、配置中心、控制总线等功能

- 服务发现 - 提供HTTP和DNS两种发现方式。
- 健康监测 - 支持多种方式，HTTP、TCP、Docker、Shell脚本定制化
- KV存储 - Key、Value的存储方式
- 多数据中心 - Consul支持多数据中心
- 可视化Web界面

```
===pom===
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
</dependency>
===yml===
spring:
  application:
    name: consul-provider-payment
####consul注册中心地址
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        #hostname: 127.0.0.1
        service-name: ${spring.application.name}
===注解===
启动类@EnableDiscoveryClient
```



三个注册中心的异同

| 组件名    | 语言CAP | 服务健康检查 | 对外暴露接口 | Spring Cloud集成 |
| --------- | ------- | ------------ | ------------ | ---------------- |
| Eureka    | Java    | AP           | 可配支持     | HTTP             |
| Consul    | Go      | CP           | 支持         | HTTP/DNS         |
| Zookeeper | Java    | CP           | 支持客户端   | 已集成           |



## Nacos

## OpenFeign

声明式WebService客户端

它的使用方法是**定义一个服务接口然后在上面添加注解**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

启动类		@EnableFeignClients

在service接口上添加注解		@FeignClient(value = "CLOUD-PAYMENT-SERVICE")

Feign**自带负载均衡配置项**

OpenFeign可以在消费端配置	**请求超时设置**，默认是1秒钟。

```yaml
#设置feign客户端超时时间(OpenFeign默认支持ribbon)(单位：毫秒)
ribbon: 
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ReadTimeout: 5000
  #指的是建立连接后从服务器读取到可用资源所用的时间
  ConnectTimeout: 5000
```

**日志增强**

简单来说，对Feign接口的调用情况进行监控和输出

日志级别

- NONE：默认的，不显示任何日志;
- BASIC：仅记录请求方法、URL、响应状态码及执行时间;
- HEADERS：除了BASIC中定义的信息之外，还有请求和响应的头信息;
- FULL：除了HEADERS中定义的信息之外，还有请求和响应的正文及元数据。

```java
//配置类
import feign.Logger;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FeignConfig
{
    @Bean
    Logger.Level feignLoggerLevel()
    {
        return Logger.Level.FULL;
    }
}
```

```yaml
longging: 
  level:
    com.lun.springcloud.service.PaymentFeignService: debug
```

## Hystrix

**分布式系统面临的问题**：某些时候，服务不可用，调用失败

A调用	B，C	|	B调用	X，Y	|	C调用	Z，U	。。。

在这条调用链路中，若某个微服务响应时间过长，或不可用。

则微服务	A	的调用就会占用越来越多的系统资源。

进而引起系统 崩溃		这就是所谓的		雪崩效应

功能：用于处理分布式系统的，延迟和容错

保证一个依赖出问题的情况下，不会导致整体服务失败，避免级联故，提高分布式系统的弹性

具体原理：在服务发生故障后 ，通过故障监控，向调用方，返回一个【符合预期，可处理的备用选项】。而不是长时间等待或一个无法处理的异常

实现的功能：

- 服务降级
- 服务熔断
- 监控

**服务降级**：服务器忙，避免等待，立即返回一个友好提示 ，【fallback】

发生降级的情况：服务运行异常，超时，熔断触发的降级，信号量打满

**服务熔断**：类似于保险丝，直接拒绝访问，然后服务降级方式返回友好提示

**服务限流**：适合秒杀等高并发环境，排队调用

##### 项目整合

```java
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>

#开启
feign:
  hystrix:
    enabled: true

启动类
@EnableHystrix

controller层 
@GetMapping("/consumer/payment/hystrix/timeout/{id}")
@HystrixCommand(fallbackMethod = "paymentTimeOutFallbackMethod",commandProperties = { @HystrixProperty(name="execution.isolation.thread.timeoutInMilliseconds",value="1500")
    })

```

##### **全局服务降级**

在controller类上添加注解

```java
@DefaultProperties(defaultFallback = "payment_Global_FallbackMethod")
//在需要注解的方法上添加
@HystrixCommand//用全局的fallback方法
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id) {
        //int age = 10/0;
        String result = paymentHystrixService.paymentInfo_TimeOut(id);
        return result;
    }
//并在该类里添加全局fallback方法
// 下面是全局fallback方法
    public String payment_Global_FallbackMethod()
    {
        return "Global异常处理信息，请稍后再试，/(ㄒoㄒ)/~~";
    }
```



##### **通配服务降级	**

**FeignFallback**

在客户端做服务降级，当遇到服务端宕机或 关闭，客户端自己调用提示 ，而不是挂起耗死服务器

配置信息

```xml
#开启
feign:
  hystrix:
    enabled: true
```

在	Fallback	接口上添加注解		PaymentHystrixService.java

```java
@Component
@FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT" ,
      fallback = PaymentFallbackService.class)//指定PaymentFallbackService类
public interface PaymentHystrixService
{
    @GetMapping("/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id);

    @GetMapping("/payment/hystrix/timeout/{id}")
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id);
}

//实现fallback方法接口
@Component
public class PaymentFallbackService implements PaymentHystrixService
{
    @Override
    public String paymentInfo_OK(Integer id)
    {
        return "-----PaymentFallbackService fall back-paymentInfo_OK ,o(╥﹏╥)o";
    }

    @Override
    public String paymentInfo_TimeOut(Integer id)
    {
        return "-----PaymentFallbackService fall back-paymentInfo_TimeOut ,o(╥﹏╥)o";
    }
}
```



##### 熔断理论

熔断机制是应对雪崩效应的一种微服务链路保护机制

响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用

**当检测到该节点微服务调用响应正常后，恢复调用链路**。

当失败的调用到一定阈值，默认是5秒内20次调用失败，就会启动熔断机制

熔断打开-----故障平均时长达到----》熔断半开-------如果符合条件--------》熔断关闭

**断路器**三个重要参数（时间窗口长度10秒，请求失败阈值，错误百分比阈值）



##### 工作流程

创建hystrixCommand对象

然后execute，或obseve方法执行。

若触发条件，请求缓存功能被启用，检查断路器状态，并且检查服务信号量是否沾满

根据策略将服务调用状态给断路器。断路器判断是否熔断

然后hystix尝试fallback友好提示

![image-20210711142456990](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210711142456990.png)

## GateWay

为微服务架构提供—种简单有效的统一的API路由管理方式

**SpringCloud Gateway是基于WebFlux框架实现的，而WebFlux框架底层则使用了高性能的Reactor模式通信框架Netty**。

提供统一的路由方式且基于 Filter链的方式提供了网关基本的功能，例如:安全，监控/指标，和限流

- 基于Spring Framework 5，Project Reactor和Spring Boot 2.0进行构建；
- 动态路由：能够匹配任何请求属性；
- 可以对路由指定Predicate (断言)和Filter(过滤器)；
- 集成Hystrix的断路器功能；
- 集成Spring Cloud 服务发现功能；
- 易于编写的Predicate (断言)和Filter (过滤器)；
- 请求限流功能；
- 支持路径重写。

##### 工作流程

![image-20210711150837129](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210711150837129.png)

1. 客户端向Spring Cloud Gateway发出请求。然后在Gateway Handler Mapping 中找到与请求相匹配的路由，将其发送到GatewayWeb Handler。
2. Handler再通过指定的过滤器链来将请求发送到我们实际的服务执行业务逻辑，然后返回。
3. 过滤器之间用虚线分开是因为过滤器可能会在发送代理请求之前(“pre”)或之后(“post"）执行业务逻辑。
4. Filter在“pre”类型的过滤器可以做参数校验、权限校验、流量监控、日志输出、协议转换等，在“post”类型的过滤器中可以做响应内容、响应头的修改，日志的输出，流量监控等有着非常重要的作用。

核心逻辑：路由转发 + 执行过滤器链。


##### 项目整合

```yaml
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
第一种，配置文件配置路由
spring: 
  cloud:
    gateway:
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001          #匹配后提供服务的路由地址
          #uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001          #匹配后提供服务的路由地址
          #uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
第二种，代码注入 
@Configuration
public class GateWayConfig
{
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder)
    {
        RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();

        routes.route("path_route_atguigu",
                r -> r.path("/guonei")
                        .uri("http://news.baidu.com/guonei")).build();

        return routes.build();
    }
}

##	uri	是转发后的		path   是请求过来的
```

##### 动态路由 

默认情况下Gateway会根据注册中心注册的服务列表，以注册中心上微服务名为路径创建**动态路由进行转发，从而实现动态路由的功能**（不写死一个地址）

在配置文件中pom.xml，具体配置看上面代码块

需要注意的是uri的协议为lb，表示启用Gateway的负载均衡功能。

lb://serviceName是spring cloud gateway在微服务中自动为我们创建的负载均衡uri。

##### Predicate

gateway包含了许多内置的Route Predicate工厂。所有这些Predicate都与HTTP请求的不同属性匹配

Spring Cloud Gateway创建Route 对象时，使用RoutePredicateFactory 创建 Predicate对象，Predicate 对象可以赋值给Route。Spring Cloud Gateway包含许多内置的Route Predicate Factories。
所有这些谓词都匹配HTTP请求的不同属性。多种谓词工厂可以组合，并通过逻辑and。

说白了，Predicate就是**为了实现一组匹配规则，让请求过来找到对应的Route进行处理**。

可以通过时间【After】，cookie【Cookie】，host【host】，请求方式【Method】 ，请求路径【Path】，请求参数【Query，ip地址【RemoteAddr】 	来匹配

也可以组合使用

##### Filter

路由过滤器可用于修改进入的HTTP请求和返回的HTTP响应，路由过滤器只能指定路由进行使用。Spring Cloud Gateway内置了多种路由过滤器，他们都由GatewayFilter的工厂类来产生。

用来：

1. 全局日志记录
2. 统一网关鉴权

常用的GatewayFilter：AddRequestParameter，GatewayFilter，自定义全局GlobalFilter

```java
@Component
@Slf4j
public class MyLogGateWayFilter implements GlobalFilter,Ordered
{

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain)
    {
        log.info("***********come in MyLogGateWayFilter:  "+new Date());

        String uname = exchange.getRequest().getQueryParams().getFirst("uname");

        if(uname == null)
        {
            log.info("*******用户名为null，非法用户，o(╥﹏╥)o");
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();
        }
        return chain.filter(exchange);
    }
    //过滤掉没有uname参数的请求
}
```

## zuul



##### 项目整合+路由配置

添加依赖，【Cloud Discovery->Eureka Discovery和Cloud Rouing->Zuul】

启动类加注解	@EnableZuulProxy

yml

```yaml
spring:
  application:
    name: gateway-zuul-demo
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/,http://localhost:8762/eureka/
  instance:
    prefer-ip-address: true
>路由配置<    
zuul:
  routes:
    route-name:			#路由 别名
      url: http://localhost:8000/	#映射后的地址
      path: /rest/**			#请求过来的 
      
#	ignored-service		忽略指定 微服务
#	rest-demo:/rest/**		表示将rest-demo微服务的地址映射到/rest/**路径
>同时可以使用Hystrix，Ribbon特性
>借助PatternServiceRouteMapper实现路由的正则匹配
	return new PatternServiceRouteMapper("(?<name>^.+)-(?<version>v.+$)", "${version}/${name}");	====》将rest-demo-v1映射为/v1/rest-demo/**

>路由前缀
zuul:
  prefix: /api
  strip-prefix: true
#此时访问Zuul的/api/rest/user/xdlysk会被转发到/rest-demo/user/xdlysk
>忽略某些微服务中的某些路径
zuul:
  ignoredPatterns: /**/user/* #忽略所有包含/user/的地址请求
```

##### Filter

Zuul Filter有以下几个特征：

- Type：用以表示路由过程中的阶段（内置包含PRE、ROUTING、POST和ERROR）
- Execution Order：表示相同Type的Filter的执行顺序
- Criteria：执行条件
- Action：执行体

<img src="C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210712160410394.png" alt="image-20210712160410394" style="zoom: 67%;" />

<img src="C:\Users\PEX\Pictures\zuul.png" alt="zuul" style="zoom:67%;" />

route**过滤器常用的几个类**	

可以继承以下类，完成业务需求 

RibbonXXXFilter		路由到服务

SimpleHostRoutingFilter		从旧的url转到新的url地址

SendForwardFilter	转发	（转向zuul自己）

| 类型  | 顺序 | 过滤器                  | 功能                       |
| :---- | :--- | :---------------------- | :------------------------- |
| pre   | -3   | ServletDetectionFilter  | 标记处理Servlet的类型      |
| pre   | -2   | Servlet30WrapperFilter  | 包装HttpServletRequest请求 |
| pre   | -1   | FormBodyWrapperFilter   | 包装请求体                 |
| route | 1    | DebugFilter             | 标记调试标志               |
| route | 5    | PreDecorationFilter     | 处理请求上下文供后续使用   |
| route | 10   | RibbonRoutingFilter     | serviceId请求转发          |
| route | 100  | SimpleHostRoutingFilter | url请求转发                |
| route | 500  | SendForwardFilter       | forward请求转发            |
| post  | 0    | SendErrorFilter         | 处理有错误的请求响应       |
| post  | 1000 | SendResponseFilter      | 处理正常的请求响应         |

##### 自定义网关

```java
//该网关将旧的URL	通过网关过滤器		发送到新的服务地址
//然后修改启动类用@Bean注解	将filter注入
//重写的四个方法
filterType：返回一个字符串代表过滤器的类型
filterOrder：通过int值来定义过滤器的执行顺序
    【FilterLoader.java的getFilterByType中按类型pre、route、post取Filter后，再按照FilterOrder进行排序。】
shouldFilter：返回一个boolean类型来判断该过滤器是否要执行
    【在db中存储过滤器的开关】【当项目已经线上跑了，有某种需求要开关过滤器】【可以在后台管理系统上配置过滤器开关。目的是不重启，让过滤器生效】【在开放平台刚上线生产的时候，不过多开过滤器，等用户较多的时候，开启过滤器，限制用户的频繁操作】
run：过滤器的具体逻辑
@Component
public class CommonServicePathFilter extends ZuulFilter {
    private final static String GETWAY_FOWARD_PREFIX="getway_forward_";

    private final static String GETWAY_COMPAY_CONFIG_KEY = "getway_company";

    @Autowired
    private RedisTemplate redisTemplate;
    @Override
    public String filterType() {
       //这里很重要，必须是route
        return "route";
    }
    @Override
    public int filterOrder() {
        return 1;
    }
	@Override
    public boolean shouldFilter() {
       return true;
    }	
    @Override	//实际代码区
    public Object run() throws ZuulException {
        RequestContext ctx = RequestContext.getCurrentContext();
        String url = ctx.getRequest().getRequestURI();
        Map<String,String> forwardMap =  getForwardMap(url);
        if(forwardMap != null){
            String fowardUrl = forwardMap.get(url);
            String serviceId = getServiceId(fowardUrl);
            String requestUrl = getRequestUrl(fowardUrl,serviceId);
              //1.设置目标service的Controller的路径
            ctx.put(FilterConstants.REQUEST_URI_KEY,requestUrl);
             //2.设置目标service的serviceId
            ctx.put(FilterConstants.SERVICE_ID_KEY,serviceId);
        }
        return null;
    }
    private String getServiceId(String url){
        if(url.startsWith("/")){
            String temp = url.substring(1);
            return temp.split("/")[0];
        }else{
            return null;
        }
    }
    private String getRequestUrl(String url,String serviceId){
        return url.substring(serviceId.length() +1);
    }
    private Map<String,String> getForwardMap(String originalUrl){
           //todo:这里是返回一个map，传入一个originUrl,返回一个要转发的url
    }

}
```



#### **生产中出现的问题**

token，请求头等信息无法向后传		在配置文件中，修改 敏感信息的配置 sensitive	

老项目改造，原来的url无法匹配服务，用网关去适应

动态路由	不同的用户匹配不同的服务

404找不到地址

网关中的地址来源：

1.eureka服务，zuul从eureka获取的服务

2.配置文件中定义

如何使	网关	**高可用方案**

​	先用nginx，做负载均衡，在发送到网关上

​	将网关服务注册在Eureka Server

​	或者从eureka server处获取zuul网关地址实现客户端 负载均衡

过滤器设计好的执行顺序，减少过多的资源消耗

导入actuator包，在yml上配置好 ，监控端口信息

​	/actuator/routes	查看路由地址

​	/actuator/filter		查看过滤器

## Config

分布式配置中心，为微服务架构中的微服务提供集中化的外部配置支持，配置服务器为各个不同微服务应用的所有环境提供了一个中心化的外部配置。

分为**服务端**【配置中心】和**客户端**【管理服务配置 ，拉取配置信息】

```
1）在github创建一个库
2）用git命令	git clone +地址
3）创建配置文件
	config-dev.yml
	config-prod.yml
	config-test.yml
4)用git命令上传提交，	git add		git commit	
```

##### 项目整合

##### 搭建配置中心服务端

```yaml
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
</dependency>

spring: 
  cloud:
    config:
      server:
        git:
          uri: git@github.com:zzyybs/springcloud-config.git #GitHub上面的git仓库名字
        ####搜索目录
          search-paths:
            - springcloud-config
      ####读取分支
      label: master
```

配置信息访问，规则

/{label}/{application}-{profile}.yml

​	localhost:3344/master/config-dev.yml		【master分支】

​	localhost:3344/dev/config-dev.yml				【dev分支】

##### 搭建客户端配置服务 

bootstrap.yml	是application context	的父上下文

初始化时，bootstrap从外部源加载配置信息并解析

Bootstrap优先级更高，同时不会覆盖Application，所以bootstrap.yml先加载

```yaml
spring:
  application:
    name: config-client
  cloud:
    #Config客户端配置
    config:
      label: master #分支名称
      name: config #配置文件名称
      profile: dev #读取后缀名称   上述3个综合：master分支上config-dev.yml的配置文件被读取http://config-3344.com:3344/master/config-dev.yml
      uri: http://localhost:3344 #配置中心地址k
      
#配置类
@RestController
@RefreshScope
public class ConfigClientController
{
    @Value("${config.info}")
    private String configInfo;

    @GetMapping("/configInfo")
    public String getConfigInfo()
    {
        return configInfo;
    }
}
```

##### 配置的动态刷新问题

客户端的动态刷新

1. #### 引入actuator监控依赖

2. #### 修改YML，新增暴露监控端口配置

3. #### Controller层添加 @RefreshScope 注解

4. 发送post请求     curl -X POST "http://localhost:3355/actuator/refresh"



## Bus消息总线

分布式自动刷新配置功能

配合Spring Cloud Config使用可以实现配置的动态刷新

用来将分布式系统的节点与轻量级消息系统链接起来的框架，它整合了Java的事件处理机制和消息中间件的功能。Spring Clud Bus目前支持RabbitMQ和Kafka。

在微服务架构的系统中，通常会使用轻量级的消息代理来构建一个共用的消息主题，并让系统中所有微服务实例都连接上来。由于该主题中产生的消息会被所有实例监听和消费，所以称它为**消息总线**。

基本原理

ConfigClient实例都监听MQ中同一个topic(默认是Spring Cloud Bus)。当一个服务刷新数据的时候，它会把这个信息放入到Topic中，这样其它监听同一Topic的服务就能得到通知，然后去更新自身的配置

**动态刷新**	======》全局广播		**—次修改，广播通知，处处生效**

**动态刷新**   ======》定点通知	**指定具体某一个实例生效而不是全部**



## Stream	消息

Spring Cloud Stream是一个构建消息驱动微服务的框架。

目前仅支持RabbitMQ、 Kafka。

```yaml
dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
</dependency>
spring: 
  cloud:
      stream:
        binders: # 在此处配置要绑定的rabbitmq的服务信息；
          defaultRabbit: # 表示定义的名称，用于于binding整合
            type: rabbit # 消息组件类型
            environment: # 设置rabbitmq的相关的环境配置
              spring:
                rabbitmq:
                  host: localhost
                  port: 5672
                  username: guest
                  password: guest
        bindings: # 服务的整合处理
          output: # 这个名字是一个通道的名称
            destination: studyExchange # 表示要使用的Exchange名称定义
            content-type: application/json # 设置消息类型，本次为json，文本则设置“text/plain”
            binder: defaultRabbit # 设置要绑定的消息服务的具体设置
```

```java
//生产者
业务实现类

@EnableBinding(Source.class) //定义消息的推送管道
public class MessageProviderImpl implements IMessageProvider
{
    @Resource
    private MessageChannel output; // 消息发送管道

    @Override
    public String send()
    {
        String serial = UUID.randomUUID().toString();
        output.send(MessageBuilder.withPayload(serial).build());
        System.out.println("*****serial: "+serial);
        return null;
    }
}

//消费者
@Component
@EnableBinding(Sink.class)
public class ReceiveMessageListenerController
{
    @Value("${server.port}")
    private String serverPort;


    @StreamListener(Sink.INPUT)
    public void input(Message<String> message)
    {
        System.out.println("消费者1号,----->接受到的消息: "+message.getPayload()+"\t  port: "+serverPort);
    }
}
当生产者与消费者存在	一对多关系
就会存在========消息重复消费问题
解决方法：Stream中的消息分组来解决
同组内，只能有一个客户端消费，不同组可以重复消费
同时，有分组属性的配置的微服务能够消费MQ队列上的消息		消息持久化的体现

```

## Sleuth 服务追踪

提供了一套完整的服务跟踪的解决方案

在分布式系统中提供追踪解决方案并且兼容支持了zipkin

一条链路通过Trace ld唯一标识，Span标识发起的请求信息，各span通过parent id关联起来

- Trace：类似于树结构的Span集合，表示一条调用链路，存在唯一标识
- span：表示调用链路来源，通俗的理解span就是一次请求信息

```yaml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
 zipkin: #<-------------------------------------关键 
      base-url: http://localhost:9411
  sleuth: #<-------------------------------------关键
    sampler:
    #采样率值介于 0 到 1 之间，1 则表示全部采集
    probability: 1
```

```
不需要自己构建Zipkin Server了，只需调用jar包即可
服务器上运行    java -jar zipkin-server-2.12.9-exec.jar
http://localhost:9411	访问服务追踪后台，查看调用链路
```

# Alibaba

## Nacos

一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

注册 中心 ，配置 中心

**Nacos支持AP和CP模式的切换**

| 服务注册与发现框架 | CAP模型 | 控制台管理 | 社区活跃度      |
| ------------------ | ------- | ---------- | --------------- |
| Eureka             | AP      | 支持       | 低(2.x版本闭源) |
| Zookeeper          | CP      | 不支持     | 中              |
| consul             | CP      | 支持       | 高              |
| Nacos              | AP      | 支持       | 高              |

父pom

```xml
<dependencyManagement>
    <dependencies>
        <!--spring cloud alibaba 2.1.0.RELEASE-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2.1.0.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
原文链接：https://blog.csdn.net/u011863024/article/details/114298282
```

提供者模块pom

```xml
<dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>

spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址
@EnableDiscoveryClient
@SpringBootApplication
public class PaymentMain9001 {
    public static void main(String[] args) {
            SpringApplication.run(PaymentMain9001.class, args);
    }
}
```

nacos集群-负载均衡

为什么nacos支持负载均衡？因为spring-cloud-starter-alibaba-nacos-discovery内含netflix-ribbon包。

![image-20210714153359631](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210714153359631.png)





## JMeter	压测

Apache JMeter™应用程序是开源软件，是一个100%纯Java应用程序，设计用于加载测试功能行为和测量性能。它最初是为测试Web应用程序而设计的，但后来扩展到其他测试功能。

**降级配置**		同时还要在启动 类添加	@EnableCircuitBreaker

```java
@HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler"/*指定善后方法名*/,commandProperties = {          @HystrixProperty(name="execution.isolation.thread.timeoutInMilliseconds",value="3000")
    })
    public String paymentInfo_TimeOut(Integer id)
    {
        //int age = 10/0;
        try { TimeUnit.MILLISECONDS.sleep(5000); } catch (InterruptedException e) { e.printStackTrace(); }
        return "线程池:  "+Thread.currentThread().getName()+" id:  "+id+"\t"+"O(∩_∩)O哈哈~"+"  耗时(秒): ";
    }
    //用来善后的方法
    public String paymentInfo_TimeOutHandler(Integer id)
    {
        return "线程池:  "+Thread.currentThread().getName()+"  8001系统繁忙或者运行报错，请稍后再试,id:  "+id+"\t"+"o(╥﹏╥)o";
    }
```


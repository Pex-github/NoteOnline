### Register：服务注册

当Eureka客户端向Eureka Server注册时，它提供自身的元数据，比如IP地址、端口，运行状况指示符URL，主页等。，没有设置serviceID，就用application Name替代。具体实现类是DiscoveryClient类，它实现了EurekaClient接口并且是个单例，服务注册是该类下的register方法，将实例信息用过http请求来注册。同时 在DiscoveryClient初始化时创建的类InstanceInfoReplicator，在需要向服务注册时，开启定时任务InitScheduledTasks方法，向服务续约。在服务端有ApplicationResource类的addInstance方法提供注册接口，方法体中调用了PeerAwareInstanceRegistryImpl的register()方法



### Renew：服务续约

Eureka客户会每隔30秒发送一次心跳来续约。 通过续约来告知Eureka Server该Eureka客户仍然存在，没有出现问题。 正常情况下，如果Eureka Server在90秒没有收到Eureka客户的续约，它会将实例从其注册表中删除。 建议不要更改续约间隔。在DiscoveryClient类下 有renew方法，服务端有InstanceResource类下有renewLease方法接受服务续约



### Fetch Registries：获取注册列表信息

Eureka客户端从服务器获取注册表信息，并将其缓存在本地。客户端会使用该信息查找其他服务，从而进行远程调用。该注册列表信息定期（每30秒钟）更新一次。每次返回注册列表信息可能与Eureka客户端的缓存信息不同， Eureka客户端自动处理。如果由于某种原因导致注册列表信息不能及时匹配，Eureka客户端则会重新获取整个注册表信息。 Eureka服务器缓存注册列表信息，整个注册表以及每个应用程序的信息进行了压缩，压缩内容和没有压缩的内容完全相同。Eureka客户端和Eureka 服务器可以使用JSON / XML格式进行通讯。在默认的情况下Eureka客户端使用压缩JSON格式来获取注册列表的信息。一般服务续约 有两个参数可以调试，clieant发送 心跳间隔的时间参数和服务端剔除实例的时间参数。但一般不建议修改

分为全量拉去 和增量拉去，（recentlyChangedQueue）增量拉去3分钟的注册列表

### Cancel：服务下线

Eureka客户端在程序关闭时向Eureka服务器发送取消请求。 发送请求后，该客户端实例信息将从服务器的实例注册表中删除。该下线请求不会自动完成，它需要调用以下内容：DiscoveryManager.getInstance().shutdownComponent()；



### Eviction 服务剔除

在默认的情况下，当Eureka客户端连续90秒没有向Eureka服务器发送服务续约，即心跳，Eureka服务器会将该服务实例从服务注册列表删除，即服务剔除。



### Replicate 状态更新

集群情况下，server之间会发送Replicate，采用**复制**的方式进行注册信息的同步，但设计时先保证可用性，几个节点 挂掉也不会影响正常节点工作

基本流程如下：

InstanceResource类的statusUpdate()方法，是状态更新的入口，用来处理状态更新请求。

然后，调用PeerAwareInstanceRegistryImpl类的statusUpdate()方法，在该过程主要实现状态更新事件同步到集群中的其他节点

最后，调用AbstractInstanceRegistry类的statusUpdate()方法，该方法就是更新实例状态的真正方法。



### 自我保护模式

当一个server出现，会尝试向peer节点获取所有 的实例信息。获取成功则更新阈值。若服务端接受的续约在15分钟内低于百分之85则服务器开启自我保护，不在剔除注册列表信息



### eurakeClient注册一个实例为什么这么慢？

在DafaultEurekaClientConfig类下有一个延迟向服务端注册 的方法，默认延迟时间为40秒。客户端本地缓存的注册表信息需要30秒才会刷新发现其他新注册的实例



### Eureka区域问题

如果用户量比较大或地理分布比较广，为了保证服务 的可用，可以调取其他区域的服务，一般用region划分，再用zone划分具体的机房

服务注册：要保证服务注册到同一个zone内的注册中心，因为如果注册到别zone的注册中心的话，网络延时比较大，心跳检测很可能出问题。
服务调用：要保证优先调用同一个zone内的服务，只有在同一个zone内的服务不可用时，才去调用别zone的服务。

服务端配置：

机房：Service

```yml
prefer-same-zone-eureka: true
region: beijing
	availability-zones:
	beijing: zone-1,zone-2  #顺序不同，注册位置不同
service-url:
zone-1: http://localhost:30000/eureka/
zone-2: http://localhost:30001/eureka/

metadata-map:    #指定消费者 和提供者 属于哪个zone
	zone: zone-1
```

注意：以上配置信息消费者和提供者都要写，为了保证服务注册到同一个zone的注册中心，一定要注意availability-zones的顺序，必须把同一zone写在前面

### 你在生产中遇到哪些问题？

##### 下线重现问题

我在向注册中心发送delete请求下线再关闭微服务后，发现server中还是存在注册的实例，等于白下线。后来我debug发现内部运行机制：客户端每个30秒续约，如果注册列表中没有，会重新注册。所以没有及时停掉的话，服务又会再次回到服务列表中？



##### 集群搭建问题

集群搭建完成后，发现服务节点均在unavailable-replicas下，即说明集群搭建失败，各节点之间不能互相通信，网上查找了各种资料，终于解决，现将问题处理的过程记录如下：

1. 检查各节点的`spring.application.name必须一样`

2. `eureka.instance.hostname``应该与host文件中的映射相同`

3. defaultZone中的url，不能包含自己，包含其他服务器节点，如：http://hostname/端口/eureka

4. 开启自注册和互相注册

5. ```yml
   ---
   spring:
     profiles: eureka-7900
   server:
     port: 7900
   eureka:
     instance:
       hostname: eureka-7900
     client:
       #表示是否将自己注册进EurekaServer默认为true。
       register-with-eureka: true
       #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
       fetch-registry: true
       service-url:
         # 5 24
         defaultZone: http://eureka-7901:7901/eureka/,http://eureka-7902:7902/eureka/
   ```


##### eureka优化问题

1. 不同数量服务的自我保护优化

   1. 配置参数：自我保护阈值，剔除间隔，续约间隔，阈值更新间隔，自我保护开关
   2. 服务数量比较少，不建议开启自我保护，可以将剔除间隔时间设置短一点，及时剔除不可用服务，使服务 快速下线。
   3. 服务数比较大，建议开启自我保护。

2. 三级缓存优化

   1. 原理流程：定时任务每 30s 将 readWriteCacheMap 同步至 readOnlyCacheMap，每 60s 清理超过 90s 未续约的节点，Eureka Client 每 30s 从 readOnlyCacheMap 拉取服务注册信息，而服务的注册则在 registry 更新信息
      包含了三个变量registry、readWriteCacheMap、readOnlyCacheMap 保存服务注册信息

      优点：保证了内存中注册表不会出现读写冲突问题，提升接受请求的性能

   2. 生产环境的弱一致性问题。默认从readOnlyCacheMap中读取， readWriteCacheMap每隔30s才同步到readOnlyCacheMap，数据不是强一致性的，这也是没有 实现C的原因。

   3. 优化：关闭从readOnly读注册表，直接去readWriteCacheMap中读。减少这两张表同步间隔时间实现快速剔除 （服务快速上线，快速下线）

   

3. 客户端优化

   拉取注册表间隔时间（刷新注册表间隔）

   续约间隔时间都可以优化（心跳）

   饥饿加载，防止第一次请求超时



##### 生产细节

注册中心的Service-url是按照顺序注册拉去注册 列表的。为了不给首个的注册中心制造压力 ，必须把配置 文件中的url全部打乱



##### 集群搭建

集群搭建样式

![ScreenClip [2]](C:\Users\PEX\Desktop\image\ScreenClip [2].png)



集群搭建成功

![ScreenClip](C:\Users\PEX\Desktop\image\ScreenClip.png)



单服务开集群yml

```yml
spring:
  application:
    name: cloud-eureka
eureka:
  client:
    service-url:
      defaultZone: http://eureka-7900:7900/eureka/,http://eureka-7901:7901/eureka/,http://eureka-7902:7902/eureka/

---
spring:
  profiles: eureka-7900
server:
  port: 7900
eureka:
  instance:
    hostname: eureka-7900
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
      # 5 24
      defaultZone: http://eureka-7901:7901/eureka/,http://eureka-7902:7902/eureka/


---
spring:
  profiles: eureka-7901
server:
  port: 7901
eureka:
  instance:
    hostname: eureka-7901
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      # 5 24
      defaultZone: http://eureka-7900:7900/eureka/,http://eureka-7902:7902/eureka/
---
spring:
  profiles: eureka-7902
server:
  port: 7902
eureka:
  instance:
    hostname: eureka-7902
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      # 5 24
      defaultZone: http://eureka-7900:7900/eureka/,http://eureka-7901:7901/eureka/
```

host：

127.0.0.1	eureka-7900
127.0.0.1	eureka-7901
127.0.0.1	eureka-7902



区域	https://www.cnblogs.com/kebibuluan/p/13265345.html



原文链接：https://blog.csdn.net/forezp/article/details/73017664



原文链接：https://blog.csdn.net/hou_ge/article/details/111291991
##### 能简单描述下redis吗

​	他是一个c语言编写的开源，基于内存，可选持久化，支持网络的键值对存储数据库，他拥有多种数据结构并且性能高。同时支持数据备份，即主从复制

##### redis的key支持哪些数据结构

​	String	可以是字符串，数字，二进制，他的最大存储容量是512M

​	命令：set/get	setex/setnx/	incr/decr	getbit/setbit	getset		

   			 setrange/getrange	mget/mset

​	hash	字段+值，适合存储对象

​	list	按照插入顺序的字符串链表（双向链表），主要命令是 LPUSH 和 RPUSH，能够支 持反向查找和遍历

​	set	用哈希表类型的字符串序列，没有顺序，集合成员是唯一的，没有重复数据，底 层主要是由一个 value 永远为 null 的 hashmap 来实现的。

​	zset	zset 类型：和 set 类型基本一致，不过它会给每个元素关联一个 double 类型的分数（score）， 这样就可以为成员排序，并且插入是有序的。

##### 怎么保证 redis 和 db 中的数据一致

读操作：对于不要求强一致性的读请求，走redis，强一致性的数据走mysql，读的时候，先读缓存；如果没有的话，就读数据库，同时将数据放入缓存，并返回响应

写操作：数据先写到数据库中，在更新redis

更新操作：**先删除缓存，然后再更新数据库**。

##### redis 实现原理 



##### 过期键的删除策略？

1、定时删除:在设置键的过期时间的同时，创建一个定时器 timer). 让定时器在键 的过期时间来临时，立即执行对键的删除操作。 

2、惰性删除:放任键过期不管，但是每次从键空间中获取键时，都检查取得的键是 否过期，如果过期的话，就删除该键;如果没有过期，就返回该键。 

3、定期删除:每隔一段时间程序就对数据库进行一次检查，删除里面的过期键。至 于要删除多少过期键，以及要检查多少个数据库，则由算法决定

##### redis持久化

redis 的持久化方式有两种： 

​	RDB（半持久化方式）

​	按照配置不定期的通过异步的方式、快照的形式直接把内存中的数 据持久化到磁盘的一个 dump.rdb 文件（二进制的临时文件）中，可以通过配置文件来修改 Redis 服务器 dump 快照的频率。

这是redis 默认的持久化方式， 它在配置文件（redis.conf）中。 **优点**：只包含一个文件，将一个单独的文件转移到其他存储媒介上，对于文件备份、灾难恢 复而言，比较实用。 **缺点**：系统一旦在持久化策略之前出现宕机现象，此前没有来得及持久化的数据将会产生丢 失

​	AOF（全持久化的方式）	

​	把每一次数据变化都通过 write()函数将你所执行的命令追加到一个 appendonly.aof 文件里 面，Redis 默认是不支持这种全持久化方式的，需要在配置文件（redis.conf）中将 appendonly no 改成 appendonly yes 优点：数据安全性高，对日志文件的写入操作采用的是 append 模式，因此在写入过程中即 使出现宕机问题，也不会破坏日志文件中已经存在的内容； 缺点：对于数量相同的数据集来说，aof 文件通常要比 rdb 文件大，因此 rdb 在恢复大数据 集时的速度大于 AOF；

**二种持久化方式区别**：

 AOF 在运行效率上往往慢于 RDB，每秒同步策略的效率是比较高的，同步禁用策略的效率和 RDB 一样高效； 如果缓存数据安全性要求比较高的话，用 aof 这种持久化方式（比如项目中的购物车）； 如果对于大数据集要求效率高的话，就可以使用默认的。而且这两种持久化方式可以同时使 用。



##### redis 有事务吗？ 

事务是一个单独 的隔离操作，包含一组命令且按顺序执行，执行不被其他命令打断

Redis 是有事务的，redis 中的事务是一组命令的集合，这组命令要么都执行，要不都不执行， 保证一个事务中的命令依次执行而不被其他命令插入。redis 的事务是不支持回滚操作的。 redis 事务的实现，需要用到 MULTI（事务的开始）和 EXEC（事务的结束）命令 ;

##### 缓存穿透

 缓存查询一般都是通过 key 去查找 value，如果不存在对应的 value，就要去数据库中查找。 如果这个 key 对应的 value 在数据库中也不存在，并且对该 key 并发请求很大，就会对数据 库产生很大的压力，这就叫缓存穿透 

​	解决方案： 1.对所有可能查询的参数以 hash 形式存储，在控制层先进行校验，不符合则丢弃。 2.将所有可能存在的数据哈希到一个足够大的 bitmap 中，一个一定不存在的数据会被这个 bitmap 拦截掉，从而避免了对底层存储系统的查询压力。 3. 如果一个查询返回的数据为空（不管是数 据不存在，还是系统故障），我们仍然把这个 空结果进行缓存，但它的过期时间会很短，最长不超过五分钟。 

##### 缓存雪崩

 当缓存服务器重启或者大量缓存集中在一段时间内失效，发生大量的缓存穿透，这样在失效 的瞬间对数据库的访问压力就比较大，所有的查询都落在数据库上，造成了缓存雪崩。 这 个没有完美解决办法，但可以分析用户行为，尽量让失效时间点均匀分布。大多数系统设计 者考虑用加锁或者队列的方式保证缓存的单线程（进程）写，从而避免失效时大量的并发请 求落到底层存储系统上。 

​	解决方案： 1.在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对某个 key 只 允许一个线程查询数据和写缓存，其他线程等待。 2.可以通过缓存 reload 机制，预先去更新缓存，再即将发生大并发访问前手动触发加载缓存 3.不同的 key，设置不同的过期时间，让缓存失效的时间点尽量均匀 4.做二级缓存，或者双缓存策略。A1 为原始缓存，A2 为拷贝缓存，A1 失效时，可以访问 A2，A1 缓存失效时间设置为短期，A2 设置为长期。



##### 哨兵机制

监控：监控主数据库和从数据库是否正常运行； 提醒：当被监控的某个 redis 出现问题的时候，哨兵可以通过 API 向管理员或者其他应用程 序发送通知； 自动故障迁移：主数据库出现故障时，可以自动将从数据库转化为主数据库，实现自动切换； 具体的配置步骤参考的网上的文档。要注意的是，如果 master 主服务器设置了密码，记得 在哨兵的配置文件（sentinel.conf）里面配置访问密码



##### redis安全机制，你们公司 redis 的安全这方面怎么考虑 的？

漏洞介绍：redis 默认情况下，会绑定在 bind 0.0.0.0:6379，这样就会将 redis 的服务暴露到 公网上，如果在没有开启认证的情况下，可以导致任意用户在访问目标服务器的情况下，未 授权就可访问 redis 以及读取 redis 的数据，攻击者就可以在未授权访问 redis 的情况下可以 利用 redis 的相关方法，成功在 redis 服务器上写入公钥，进而可以直接使用私钥进行直接登 录目标主机



##### redis cluster 实现原理 

redis cluster是Redis的分布式解决方案

- 为什么要实现Redis Cluster

```
1.主从复制不能实现高可用
2.随着公司发展，用户数量增多，并发越来越多，业务需要更高的QPS，而主从复制中单机的QPS可能无法满足业务需求
3.数据量的考虑，现有服务器内存不能满足业务数据的需要时，单纯向服务器添加内存不能达到要求，此时需要考虑分布式需求，把数据分布到不同服务器上
4.网络流量需求：业务的流量已经超过服务器的网卡的上限值，可以考虑使用分布式来进行分流
5.离线计算，需要中间环节缓冲等别的需求
```

- 数据分区

顺序分区，哈希分区（节点数取余，一致性哈希，虚拟槽分区），

虚拟槽分区是Redis Cluster采用的分区方式，预设虚拟槽，每个槽就相当于一个数字，有一定范围。每个槽映射一个数据子集，一般比节点数大

1. 把16384槽按照节点数量进行平均分配，由节点进行管理 
2. 对每个key按照CRC16规则进行hash运算 
3. 把hash结果对16383进行取余 
4. 把余数发送给Redis节点 
5. 节点接收到数据，验证是否在自己管理的槽编号的范围    如果在自己管理的槽编号范围内，则把数据保存到数据槽中，然后返回执行结果    如果在自己管理的槽编号范围外，则会把数据发送给正确的节点，由正确的节点来把数据保存在对应的槽中

※

节点之间互相通信，每个节点都负责读写操作，每个节点负责一部分槽和相关数据，实现数据和请求的负载均衡

搭建Redis Cluster划分四个步骤：准备节点，meet操作，分配槽，复制数据。

Redis官方推荐使用redis-trib.rb工具快速搭建Redis Cluster

集群伸缩通过在节点之间移动槽和相关数据实现    扩容时根据槽迁移计划把槽从源节点迁移到新节点    收缩时如果下线的节点有负责的槽需要迁移到其他节点，再通过cluster forget命令让集群内所有节点忘记被下线节点

使用smart客户端操作集群过到通信效率最大化，客户端内部负责计算维护键，槽以及节点的映射，用于快速定位到目标节点

集群自动故障转移过程分为故障发现和节点恢复。节点下线分为主观下线和客观下线，当超过半数节点认为故障节点为主观下线时，标记这个节点为客观下线状态。从节点负责对客观下线的主节点触发故障恢复流程，保证集群的可用性

##### redis 数据类型 string 和 list 都有什么适 用场景 

![image-20210618152554252](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210618152554252.png)

##### Codis 相关 

Codis 是一个分布式 Redis 解决方案

Codis由四部分组成

- Codis-proxy	实现redis协议,由于本身是无状态的,因此可以部署很多个节点
- Codis-config	是codis的管理工具,包括添加/删除redis节点添加/删除proxy节点,发起数据迁移等操作,自带httpserver,支持管理后台方式管理配置
- Codis-server	是codis维护的redis分支,基于2.8.21分支,加入了slot的支持和原子的数据迁移指令; codis-proxy和codis-config只能和这个版本的redis交互才能正常运行
- Zookeeper	用于codis集群元数据的存储,维护codis集群节点

##### redis 与 memcached 区别 

​	redis支持集群，拥有主从模式 和哨兵提升性能		memcached本身不支持分布式，只能通过一致性哈希的分布式算法实现分布式存储

​	两者 的内存管理机制村通，redis要简单很多，memcached损耗内存

##### redis 中 SortedSet 

sort set和set类型一样，也是string类型元素的集合，也没有重复的元素，不同的是sort set每个元素都会关联一个权，通过权值可以有序的获取集合中的元素

##### redis 主从复制过程，同步还是异步

异步 

##### redis 主从是怎么选取的 redis 插槽的分配

虚拟槽分区

key的有效部分使用CRC16算法计算出哈希值，再将哈希值对16384取余，得到插槽值。

##### redis 主节点宕机了怎么办，还有没有同步的数据怎么办？ 

连上从库，做save操作。将会在从库的data目录保存一份从库最新的dump.rdb文件。将这份dump.rdb文件拷贝到主库的data目录下。再重启主库。

减少数据丢失方案

配置参数 减少同步复制延迟时间，一旦超过延迟时间的阈值，就不往master中写入数据

对于客户端，可以采用降级措施，将数据暂时写入本地缓存或磁盘，在一段时间后重新写入master来保证数据不丢失；也可以将数据写入kafka消息队列，隔一段时间去消费kafka中的数据。

##### 做过 redis 的集群吗？你们做集群的时候搭建了几台，都是 怎么搭建的？

Redis 的数据是存放在内存中的，不适合存储大数据，大数据存储一般公司常用 hadoop 中的 Hbase 或者 MogoDB。redis 主要用来处理高并发的，用我们的项目来说，电商项目如果并发 大的话，一台单独的 redis 是不能足够支持我们的并发，这就需要我们扩展多台设备协同合 作，即用到集群。 

Redis 搭建集群的方式有多种，例如：客户端分片、Twemproxy、Codis 等，但是 redis3.0 之 后就支持 redis-cluster 集群，这种方式采用的是无中心结构，每个节点保存数据和整个集群 的状态，每个节点都和其他所有节点连接。如果使用的话就用 redis-cluster 集群。集群这块 是公司运维搭建的，具体怎么搭建不是太了解。

我们项目中 redis 集群主要搭建了 6 台，3 主（为了保证 redis 的投票机制）3 从（高可用）， 每个主服务器都有一个从服务器，作为备份机。所有的节点都通过 PING-PONG 机制彼此互 相连接；客户端与 redis 集群连接，只需要连接集群中的任何一个节点即可；Redis-cluster 中内置了 16384 个哈希槽，Redis-cluster 把所有的物理节点映射到【0-16383】slot 上，负责 维护。

集群之间的复制：异步复制

集群最大节点数：16384

##### Redis 常见性能问题和解决方案

1、Master 最好不要写内存快照，如果 Master 写内存快照，save 命令调度 rdbSave 函数，会阻塞主线程的工作，当快照比较大时对性能影响是非常大的，会间断性 暂停服务

2、如果数据比较重要，某个 Slave 开启 AOF 备份数据，策略设置为每秒同步一 

3、为了主从复制的速度和连接的稳定性，Master 和 Slave 最好在同一个局域网 

4、尽量避免在压力很大的主库上增加从

5、主从复制不要用图状结构，用单向链表结构更为稳定，即：Master <- Slave1 <- Slave2 <- Slave3…这样的结构方便解决单点故障问题，实现 Slave 对 Master 的替换。如果 Master 挂了，可以立刻启用 Slave1 做 Master，其他不变。

##### 假如 Redis 里面有 1 亿个 key，其中有 10w 个 key 是以 某个固定的已知的前缀开头的，如果将它们全部找出来？

keys 指令可以扫出指定模式的 key 列表。

接着追问：如果这个 redis 正在给线上的业务提供服务

redis 的单线程的。keys 指令会导致线 程阻塞一段时间，线上服务会停顿，直到指令执行完毕，服务才能恢复，这个时 候可以使用 scan 指令，scan 指令可以无阻塞的提取出指定模式的 key 列表。但 是会有一定的重复概率，在客户端做一次去重就可以了，但是整体所花费的时间 会比直接用 keys 指令长。

##### 如果有大量的 key 需要设置同一时间过期，一般需要注意 什么？

如果大量的 key 过期时间设置的过于集中，到过期的那个时间点，redis 可能 会出现短暂的卡顿现象。一般需要在时间上加一个随机值，使得过期时间分散一 些。

##### 使用过redis做过异步队列吗？

答：一般使用 list 结构作为队列，rpush 生产消息，lpop 消费消息。当 lpop 没有 消息的时候，要适当 sleep 一会再重试。

![image-20210618135012212](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210618135012212.png)

##### 使用过 Redis 分布式锁么，它是什么回事？

先拿 setnx 来争抢锁，抢到之后，再用 expire 给锁加一个过期时间防止锁忘记了 释放

- expire和setnx不是原子操作，setnx的节点1突然挂掉，那么expire来不及执行就变成了无止尽的锁

解决办法：使用redis的set多参数方法，set(key,1,30,NX)

- del 误删，setnx获得锁之后，执行业务逻辑时间很长，expire已过期，那么节点2会拿到该锁，等节点1执行完业务逻辑之后，执行del操作，其实是删掉节点2的锁；

解决办法：把线程id加到锁的value当中去，拿到key的时候判断当前线程id是否为当前线程id；

新问题：判断线程id的逻辑和删除逻辑不在同一个原子操作中；

解决办法：使用lua脚本；把判断线程id和删除逻辑放在一个原子操作中；



## 发布订阅模式 

![image-20210707162025502](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210707162025502.png)

发布者发布消息到频道，订阅了频道的订阅者可以收到消息，订阅者可以订阅不同的频道。

一个server可以创建多个频道

一个订阅者可以订阅多个频道

发布者向一个频道发布 消息，则订阅该频道的订阅者都会 收到消息

最近订阅频道的订阅者，无法接受之前频道里的历史消息。没有消息积压功能

订阅者收到的消息模板：channel名称+newMessage

##### redis适合的场景

会话缓存

消息队列

消息通知

评论，排行榜，商品列表



------

1. Redis用过哪些数据类型，每种数据类型的使用场景
2. Redis缓存穿透、缓存雪崩和缓存击穿原因，以及解决方案
3. 如何使用Redis来实现分布式锁，redis分布式锁有什么缺陷？
4. Redis 持久化机制，有几种方式，优缺点是什么，怎么实现的，RDB和AOF的区别
5. Redis集群，高可用，原理。
6. Redis的数据淘汰策略
7. 为什么要用redis？为什么要用缓存，在哪些场景使用缓存
8. redis事务，了解吗，了解Redis事务的CAS操作吗
9. 如何解决 Redis 的并发竞争Key问题。
10. Redis为什么是单线程的，为什么单线程还这么快？
11. 如何保证缓存与数据库双写时的数据一致性?
12. redis和memcached有什么区别
13. JVM本地缓存，了解过吗
14. redis的list结构相关的操作。
15. redis2和redis3的区别，redis3内部通讯机制。
16. Redis的选举算法和流程是怎样的？
17. Reids的主从复制机制原理。
18. Redis的线程模型是什么？
19. Redis的使用要注意什么，讲讲持久化方式，内存设置，集群的应用和优劣势，淘汰策略等。
20. Redis缓存分片
21. redis的集群怎么同步的数据的？
22. 请思考一个方案，设计一个可以控制缓存总体大小的自动适应的本地缓存。
23. redis的哨兵模式，一个key值如何在redis集群中找到存储在哪里。
24. Redis，一个字符串类型的值能存储最大容量是多少？
25. MySQL里有2000w数据，redis中只存20w的数据，如何保证redis中的数据都是热点数据？
26. Redis和Redisson有什么关系？
27. Redis中的管道有什么用？
28. Redis事务相关的命令有哪几个？
29. Redis key的过期时间和永久有效分别怎么设置？
30. Redis回收使用的是什么算法？
31. 一个Redis实例最多能存放多少的keys？List、Set、Sorted Set他们最多能存放多少元素？
32. Redis—跳跃表，复杂度是多少？
33. Redis有哪些优缺点？为什么要用 Redis ？
34. 为什么要用Redis 而不用 map/guava 做缓存?
35. 如何用 Redis 统计独立用户访问量？
36. 如何选择合适的持久化方式
37. Redis持久化数据和缓存怎么做扩容？
38. Redis key的过期时间和永久有效分别怎么设置？
39. 我们知道通过expire来设置key 的过期时间，那么对过期的数据怎么处理呢?
40. Redis的过期键的删除策略
41. Redis的内存用完了会发生什么？
42. Redis如何做内存优化？
43. Redis事务的三个阶段
44. Redis事务相关命令
45. Redis事务保证原子性吗，支持回滚吗？
46. Redis事务支持隔离性吗？
47. Redis集群的主从复制模型是怎样的？
48. 生产环境中的 redis 是怎么部署的？
49. 说说Redis哈希槽的概念
50. Redis集群会有写操作丢失吗？为什么？
51. Redis集群最大节点个数是多少？
52. Redis集群如何选择数据库？
53. Redis是单线程的，如何提高多核CPU的利用率？
54. 为什么要做Redis分区？有什么缺点？
55. 你知道有哪些Redis分区实现方案？
56. 缓存的实现原理，设计缓存要注意什么
57. 如何解决 Redis 的并发竞争 Key 问题
58. 分布式Redis是前期做还是后期规模上来了再做好？为什么？
59. 什么是 RedLock？
60. Redis支持的Java客户端都有哪些？官方推荐用哪个？
61. 为什么Redis的操作是原子性的，怎么保证原子性
62. Redis常见性能问题和解决方案？
63. 一个字符串类型的值能存储最大容量是多少？
64. Redis如何做大量数据插入？
65. 假如Redis里面有1亿个key，其中有10w个key是以某个固定的已知的前缀开头的，如果将它们全部找出来？
66. 使用Redis做过异步队列吗，是如何实现的？
67. Redis如何实现延时队列？
68. Redis回收进程如何工作的？
69. 热点数据和冷数据是什么
70. 使用过Redis哪些命令？

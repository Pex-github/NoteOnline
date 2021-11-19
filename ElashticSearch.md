##### elasticsearch 的倒排索引是什么

相反于一篇文章包含了哪些词，它从词出发，记载了这个词在哪些文 档中出现过，由两部分组成——词典和倒排表

通过分词策略，形成了词和文章的映射关系表，这种词典+映射表 即为倒排索引

倒排索引的底层实现是基于：FST数据结构

该数据结构的特点：1、空间占用小。通过对词典中单词前缀和后缀的重复利用，压缩了存储空间； 2、查询速度快。O(len(str))的查询时间复杂度

##### elasticsearch 索引数据多了怎么办，如何调优，部署

索引数据的规划，应在前期做好规划，才能有效的避免突如其来的数据激增导致集群处理能力不足引发的线上客户 检索或者其他业务受到影响

**动态索引层面**

基于模板+时间+rollover api 滚动创建索引，举例：设计阶段定义：blog 索 引的模板格式为：blog_index_时间戳的形式，每天递增数据。

**存储层面**

冷热数据分离存储，热数据（比如最近 3 天或者一周的数据），其余为冷数据。 对于冷数据不会再写入新数据，可以考虑定期 force_merge 加 shrink 压缩操作， 节省存储空间和检索效率

**部署层面**

属于应急策略。 结合 ES 自身的支持动态扩展的特点，动态新增机器的方式可以缓解集群压力

##### elasticsearch 是如何实现 master 选举的

前置前提： 1、只有候选主节点（master：true）的节点才能成为主节点。 2、最小主节点数（min_master_nodes）的目的是防止脑裂

选举流程：

第一步：确认候选主节点数达标，elasticsearch.yml 设置的值 discovery.zen.minimum_master_nodes【最少投票通过数量】

第二步：比较：先判定是否具备 master 资格，具备候选主节点资格的优先返回； 若两节点都为候选主节点，则 id 小的值会主节点。注意这里的 id 为 string 类型

##### 详细描述一下 Elasticsearch 索引文档的过程

单文档写入流程

![image-20210618183546995](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210618183546995.png)

​	第一步：客户写集群某节点写入数据，发送请求。（如果没有指定路由/协调节点， 请求的节点扮演 路由节点的角色

​	第二步：节点 1 接受到请求后，使用文档_id 来确定文档属于分片 0。请求会被转 到另外的节点，假定节点 3。因此分片 0 的主分片分配到节点 3 上。 （借助路由算法获取，路由算法就是根据路由和文档 id 计算目标的分片 id 的 过程）
​	第三步：节点 3 在主分片上执行写操作，如果成功，则将请求并行转发到节点 1 和节点 2 的副本分片上，等待结果返回。所有的副本分片都报告成功，节点 3 将 向协调节点（节点 1）报告成功，节点 1 向请求客户端报告写入成功

##### 详细描述一下 Elasticsearch 搜索的过程？

分为两个阶段

query 阶段的目的：定位到位置，但不取

1、假设一个索引数据有 5 主+1 副本 共 10 分片，一次请求会命中（主或者副本 分片中）的一个。 

2、每个分片在本地进行查询，结果返回到本地有序的优先队列中。 

3、第 2）步骤的结果发送到协调节点，协调节点产生一个全局的排序列表。

fetch 阶段的目的：取数据。 路由节点获取所有文档，返回给客户端。

##### Elasticsearch 在部署时，对 Linux 的设置有哪些优化方法

1、关闭缓存 swap; 

2、堆内存设置为：Min（节点内存/2, 32GB）; 

3、设置最大文件句柄数； 

4、线程池+队列大小根据业务需要做调整； 

5、磁盘存储 raid 方式——存储有条件使用 RAID10，增加单节点性能以及避免单 节点存储故障

##### Lucene的内部结构

包含了两个过程，索引的创建和搜索。有三个要点可以展开：索引创建，索引，搜索

##### 客户端连接集群如何选择特定的节点执行请求

TransportClient 利用 transport 模块远程连接一个 elasticsearch 集群。它并 不加入到集群中，只是简单的获得一个或者多个初始化的 transport 地址，并以 轮 询 的方式与这些地址进行通信

##### 在并发情况下，Elasticsearch 如果保证读写一致

##### 如何监控 Elasticsearch 集群状态

通过 Kibana 监控 Elasticsearch。你可以实时查看你 的集群健康状态和性能，也可以分析过去的集群、索引和节点指标

##### 介绍下电商搜索的整体技术架构

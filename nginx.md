##### Nginx的模块与工作原理是什么?

##### Nginx 是什么？有什么作用？

 是一个高性能的[HTTP](https://baike.baidu.com/item/HTTP)和[反向代理](https://baike.baidu.com/item/反向代理/7793488)服务

反向代理以[代理服务器](https://baike.baidu.com/item/代理服务器/97996)来接受internet上的连接请求，然后将请求转发给内部网络上的服务器

```
	区别：	正向代理架设在客户机和目标主机之间，并且代理的客户端
		  反向代理架设在服务端。客户端不知道具体提供服务的服务端
```

支持http协议，可以做负载均衡

##### 说说Nginx的一些特性。

反向代理

负载均衡 

可用于重新编写URL，支持正则表达式

##### 请说一下Nginx如何处理HTTP请求。

![image-20210712201621720](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210712201621720.png)

##### 你知道，Nginx服务器上的Master和Worker进程分别是什么吗?

![image-20210712201646129](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210712201646129.png)

nginx常用命令，启动，重启，检查配置文件等

make && make install			安装

./nginx		启动

./nginx	-s	stop		停止

./nginx	-s	reload		重启

cd /usr/local/nginx/conf/nginx.conf		配置文件

![image-20210712202111383](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210712202111383.png)

##### Nginx 多进程模型是如何实现高并发的？

用nginx做集群，一个master对应多个worker	一个worker最大的连接数1024

作为静态访问	或者	反向代理	两个的最大并发数的计算公式不一样

注意，nginx需要keepalived插件



##### 请列举Nginx服务器的最佳用途

![image-20210712203757272](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210712203757272.png)

动静分离，把静态文件独立成单独的域名，放在独立的服务器上，也是目前主流推崇的方案



```
file:///E:/Java20/Nginx/%E7%AC%94%E8%AE%B0%E8%B5%84%E6%96%99/nginx%E8%AF%BE%E5%A0%82%E7%AC%94%E8%AE%B0.pdf
```


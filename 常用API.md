#### RestTemplate



## Redis

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

```java
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.ValueOperations;
```

#### ValueOperations

连接池自动管理，提供了一个高度封装的“RedisTemplate”类

针对jedis客户端中大量api进行了归类封装,将同一类型操作封装为operation接口 

- ValueOperations：简单K-V操作 
- SetOperations：set类型数据操作 
- ZSetOperations：zset类型数据操作 
- HashOperations：针对map类型的数据操作 
- ListOperations：针对list类型的数据操作

所以想要使用ValueOperations，就必须注入redisTemplate

```java
@Service
public class TokenRedisServiceImpl implements TokenRedisService {

    private static final String PRE_KEY = "token:";

    @Resource(name = "redisTemplate")
    private ValueOperations<String, String> vOps;

    @Resource(name = "redisTemplate")
    private RedisTemplate<String, String> redisTemplate;
    
    @Override
    public void put(String phoneNum, String token, Integer expHours) {
        vOps.set(PRE_KEY + phoneNum, token, expHours, TimeUnit.HOURS);
    }
    @Override
    public String get(String phoneNum) {
        return vOps.get(PRE_KEY + phoneNum);
    }
    @Override//是否到期
    public Boolean expire(String phoneNum, Integer expHours) {
        return redisTemplate.expire(phoneNum, expHours, TimeUnit.HOURS);
    }
    @Override
    public void delete(String phoneNum) {
        redisTemplate.delete(PRE_KEY + phoneNum);
    }
}
```



![valuesOperations](C:\Users\PEX\Pictures\valuesOperations.png)

![image-20210712223226693](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20210712223226693.png)



## springframework.util

#### ObjectUtils

https://docs.spring.io/spring-framework/docs/5.2.2.RELEASE/javadoc-api/org/springframework/util/ObjectUtils.html

nullSafeEquals方法可以完全避免空指针问题，null和null比较的结果为true。



## javax.servlet.http

#### HttpServletRequest

**字段**	都是static final String

BASIC_AUTH，FORM_AUTH，CLIENT_CERT_AUTH，DIGEST_AUTH

基础认证，表单认证，客户端认证，摘要认证

**常用方法**

| 获取客户端信息方法 | 描述                             |
| ------------------ | -------------------------------- |
| getRequestURL()    | 返回客户端发出请求时的完整URL    |
| getRequestURI()    | 返回请求行中的参数部分           |
| getQueryString ()  | 方法返回请求行中的参数部分       |
| getRemoteHost()    | 返回发出请求的客户机的完整主机名 |
| getRemoteAddr()    | 返回发出请求的客户机的IP地址     |
| getPathInfo()      | 返回请求URL中的额外路径信息      |
| getRemotePort()    | 返回客户机所使用的网络端口号     |
| getLocalAddr()     | 返回WEB服务器的IP地址            |
| getLocalName()     | 返回WEB服务器的主机名            |



| 获取请求头 信息                         | 描述                                                         |
| --------------------------------------- | ------------------------------------------------------------ |
| getHeader(string name)                  | 获取请求头中name对应的值                                     |
| getHeaderNames()                        | 获取请求中的所有的name，返回一个Enumeration<String>          |
| getHeaders(String name)                 | 获取请求头中name对应的所有值，返回一个Enumeration<String>    |
| **获取客户机请求参数**                  | **描述**                                                     |
| getParameter(String name)               | 根据name获取请求参数的值                                     |
| getParameterValues(String name)         | 根据name获取请求的参数列表                                   |
| getParameterMap()                       | 返回的是一个Map类型的值                                      |
| String getContextPath()                 | 该方法用于获取请求 URL 中属于 Web 应用程序的路径，这个路径以 / 开头 |
| Cookie[ ] getCookie                     | 返回一个数组，其中包含客户端与此请求一起发送的所有Cookie对象。 |
| String getMethod()                      | 返回发出此请求所用的HTTP方法的名称，例如GET、POST或PUT。     |
| HttpSession getSession()                | 返回与此请求关联的当前会话，如果该请求没有会话，则创建一个   |
| String  getServletPath()                | 返回该请求的URL中调用servlet的部分                           |
| void setAttribute(String name,Object o) | 在此请求中存储一个属性                                       |
| void setCharacterEncoding(String env)   | 覆盖此请求正文中的使用的字符编码                             |

#### HttpServletResponse

字段：各种static状态码

封装了向客户端发送数据、发送响应头，发送响应状态码的方法

| 发送数据给客户端方法                       | 描述                                   |
| ------------------------------------------ | -------------------------------------- |
| getOutputStream()                          | 发送字节实体内容                       |
| getWriter()                                |                                        |
| **发送响应头方法**                         | **描述**                               |
| void addDateHeader(String name, long date) | 添加指定名称的响应头和日期值           |
| void addHeader(String name, String value)  | 添加指定名称的响应头和值               |
| void addIntHeader(String name, int value)  | 添加指定名称的响应头和int值            |
| boolean containsHeader(String name)        | 返回指定的响应头是否存在               |
| void setHeader(String name, String value)  | 使用指定名称和值设置响应头的名称和内容 |
| void setIntHeader(String name, int value)  | 指定 int 类型的值到 name 标头          |
| void setDateHeader(String name, long date) | 使用指定名称和值设置响应头的名称和内容 |
| **发送响应状态码**                         | **描述**                               |
| void setStatus(int sc)                     | 设置响应的状态码                       |
| void addCookie(Cookie cookie)              | 将指定cookie添加到响应中               |
|                                            |                                        |



## slf4j

 Simple Logging Facade for Java





## Guava

#### String操作

1.使用com.google.common.base.Strings类的isNullOrEmpty(input)方法判断字符串是否为空

```java
Strings.isNullOrEmpty(input)	返回布尔型
```

2.获得两个字符串相同的前缀或者后缀

```java
 1        //Strings.commonPrefix(a,b) demo
 2         String a = "com.jd.coo.Hello";
 3         String b = "com.jd.coo.Hi";
 4         String ourCommonPrefix = Strings.commonPrefix(a,b);
 5         System.out.println("a,b common prefix is " + ourCommonPrefix);
 6 
 7         //Strings.commonSuffix(a,b) demo
 8         String c = "com.google.Hello";
 9         String d = "com.jd.Hello";
10         String ourSuffix = Strings.commonSuffix(c,d);
11         System.out.println("c,d common suffix is " + ourSuffix);
```

3.Strings的padStart和padEnd方法来补全字符串

```java
1         int minLength = 4;
2         String padEndResult = Strings.padEnd("123", minLength, '0');
3         System.out.println("padEndResult is " + padEndResult);
4 
5         String padStartResult = Strings.padStart("1", 2, '0');
6         System.out.println("padStartResult is " + padStartResult);
```

4.使用Splitter类来拆分字符串

根据正则表达式来拆分字符串，可以去掉拆分结果中的空串，可以对拆分后的字串做trim操作，还可以做二次拆分。

```java
1         String toSplitString = "a=b;c=d,e=f";
2         Map<String,String> kvs = Splitter.onPattern("[,;]{1,}").withKeyValueSeparator('=').split(toSplitString);
3         for (Map.Entry<String,String> entry : kvs.entrySet()) {
4             System.out.println(String.format("%s=%s", entry.getKey(),entry.getValue()));
5         }
```

5.合并字符串，guava为我们提供了Joiner类来做字符串的合并

```java
1         String joinResult = Joiner.on(" ").join(new String[]{"hello","world"});
2         System.out.println(joinResult);
//-----------
1         Map<String,String> map = new HashMap<String,String>();
2         map.put("a", "b");
3         map.put("c", "d");
4         String mapJoinResult = Joiner.on(",").withKeyValueSeparator("=").join(map);
```

使用withKeyValueSeparator方法可以对map做合并。合并的结果是:`a=b,c=d`

- 

## RedisTemplate

opsForValue()

​	get(key)

​	下面还包含很多方法

## Jackson

#### ObjectMapping

ObjectMapper类————如何序列化java对象为json以及json字符串反序列化为java对象

- writeValue()       可以序列化任何java对象为json字符串。
- readValue()        json转java 对象

# Java

## lang

#### String

```format()```		使用指定的格式字符串和参数返回格式化的字符串。



## Util

#### Optional

- 可能包含或不包含非空值的容器对象

- 提供依赖于存在或不存在包含值的其他方法，例如[`orElse()`](../../java/util/Optional.html#orElse-T-)  （如果值不存在则返回默认值）和[`ifPresent()`](../../java/util/Optional.html#ifPresent-java.util.function.Consumer-)  （如果值存在[则](../../java/util/Optional.html#ifPresent-java.util.function.Consumer-)执行代码块）。

- ofNullable（T value）返回一个描述指定值的Optional ，如果非空，否则返回一个空的Optional 

  


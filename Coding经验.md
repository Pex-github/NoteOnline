#### stream()

###### .map()方法

函数式编程，对stream的任何操作都不会修改背后的数据源。方法理解：迭代器，单向，不可重复，

```java
@Data
public class TestStream {
    private String name;
    private int age;
    public TestStream(){}
    public TestStream(String name,int age){
        this.name = name;
        this.age = age;
    }
    public static void main(String[] args) {
        ArrayList<TestStream> list = new ArrayList<>();

        list.add(new TestStream("zhangsan",18));
        list.add(new TestStream("lisi",30));
        list.add(new TestStream("wangwu",40));

        list.stream()
            .forEach(person->System.out.println(person));
        System.out.println("---------------");
        list.stream()
            .map(person->person.getName())
            .forEach(person-> System.out.println(person));
        System.out.println("---------------");
        list.stream()
            .forEach(person-> System.out.println(person.getAge()));
        System.out.println("---------------");
        System.out.println(list.stream().count());
    }
}
//输出
TestStream(name=zhangsan, age=18)
TestStream(name=lisi, age=30)
TestStream(name=wangwu, age=40)
---------------
zhangsan
lisi
wangwu
---------------
18
30
40
---------------
3
//stream的其他功能：过滤，排序，取值等功能
```

###### .foreach()方法

迭代

```java
int[] arr = {1,3,4,5,6}
Arrays.stream(arr)
    .forEach(n-> System.out.println(n));
```

###### .collect()

```java
//将对象为wmMateial列表通过流的方式展开，再给每个对象设置属性，再重新编程列表
List<WmMaterial> datas = wmMaterialMapper.findListByUidAndStatus(dto, uid);
datas = datas.stream().map((item)->{
    item.setUrl(fileServerUrl+item.getUrl());
    return item;
}).collect(Collectors.toList());
```


#### <T extends Comparable<? super T>>

1. extends后面跟的类型如<任意字符extends类/接口>表示泛型的上限
2. 同样的super表示泛型的下限
3. <T extends Comparable<? super T>>这里来分析T表示任意字符名extends对泛型上限进行了限制即T必须是Comparable<? super T>的子类然后< ? super T>表示Comparable<>中的类型下限为T
4. 必须实现Comparable接口 中的compareTo()方法
5. 这种语法中implements和extends统一写成extends
6. 也可以写：<T extends Comparable<T>>

例子：

```java
importjavautilGregorianCalendar;
class Demo<T extends Comparable<? super T>{}
public class Test1{
    public static void main(String[]args){
        Demo<GregorianCalendar>p=null;//编译正确
    }
}
//可以理解为<GregorianCalendar extends Comparable<Calendar>>
//<子类 extends Comparable<父类>>	同时子类实现了Comparable接口
```

#### 枚举类

```java
@Getter
public enum CommonTableEnum {
    AD_CHANNEL("*",true,true,true,true),
    AD_SENSITIVE("*",true,true,true,true),
    // APP用户端
    AP_ARTICLE("*",true,false,false,false),
    AP_ARTICLE_CONFIG("*",true,false,true,false),
    AP_USER("*",true,false,true,false),
    CL_NEWS("*",true,false,true,false),
    WM_NEWS("*",true,false,true,false);

    String filed;
    boolean list;//开启列表权限？
    boolean add;//开启增加权限？
    boolean update;//开启修改权限？
    boolean delete;//开启删除权限？

    CommonTableEnum(String filed,boolean list,boolean add,boolean update,boolean delete){
        this.filed = filed;
        this.list = list;
        this.add = add;
        this.update = update;
        this.delete = delete;
    }
}
```

枚举类可以实现接口，来获得通用接口里的方法

```java
//账户状态码
@Getter
@AllArgsConstructor
public enum AccountStatusCode implements CodeEnum {
    /**
     * 状态（20-30）
     * */
    ADD(1,"添加"),
    UPDATE(2,"修改");
    
    private final int code;
    private final String value;
}
```

通用枚举接口

```java
public interface CodeEnum {

    /**
     * 返回枚举体的code值
     *
     * @return code值
     */
    int getCode();

    /**
     * 返回枚举体的code值
     *
     * @return 字符串格式的code值
     */
    default String getCodeAsString() {
        return "" + getCode();
    }
}
//接口中默认访问修饰符 是public
//public修饰的方法不可有具体实现
//但default修饰的方法可以"继承"
//若实现的多个接口，且接口中有相同的default方法，就必须重写default方法
//当父类和接口中有相同方法名的方法，子类测试选择的方法是父类而不是接口中的default
```

具体代码使用

```java
AccountStatusCode.CITY_CODE_EMPTY.getCode();
```



#### String常用方法

substring	分割字符串

lastIndexOf	获取最后一个字符的位置

matches	检查是否包含提供的字符串

getBytes("UTF-8")

#### Pattern工具类

```java
import java.util.regex.Pattern;

//校验电话号码
if (!Pattern.compile(AccountStatusCode.PHONE_NUMBER_VERIFICATION.getValue()).matcher(phoneNum).matches()) {
                return ResponseResult.fail(AccountStatusCode.PHONE_NUM_ERROR.getCode(), AccountStatusCode.PHONE_NUM_ERROR.getValue(), phoneNum);
            }

枚举类
PHONE_NUMBER_VERIFICATION(2027,"^((13[0-9])|(14[56789])|(15[0-9])|(16[124567])|(17[0-8])|(18[0-9])|(19[0-9])|(92[0-9])|(98[0-9]))\\d{8}$"),
```

#### Integer常用方法

```java

public static String toHexString(byte b[]) {
    StringBuffer hexString = new StringBuffer();
    for (int i = 0; i < b.length; i++) {
        String plainText = Integer.toHexString(0xff & b[i]);
        if (plainText.length() < 2) {
            plainText = "0" + plainText;
        }
        hexString.append(plainText);
    }
    return hexString.toString();
}
//		Integer.toHexString(0xff & b[i]);
```

# 日志

在需要产生日志的类中

```java
private static Logger log = LoggerFactory.getLogger(当前类.class);
log.info();

```

```java
@slf4j
log.error("jingao");
    
```



#### 参数校验

```java
if(null == idType){
	log.error();
    return ;
}
if (ObjectUtils.nullSafeEquals(idType, IdentityEnum.DRIVER.getCode())) {
	//防止空指针异常，两个都为空则为true，相等也为true，一个为空则为false
}
if(StringUtils.isEmpty(strPhone)){
    request.getInfoList().get(i).setPhone(request.getInfoList().get(i).getEncrypt());
}

```



#### Map

修改Map中的每个entry中的值

```java
for (Map.Entry<String, Object> entry : template.getTemplateMap().entrySet()) {
    content = StringUtils.replace(content, "${" + entry.getKey() + "}", "" + entry.getValue());
}
```



#### JSON

###### JSONObject

只是一种数据结构，可以理解为JSON格式的数据结构（[key-value](https://www.baidu.com/s?wd=key-value&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao) 结构），可以使用put方法给json对象添加元素。JSONObject可以很方便的转换成字符串，也可以很方便的把其他对象转换成JSONObject对象。

```java
//原生生成json数据格式
JSONObject zhangsan = new JSONObject();
try {
	//添加
	zhangsan.put("name", "张三");
    zhangsan.put("age", 18.4);
    zhangsan.put("birthday", "1900-20-03");
    zhangsan.put("majar", new String[] {"哈哈","嘿嘿"});
    zhangsan.put("null", null);
    zhangsan.put("house", false);
    System.out.println(zhangsan.toString());
} catch (JSONException e) {
    e.printStackTrace();
}
————————————————
//hashMap数据结构生成
HashMap<String, Object> zhangsan = new HashMap<>();
        
        zhangsan.put("name", "张三");
        zhangsan.put("age", 18.4);
        zhangsan.put("birthday", "1900-20-03");
        zhangsan.put("majar", new String[] {"哈哈","嘿嘿"});
        zhangsan.put("null", null);
        zhangsan.put("house", false);
        System.out.println(new JSONObject(zhangsan).toString());
————————————————
//通过实体生成 
Student student = new Student();
        student.setId(1);
        student.setAge("20");
        student.setName("张三");
        //生成json格式
        System.out.println(JSON.toJSON(student));
        //对象转成string
        String stuString = JSONObject.toJSONString(student);
————————————————
//JSON字符串转换成JSON对象
String studentString = "{\"id\":1,\"age\":2,\"name\":\"zhang\"}";
JSONObject jsonObject1 = JSONObject.parseObject(stuString);
System.out.println(jsonObject1);
————————————————
//list对象转listJson
ArrayList<Student> studentLsit = new ArrayList<>();
        Student student1 = new Student();
        student1.setId(1);
        student1.setAge("20");
        student1.setName("asdasdasd");
 
        studentLsit.add(student1);
 
        Student student2 = new Student();
        student2.setId(2);
        student2.setAge("20");
        student2.setName("aaaa:;aaa");
 
        studentLsit.add(student2);
 
        //list转json字符串
        String string = JSON.toJSON(studentLsit).toString();
        System.out.println(string);
 
        //json字符串转listJson格式
        JSONArray jsonArray = JSONObject.parseArray(string);
原文链接：https://blog.csdn.net/u012448904/article/details/84292821
```



###### 使用场景

```
前端发送json格式字符串，后端解析为一个JavaBean格式
存储方便,Java对象转换为一个String对象存储在一个字段中
对于一些字段个数无法确定的对象，会将这个对象定义成一个JSONData进行请求
```

###### Fastjson

【com.alibaba.fastjson.*】

```java
转换为jsonString
1.将基本类型和对象转换为json字符串
toJSONString(Object);String
2.将基本类型和对象 转换为json格式字符串，并以UTF-8的Byte[]数组形式返回
toJSONBytes(Object);byte[]
3.将基本类型和对象转为json字符串，并将其保存在write或OutputStream中返回
writeJSONString(OutputStream,Object)
writeJSONString(Write,Object)

转换为JavaBean
1.将JSONString转换为T类型的对象
parseObject(String,Class<T>)
2.将json类型String对应的bytes[]数组转换成T类型的对象
parseObject(byte[]，Class,…)
3.将jsonString反序列化为泛型类型的JavaBean
parseObject(String text, TypeReference type, Feature… features)
4.将InputStream反序列化为泛型类型的JavaBean
parseObject(inputStream, Type, Feature… features)
5.将jsonString反序列化为JsonObject、JsonArray
parseArray(String)
parseObject(String)
```

```java
简单使用
//用法一 ：jsonString 转换JSONObject
JSONObject jsonObj = JSON.parseObject(jsonStr);
//用法二 ：JSONString转换成bean/Map
Model model = JSON.parseObject(jsonStr, Model.class);
//用法三 ：JSONString转换自定义泛型
Type type = new TypeReference<List<Model>>() {}.getType(); 
List<Model> list = JSON.parseObject(jsonStr, type);
————————————————
//用法一 : 对象转换成jsonString
Model model = ...; 
String jsonStr = JSON.toJSONString(model);
//用法二 : 对象转换成jsonString格式的byte数组
byte[] jsonBytes = JSON.toJSONBytes(model);
//用法三 : 对象转换成jsonString并将jsonString保存到OutputStream 或Writer
OutputStream os;
JSON.writeJSONString(os, model);
Writer writer = ...;
JSON.writeJSONString(writer, model);
原文链接：https://blog.csdn.net/weixin_41251135/article/details/110231280
```

###### 数据结构

- 1 []中括号代表的是一个数组；
- 2 {}大括号代表的是一个对象
- 3 双引号“”表示的是属性值
- 4 冒号：代表的是前后之间的关系，冒号前面是属性的名称，后面是属性的值，这个值可以是基本数据类型，也可以是引用数据类型。

```javascript
Demo: JSON 表示一个数字
2.90

Demo: JSON 表示一个字符串
"Hello world"

Demo: JSON 表示一个对象
{
　　"name":"smith".
　　"age":30,
　　"sex":"男"
}

Demo: JSON对象的属性可以是对象
{
　　"name":"smith".
　　"age":28,
　　"sex":"男"
　　"school":{
　　"sname":"南京大学".
　　"address":"南京市鼓楼区汉口路22号"
}
}
Demo: JSON 格式表示数组
保存名字的数组: ["张三","李四","王五"]
保存雇员的信息: ["smith",1001,"clerck",7788,2000.00,200.0]
数组里面是对象：
[
　　{"name":"smith","empno":1001,"job":"clerck","sal":9000.00,"comm":5000.00},
　　{"name":"smith","empno":1001,"job":"clerck","sal":9000.00,"comm":5000.00},
　　{"name":"smith","empno":1001,"job":"clerck","sal":9000.00,"comm":5000.00},
]
二维数组
[
　　["Java 开发",3,["smith","张三","李四"]],
　　["Web 开发",3["Allen","王五","赵六"]]
]

```

#### SpringBoot测试

生成的SpringBoot测试代码

```java
@RequestController
public class TestController{
    @GetMapping("/test")
    public String test(){
        return "test success"
    }
}
```


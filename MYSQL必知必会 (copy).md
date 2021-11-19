MYSQL必知必会

##### 目录

[TOC]



##### 第三章	使用MySQL	show	use

mysql -u root -p 

SHOW DATABASE;

USE test_database;

SHOW TABLES;

SHOW COLUMNS FROM test_database;

SHOW STATUS;　　 //显示服务器状态信息

SHOW CREATE TABLE products;

SHOW CREATE DATABASE test_database;

SHOW ERRORS;

SHOW WARNINGS:
SHOW GRANTS;//显示授予用户的安全权限

HELP SHOW; //显示允许的show语句

##### 第四章	检索数据	limit

SELECT prod_name FROM products;

SELECT prod_id,prod_name,prod_price FROM products;

SELECT * FROM products;

SELECT vend_id FROM products;

SELECT DISTINCT vend_id FROM products; 			//输出vend_id数值不同的行

SELECT prod_name FROM products LIMIT 5;		//只输出前5行

SELECT prod_name FROM products LIMIT 5,5;		//从第五行开始，输出5行

SELECT products.prod_name FROM products;		//完全限定名

SELECT products.prod_name FROM test_database.products;

##### 第五章	排序数据	order by		desc：降序

SELECT prod_name FROM products ORDER BY prod_name;		//以字母表的顺序排序

mysql> SELECT prod_id,prod_price,prod_name
    	  -> FROM productsprod_price
          -> ORDER BY prod_price,prod_name;		//优先默认升序排序prod_price，再以name排序

mysql> SELECT prod_id, prod_price, prod_name
          -> FROM products
          -> ORDER BY prod_price DESC;			//以降序的顺序排序prod_price

mysql> SELECT prod_id, prod_price, prod_name
  	    -> FROM products
          -> ORDER BY  prod_price DESC,prod_name;		//以降序排序优先排序prod_price，再以升序排列name

mysql> SELECT prod_price
          -> FROM products
          -> ORDER BY prod_price DESC
          -> LIMIT 1;				//输出price表中最贵的价格

##### 第六章	过滤数据	where 	<>		between and		is null



mysql> SELECT prod_name,prod_price
 	     -> FROM products
	      -> WHERE prod_price = 2.50;			

mysql> SELECT prod_name,prod_price
   	   -> FROM products
	      -> WHERE prod_name = "fuses";		

mysql> SELECT prod_name,prod_price
	      -> FROM products
 	     -> WHERE prod_price < 10;

mysql> SELECT prod_name,prod_price
  	    -> FROM products
	      -> WHERE prod_price < 10
	      -> ORDER BY prod_price
  	    -> LIMIT 5;
mysql> SELECT prod_name,prod_price
	      -> FROM products
	      -> WHERE prod_price <= 10;

mysql> SELECT vend_id,prod_name
	       -> FROM products
	      -> WHERE vend_id <> 1003;		//不等于   

mysql> SELECT prod_name,prod_price
	      -> FROM products
	      -> WHERE prod_price BETWEEN 5 AND 10;		//5到10闭区间

mysql> SELECT cust_id
 	     -> FROM customers
  	    -> WHERE cust_email IS NULL;		//空值检查

##### 第七章	数据过滤	in 		not in			(  or  ) and d

//输出编号为1002或1003，且价格大于10的的行

mysql> SELECT vend_id,prod_name,prod_price
  	    -> FROM products
 	     -> WHERE (vend_id = 1003 OR vend_id = 1002) AND prod_price >=10; 



mysql> SELECT prod_name, prod_price
      	-> FROM products
	      -> WHERE vend_id = 1003 OR vend_id = 1002;



//输出id是1002，1003的行，并排序

mysql> select vend_id,prod_name,prod_price
	      -> from products
	      -> where vend_id in (1002,1003)
	      -> order by prod_name;



//输出id不是1002，1003的行，并排序

mysql> select vend_id,prod_name,prod_price
    -> from products
    -> where vend_id not in (1002,1003)
    -> order by prod_name;

##### 第八章	用通配符进行过滤		like		%  		_

//输出name以jet开头的行，%表示 多个任字符

mysql> select prod_id,prod_name
 	     -> from products
 	     -> where prod_name like 'jet%';

mysql> select prod_id,prod_name
    	  -> from products
	      -> where prod_name like '%anvil%';

mysql> select prod_name
	      -> from products
	      -> where prod_name like 's%e';



//  _  匹配单个字符

mysql> select prod_id,prod_name
 	     -> from products
  	    -> where prod_name like '_ ton anvil';

mysql> select prod_id,prod_name
	      -> from products
	      -> where prod_name like '% ton anvil';

不要过度使用

##### 第九章	正则表达式进行搜索	// 	^	 . 	[ ]	[:digit:]	 s?

- //检索字段中包含1000的所有行

mysql> select prod_name
  	    -> from products
    	  -> where prod_name regexp '1000'
    	  -> order by prod_name;

- //  . 匹配任意一个字符

mysql> select prod_name
  	   -> from products
	      -> where prod_name regexp '.000'
	      -> order by prod_name;

- // 	| 	匹配or

mysql> select prod_name
    -> from products
    -> where prod_name regexp '1000|2000';

- //[123] Ton	是[1|2|3] Ton的缩写	[ ]是另一种形式的or语句
- //匹配除了这些字符串以外的任何东西 

![image-20201017122049280](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20201017122049280.png)

mysql> select prod_name
    -> from products
    -> where prod_name regexp '[123] Ton'
    -> order by prod_name;

![image-20201017121226588](C:\Users\PEX\AppData\Roaming\Typora\typora-user-images\image-20201017121226588.png)

- //输出和上面不一样，匹配'1' 或  ‘2’  或  ‘3 Ton’

mysql> select prod_name
	      -> from products
 	     -> where prod_name regexp '1|2|3 Ton'
	      -> order by prod_name;

//   - 定义一个范围

mysql> select prod_name
	      -> from products
 	     -> where prod_name regexp '[0-5] Ton'
	      -> order by prod_name;

//查找字段中有 . 的行

```sql
mysql> select vend_name
    -> from vendors
    -> where vend_name regexp '\\.'
    -> order by vend_name;
```

```mysql
//? 使s可选		\\(	匹配（
mysql> select prod_name
    -> from products
    -> where prod_name regexp '\\([0-9] sticks?\\)'
    -> order by prod_name;
+----------------+
| prod_name      |
+----------------+
| TNT (1 stick)  |
| TNT (5 sticks) |
+----------------+
2 rows in set (0.00 sec)
```

```mysql
//[:digit:] 表示任意一个数字，[[:digit:]]表示一个数字集合，{4}表示前面的字符出现4次 
//[:alpha:] 表示任意一个字符[a-z]&[A-Z]
mysql> select prod_name
    -> from products
    -> where prod_name regexp '[[:digit:]]{4}'
    -> order by prod_name;
+--------------+
| prod_name    |
+--------------+
| JetPack 1000 |
| JetPack 2000 |
+--------------+
2 rows in set (0.00 sec)
```

```mysql
//用另一种方式编写上面的例子
mysql> select prod_name
    -> from products
    -> where prod_name regexp '[0-9][0-9][0-9][0-9]'
    -> order by prod_name;
+--------------+
| prod_name    |
+--------------+
| JetPack 1000 |
| JetPack 2000 |
+--------------+
2 rows in set (0.00 sec)
```

```mysql
//定位符	^ 表示文本开始	$ 表示文本末尾	
mysql> select prod_name
    -> from products
    -> where prod_name regexp '^[0-9\\.]'
    -> order by prod_name;
+--------------+
| prod_name    |
+--------------+
| .5 ton anvil |
| 1 ton anvil  |
| 2 ton anvil  |
+--------------+
3 rows in set (0.00 sec)
```

##### 第十章	创建计算字段		Concat()		Trim()		as	+ - * / 

```mysql
//Concat()	拼接字符串
mysql> select Concat(vend_name,'(',vend_country,')') concat
    -> from vendors
    -> order by vend_name;
+------------------------+
| concat                 |
+------------------------+
| ACME(USA)              |
| Anvils R Us(USA)       |
| Furball Inc.(USA)      |
| Jet Set(England)       |
| Jouets Et Ours(France) |
| LT Supplies(USA)       |
+------------------------+
6 rows in set (0.00 sec)
```

```mysql
//RTrim()	去掉右边所有的空格对各列进行整理	LTrim() 左边空格
mysql> select Concat(RTrim(vend_name),' (',RTrim(vend_country) , ')')
    -> from vendors
    -> order by vend_name;
+---------------------------------------------------------+
| Concat(RTrim(vend_name),' (',RTrim(vend_country) , ')') |
+---------------------------------------------------------+
| ACME (USA)                                              |
| Anvils R Us (USA)                                       |
| Furball Inc. (USA)                                      |
| Jet Set (England)                                       |
| Jouets Et Ours (France)                                 |
| LT Supplies (USA)                                       |
+---------------------------------------------------------+
6 rows in set (0.00 sec)
```

```mysql
//as	使用别名
mysql> select Concat(vend_name,'(',vend_country,')') as vend_title
    -> from vendors
    -> order by vend_name;
+------------------------+
| vend_title             |
+------------------------+
| ACME(USA)              |
| Anvils R Us(USA)       |
| Furball Inc.(USA)      |
| Jet Set(England)       |
| Jouets Et Ours(France) |
| LT Supplies(USA)       |
+------------------------+
6 rows in set (0.00 sec)
```

```mysql
//执行算数运算	+ - * /
mysql> select prod_id,quantity,
			  item_price,
			  quantity*item_price as expanded_price
    -> from orderitems
    -> where order_num = 20005;
+---------+----------+------------+----------------+
| prod_id | quantity | item_price | expanded_price |
+---------+----------+------------+----------------+
| ANV01   |       10 |       5.99 |          59.90 |
| ANV02   |        3 |       9.99 |          29.97 |
| TNT2    |        5 |      10.00 |          50.00 |
| FB      |        1 |      10.00 |          10.00 |
+---------+----------+------------+----------------+
4 rows in set (0.00 sec)
```

```mysql
//select提供了测试和实验函数与计算的一个好办法

mysql> select 3*4;
+-----+
| 3*4 |
+-----+
|  12 |
+-----+
1 row in set (0.00 sec)

mysql> select Trim('aaa    ') as a;
+------+
| a    |
+------+
| aaa  |
+------+
1 row in set (0.00 sec)

mysql> select NOW();
+---------------------+
| NOW()               |
+---------------------+
| 2020-10-17 17:00:43 |
+---------------------+
1 row in set (0.00 sec)
```

##### 第十一章	使用数据处理函数

```mysql
mysql> select vend_name,Upper(vend_name)as vend_name_upcase
    -> from vendors
    -> order by vend_name;
+----------------+------------------+
| vend_name      | vend_name_upcase |
+----------------+------------------+
| ACME           | ACME             |
| Anvils R Us    | ANVILS R US      |
| Furball Inc.   | FURBALL INC.     |
| Jet Set        | JET SET          |
| Jouets Et Ours | JOUETS ET OURS   |
| LT Supplies    | LT SUPPLIES      |
+----------------+------------------+
6 rows in set (0.09 sec)
```

###### 11.1	文本处理函数函数

| 函数        | 说明                    |
| :---------- | ----------------------- |
| Left()      | 返回串左边的字符        |
| Length()    | 串的长度                |
| Locate()    | 找出串的第一个子串      |
| Lower()     | 转换小写                |
| LTrim()     | 去掉串左边空格          |
| Right()     | 串右边的字符            |
| RTrim()     | 去掉串右边的空格        |
| Soundex()   | 返回soundex值（拼写像） |
| subString() | 返回子串的字符          |
| Upper()     | 转换大写                |



```mysql
mysql> select cust_name,cust_contact
    -> from customers
    -> where Soundex(cust_contact) = Soundex('Y Lie');
+-------------+--------------+
| cust_name   | cust_contact |
+-------------+--------------+
| Coyote Inc. | Y Lee        |
+-------------+--------------+
1 row in set (0.00 sec)
```

###### 11.2	时间处理函数

| 时间处理函数                | 说明                   |
| --------------------------- | ---------------------- |
| AddDate()                   | 增加一个日期           |
| AddTime()                   | 增加一个时间           |
| Date()                      | 返回日期时间的日期部分 |
| CurDate()   CurTime()       | 当前日期   时间        |
| DayOfWeek()                 | 星期几                 |
| DateAdd()   DateDiff()      | 日期之和，日期只差     |
| Date_Format()               | 格式化显示             |
| Hour() Minute()   Secound() |                        |
| Time()  Now()   Year()      |                        |

```mysql
mysql> select DayOfWeek(order_date) as weekdatee
    -> from orders
    -> where Date(order_date)= '2005-09-01';
+-----------+
| weekdatee |
+-----------+
|         5 |
+-----------+
1 row in set (0.00 sec)
```

```mysql
mysql> select cust_id,order_num
    -> from orders
    -> where Date(order_date)= '2005-09-01';
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
+---------+-----------+
1 row in set (0.00 sec)
```



###### 11.3	数值处理函数

| 函数                            | 说明                             |
| ------------------------------- | -------------------------------- |
| Rand()                          | 随机数                           |
| Mod()                           | 余数                             |
| Abs()                           | 绝对值                           |
| Exp()                           | 指数值                           |
| sin()  cos() tan()  sqrt() pi() | 正弦，余弦，正切，平方根，圆周率 |

##### 第十二章	汇总数据

12.1	聚集函数

| 函数    | 说明     |
| ------- | -------- |
| AVG()   | 列平均值 |
| COUNT() | 列行数   |
| MAX()   | 列最大值 |
| MIN()   | 列最小   |
| SUM()   | 列值之和 |

```mysql
mysql> select AVG(prod_price) as avg_price
    -> from products
    -> where vend_id = 1003;
+-----------+
| avg_price |
+-----------+
| 13.212857 |
+-----------+
1 row in set (0.00 sec)

mysql> select COUNT(*) as num_cust
    -> from customers;
+----------+
| num_cust |
+----------+
|        5 |
+----------+
1 row in set (0.00 sec)

mysql> select COUNT(cust_email)
    -> from customers;
+-------------------+
| COUNT(cust_email) |
+-------------------+
|                 3 |
+-------------------+
1 row in set (0.00 sec)

mysql> select MAX(prod_price) as max
    -> from products;
+-------+
| max   |
+-------+
| 55.00 |
+-------+
1 row in set (0.00 sec)

mysql> select MIN(prod_price) as min
    -> from products;
+------+
| min  |
+------+
| 2.50 |
+------+
1 row in set (0.00 sec)

mysql> select SUM(prod_price) as sum
    -> from products;
+--------+
| sum    |
+--------+
| 225.87 |
+--------+
1 row in set (0.00 sec)

//	DINSTINCT:可以在AVG和COUNT中使用，只包含不同的值，排除相同的值

mysql> select AVG(prod_price) as avg
    -> from products
    -> where vend_id = 1003;
+-----------+
| avg       |
+-----------+
| 13.212857 |
+-----------+
1 row in set (0.00 sec)

mysql> select AVG(DISTINCT prod_price) as avg
    -> from products
    -> where vend_id = 1003;
+-----------+
| avg       |
+-----------+
| 15.998000 |
+-----------+
1 row in set (0.00 sec)

```



##### 第十三章	分组数据	group by	having	with rollup

```mysql
mysql> select vend_id from products;
+---------+
| vend_id |
+---------+
|    1001 |
|    1001 |
|    1001 |
|    1002 |
|    1002 |
|    1003 |
|    1003 |
|    1003 |
|    1003 |
|    1003 |
|    1003 |
|    1003 |
|    1005 |
|    1005 |
+---------+
14 rows in set (0.00 sec)
----------------------------------------------------------------------------
mysql> select vend_id,COUNT(*) as num_prods
    -> from products
    -> group by vend_id with rollup;
+---------+-----------+
| vend_id | num_prods |
+---------+-----------+
|    1001 |         3 |
|    1002 |         2 |
|    1003 |         7 |
|    1005 |         2 |
|    NULL |        14 |
+---------+-----------+
5 rows in set (0.00 sec)
----------------------------------------------------------------------------
mysql> select vend_id,COUNT(*) as num_prods
    -> from products
    -> group by vend_id with rollup;
+---------+-----------+
| vend_id | num_prods |
+---------+-----------+
|    1001 |         3 |
|    1002 |         2 |
|    1003 |         7 |
|    1005 |         2 |
|    NULL |        14 |
+---------+-----------+
5 rows in set (0.00 sec)
----------------------------------------------------------------------------
mysql> select vend_id,COUNT(*) as num_prods
    -> from products
    -> where prod_price >= 10
    -> group by vend_id
    -> having COUNT(*) >=2;
+---------+-----------+
| vend_id | num_prods |
+---------+-----------+
|    1003 |         4 |
|    1005 |         2 |
+---------+-----------+
2 rows in set (0.00 sec)
----------------------------------------------------------------------------
mysql> select order_num,SUM(quantity*item_price) as sum
    -> from orderitems
    -> group by order_num
    -> having SUM(quantity*item_price) >= 50
    -> order by sum;
+-----------+---------+
| order_num | sum     |
+-----------+---------+
|     20006 |   55.00 |
|     20008 |  125.00 |
|     20005 |  149.87 |
|     20007 | 1000.00 |
+-----------+---------+
4 rows in set (0.12 sec)
```

###### select子句顺序

select	--> 	from	-->	where	-->	group by	-->	having	-->	order by	-->	limit

##### 第十四章	使用子查询

```mysql
mysql> select order_num
    -> from orderitems
    -> where prod_id = 'TNT2';
+-----------+
| order_num |
+-----------+
|     20005 |
|     20007 |
+-----------+
2 rows in set (0.00 sec)

mysql> select cust_id
    -> from orders
    -> where order_num IN (20005,20007);
+---------+
| cust_id |
+---------+
|   10001 |
|   10004 |
+---------+
2 rows in set (0.14 sec)
------以上两句合并-----------------------------------------------------
mysql> select cust_id
    -> from orders
    -> where order_num IN (select order_num from orderitems where prod_id = 'TNT2');
+---------+
| cust_id |
+---------+
|   10001 |
|   10004 |
+---------+
2 rows in set (0.04 sec)
-------------------------------------------------------------------------
mysql> select cust_name ,cust_contact
    -> from customers
    -> where cust_id IN (select cust_id from orders where order_num IN (select order_num from orderitems where prod_id ='TNT2'));
+----------------+--------------+
| cust_name      | cust_contact |
+----------------+--------------+
| Coyote Inc.    | Y Lee        |
| Yosemite Place | Y Sam        |
+----------------+--------------+
2 rows in set (0.00 sec)
---------------------------------------------------------------------------
mysql> select cust_name,cust_state,(select COUNT(*) from orders where orders.cust_id = customers.cust_id) as orders
    -> from customers
    -> order by cust_name;
+----------------+------------+--------+
| cust_name      | cust_state | orders |
+----------------+------------+--------+
| Coyote Inc.    | MI         |      2 |
| E Fudd         | IL         |      1 |
| Mouse House    | OH         |      0 |
| Wascals        | IN         |      1 |
| Yosemite Place | AZ         |      1 |
+----------------+------------+--------+
5 rows in set (0.05 sec)

```

##### 第十五章	联结表	inner  join	on			where  and  and and  

```mysql
mysql> select vend_name,prod_name,prod_price
    -> from vendors,products
    -> where vendors.vend_id = products.vend_id
    -> order by vend_name,prod_name;
+-------------+----------------+------------+
| vend_name   | prod_name      | prod_price |
+-------------+----------------+------------+
| ACME        | Bird seed      |      10.00 |
| ACME        | Carrots        |       2.50 |
| ACME        | Detonator      |      13.00 |
| ACME        | Safe           |      50.00 |
| ACME        | Sling          |       4.49 |
| ACME        | TNT (1 stick)  |       2.50 |
| ACME        | TNT (5 sticks) |      10.00 |
| Anvils R Us | .5 ton anvil   |       5.99 |
| Anvils R Us | 1 ton anvil    |       9.99 |
| Anvils R Us | 2 ton anvil    |      14.99 |
| Jet Set     | JetPack 1000   |      35.00 |
| Jet Set     | JetPack 2000   |      55.00 |
| LT Supplies | Fuses          |       3.42 |
| LT Supplies | Oil can        |       8.99 |
+-------------+----------------+------------+
14 rows in set (0.02 sec)

//功能如上		inner join  on

mysql> select vend_name,prod_name,prod_price
    -> from vendors inner join products
    -> on vendors.vend_id = products.vend_id;
+-------------+----------------+------------+
| vend_name   | prod_name      | prod_price |
+-------------+----------------+------------+
| Anvils R Us | .5 ton anvil   |       5.99 |
| Anvils R Us | 1 ton anvil    |       9.99 |
| Anvils R Us | 2 ton anvil    |      14.99 |
| LT Supplies | Fuses          |       3.42 |
| LT Supplies | Oil can        |       8.99 |
| ACME        | Detonator      |      13.00 |
| ACME        | Bird seed      |      10.00 |
| ACME        | Carrots        |       2.50 |
| ACME        | Safe           |      50.00 |
| ACME        | Sling          |       4.49 |
| ACME        | TNT (1 stick)  |       2.50 |
| ACME        | TNT (5 sticks) |      10.00 |
| Jet Set     | JetPack 1000   |      35.00 |
| Jet Set     | JetPack 2000   |      55.00 |
+-------------+----------------+------------+
14 rows in set (0.00 sec)



mysql> select prod_name,prod_price,quantity
    -> from orderitems,products,vendors
    -> where products.vend_id = vendors.vend_id
    -> and orderitems.prod_id = products.prod_id
    -> and order_num = 20005;
+----------------+------------+----------+
| prod_name      | prod_price | quantity |
+----------------+------------+----------+
| .5 ton anvil   |       5.99 |       10 |
| 1 ton anvil    |       9.99 |        3 |
| TNT (5 sticks) |      10.00 |        5 |
| Bird seed      |      10.00 |        1 |
+----------------+------------+----------+
4 rows in set (0.00 sec)


mysql> select cust_name,cust_contact
    -> from customers,orders,orderitems
    -> where customers.cust_id = orders.cust_id
    -> and orderitems.order_num = orders.order_num
    -> and prod_id = 'TNT2'
    -> ;
+----------------+--------------+
| cust_name      | cust_contact |
+----------------+--------------+
| Coyote Inc.    | Y Lee        |
| Yosemite Place | Y Sam        |
+----------------+--------------+
2 rows in set (0.09 sec)

```

##### 第十六章 	创建高级联结		left/right	outer	join	on

```mysql
mysql> select prod_id,prod_name
    -> from products
    -> where vend_id=(select vend_id from products where prod_id='DTNTR');
+---------+----------------+
| prod_id | prod_name      |
+---------+----------------+
| DTNTR   | Detonator      |
| FB      | Bird seed      |
| FC      | Carrots        |
| SAFE    | Safe           |
| SLING   | Sling          |
| TNT1    | TNT (1 stick)  |
| TNT2    | TNT (5 sticks) |
+---------+----------------+
7 rows in set (0.06 sec)

//功能如上
mysql> select p1.prod_id,p1.prod_name
    -> from products as p1,products as p2
    -> where p1.vend_id = p2.vend_id
    -> and p2.prod_id = 'DTNTR';
+---------+----------------+
| prod_id | prod_name      |
+---------+----------------+
| DTNTR   | Detonator      |
| FB      | Bird seed      |
| FC      | Carrots        |
| SAFE    | Safe           |
| SLING   | Sling          |
| TNT1    | TNT (1 stick)  |
| TNT2    | TNT (5 sticks) |
+---------+----------------+
7 rows in set (0.03 sec)

mysql> select customers.cust_id,orders.order_num
    -> from customers inner join orders
    -> on customers.cust_id = orders.cust_id;
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
|   10001 |     20009 |
|   10003 |     20006 |
|   10004 |     20007 |
|   10005 |     20008 |
+---------+-----------+
5 rows in set (0.00 sec)

mysql> select customers.cust_id,orders.order_num
    -> from customers left outer join orders
    -> on customers.cust_id = orders.cust_id;
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
|   10001 |     20009 |
|   10002 |      NULL |
|   10003 |     20006 |
|   10004 |     20007 |
|   10005 |     20008 |
+---------+-----------+
6 rows in set (0.00 sec)

```

##### 第十七章	组合查询	union			union all

union : 默认去重

union all	与之相反

```mysql
mysql> select vend_id,prod_id,prod_price
    -> from products
    -> where prod_price <= 5
    -> union
    -> select vend_id,prod_id,prod_price
    -> from products
    -> where vend_id in (1001,1002);
+---------+---------+------------+
| vend_id | prod_id | prod_price |
+---------+---------+------------+
|    1003 | FC      |       2.50 |
|    1002 | FU1     |       3.42 |
|    1003 | SLING   |       4.49 |
|    1003 | TNT1    |       2.50 |
|    1001 | ANV01   |       5.99 |
|    1001 | ANV02   |       9.99 |
|    1001 | ANV03   |      14.99 |
|    1002 | OL1     |       8.99 |
+---------+---------+------------+
8 rows in set (0.02 sec)
```



##### 第十八章	全文本搜索	match()		against()		with query expansion		

match()		指定搜索的列

against()		指定要使用的搜索表达式，指定搜索的词

```mysql
mysql> select note_text
    -> from productnotes
    -> where match(note_text) against('rabbit');
+-----------------------------------------------------------------------------------------------------------------------+
| note_text                                                                                                             |
+-----------------------------------------------------------------------------------------------------------------------+
| Customer complaint: rabbit has been able to detect trap, food apparently less effective now.                          |
| Quantity varies, sold by the sack load.
All guaranteed to be bright and orange, and suitable for use as rabbit bait. |
+-----------------------------------------------------------------------------------------------------------------------+
2 rows in set (0.08 sec)

//功能如上
mysql> select note_text
    -> from productnotes
    -> where note_text like '%rabbit%';
+-----------------------------------------------------------------------------------------------------------------------+
| note_text                                                                                                             |
+-----------------------------------------------------------------------------------------------------------------------+
| Quantity varies, sold by the sack load.
All guaranteed to be bright and orange, and suitable for use as rabbit bait. |
| Customer complaint: rabbit has been able to detect trap, food apparently less effective now.                          |
+-----------------------------------------------------------------------------------------------------------------------+
2 rows in set (0.00 sec)


//会给出全文搜索的等级值，搜索出的行按等级排序，或者排除等级为0的行，每行都有等级值，根据自生的词汇计算出
mysql> select note_text,match(note_text) against('rabbit') as rank
    -> from productnotes;
  
  
//查询扩展，输出所有包含anvils的句子，同时输出和前面句子拥有其他相同词汇的句子  
mysql> select note_text
    -> from productnotes
    -> where match(note_text) against('anvils' with query expansion);

```

#### 建表规则

普通索引：idx_字段名 

唯一索引：ux_字段名

##### 唯一索引和主键的区别

- 主键是一种约束，唯一索引是一种索引，两者在本质上是不同的。 
- 主键创建后一定包含一个唯一性索引，唯一性索引并不一定就是主键。 
- 唯一性索引列允许空值，而主键列不允许为空值。 
- 主键列在创建时，已经默认为空值 + 唯一索引了。 主键可以被其他表引用为外键，而唯一索引不能。 
- 一个表最多只能创建一个主键，但可以创建多个唯一索引。 
- 主键更适合那些不容易更改的唯一标识，如自动递增列、身份证号等



```mysql
-- 学科表
create table subject(
id int(10) auto_increment,
name varchar(20),
teacher_id int(10),
primary key (id),
index idx_teacher_id (teacher_id));			-- 普通索引
-- {
ALTER TABLE tbl_name ADD INDEX idx_index_name (column_list);
create index idx_myDeptIndex on detail(dept_id);
DROP INDEX [idx_indexName] ON mytable;
-- }

-- 教师表
create table teacher(
id int(10) auto_increment,
name varchar(20),
teacher_no varchar(20),
primary key (id),				-- 主键索引
unique index unx_teacher_no (teacher_no(20)));
-- {
ALTER TABLE tbl_name ADD PRIMARY KEY (column_list)
-- }

-- 学生表
create table student(
id int(10) auto_increment,
name varchar(20),
student_no varchar(20),
primary key (id),
unique index unx_student_no (student_no(20)));	-- 唯一索引
-- {
CREATE UNIQUE INDEX ux_indexName ON mytable(username(length))
ALTER table mytable ADD UNIQUE [ux_indexName] (username(length))
-- }
-- 学生成绩表
create table student_score(
id int(10) auto_increment,
student_id int(10),
subject_id int(10),
score int(10),
primary key (id),
index idx_student_id (student_id),
index idx_subject_id (subject_id));
-- 教师表增加名字普通索引
alter table teacher add index idx_name(name(20));
-- 主键索引能在创建表时指定
create table student_score(
id int(10) auto_increment,
student_id int(10),
subject_id int(10),
score int(10),
primary key (id),
index idx_student_id (student_id),
index idx_subject_id (subject_id)
);
-- 空间索引	SPATIAL 	用的很少
-- 全文索引	FULLTEXT	有了ElacticSearch替代品，实际使用不多，like+%实现模糊匹配
create table fulltext_test (
id int(11) NOT NULL AUTO_INCREMENT,
content text NOT NULL,
tag varchar(255),
PRIMARY KEY (id),
FULLTEXT KEY content_tag_fulltext(content,tag) // 创建联合全文索引列
) ENGINE=MyISAM DEFAULT CHARSET=utf8;1234567
-- hash 索引
create index index_test using hash on test1(id);
你会发现创建了也没有用，因为InnoDB和myIsam都不支持hash索引。


```

##### 哪些情况下不适合建索引

1.频繁更新的字段不适合建立索引 

2.where条件中用不到的字段不适合建立索引 

3.表数据可以确定比较少的不需要建索引 

4.数据重复且发布比较均匀的的字段不适合建索引（唯一性太差的字段不适合建立索引）

5.参与列计算的列不适合建索引，索引会失效



**索引不会包含有NULL值的列**



##### explain

explain关键字可以模拟MySQL优化器执行SQL语句，可以很好的分析SQL语句或表结构的性能 瓶颈。

##### CURRENT_TIMESTAMP

在insert一条新记录的收， 时间字段自动获取到当前时间

ON UPDATE CURRENT_TIMESTAMP

时间字段随着update命令的更新和实时变化

如果两个属性都设置了， 那么时间字段默认为当前时间， 且随着记录的更新而自动变化

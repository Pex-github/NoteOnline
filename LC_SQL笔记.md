//A inner join B取交集。

//A left join B取A全部，B 没有对应的值为null。

//Aright join B取B全部 A没有对应的值为null。

//A full outer join B 取并集，彼此没有对应的值为 null。对应条件在on后面填写。

-------------------------------

**获取第二高**

需要排序，使用order by(默认是升序 asc，即从小到大)，若想降序则使用关键字desc

​	去重，如果有多个相同的数据，使用关键字 distinct 去重判断临界输出，如果不存在第二高的薪水，查询应返回null，使用ifNull(查询，null)方法起别名，使用关键字as...

​	因为去了重，又按顺序排序，使用limit()方法，查询第二大的数据，即第二高的薪水，即limit(1,1) 

因为默认从0开始，所以第一个1是查询第二大的数，第二个1是表示往后显示多少条数据，这里只需要一条)

```sql
select ifNull((select distinct Salaryfrom Employeeorder by Salary desc limit 1,1),null) as SecondHightSalary
select max(Salary) SecondHighestSalary from employeewheresalary<(select max(salary) from employee)

```

--------

limit m，1表示要跳过m条数据，取1条；limit m offset1表示要跳过1条取m条，如果想要两种实现同一种功能，可以写 limit 1 offset m

题目是176.第二高的薪水的变形，将查询第二名变成查询 第N名

题目是176.第二高的薪水 的变形，将查询第二名变成查询 第N名



```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  SET N := N-1; 
RETURN (   # Write your MySQL query statement below.
  SELECT 
  	salary
  FROM
    employee
  GROUP BY
    salary
  ORDER BY
    salary DESC
  LIMIT N, 1 
);
END
```



 作者：luanhz链接：https://leetcode-cn.com/problems/nth-highest-salary/solution/mysql-zi-ding-yi-bian-liang-by-luanz/来源：力扣（LeetCode）著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
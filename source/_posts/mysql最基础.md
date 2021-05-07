---
title: mysql最基础
date: 2021-04-08 10:25:29
tags:
- 数据库
---
### MySQL
#### 1.mysql中的+号：
  只有一个功能：运算符
 ````mysql
select 100+90; #两个操作数都为数值型，则做加法运算

select '123'+90; #其中一方为字符型，试图将字符型转换成数值型
select 'abc'+90;                  #如果转换失败，则将字符型数组转换成0
                                  #如果转换成功，将继续做加法运算

select null+90; #只要一方为空，结果为空
````
---  
<!--more-->
#### 2.mysql做拼接
````mysql
select CONCAT('a','b','c') AS 结果;
````
---  
#### 3.去重
````mysql
SELECT DISTINCT department_id FROM employees;
````
---
#### 4.显示表departments的结构，并查询其中全部数据
```mysql
# 显示表结构
 desc departments;
#查询全部数据
 select * from departments;
```
---
#### 5.显示表departments的全部列，各个列之间用逗号连接，列头显示成OUT_PUT
````mysql
SELECT
	concat( department_id, ',',department_name,',', manager_id,',', ifnull( location_id, 0 ) ) AS OUT_PUT 
FROM
	departments;
````

---

#### 6.排序

- ***order by desc;*** #降序
- ***order by asc;*** #升序

---  
#### 7.字段的字节数
- length(字段名)

#### 8.常见函数

概念：类似于java的方法，将一组逻辑语句封装在方法体中，对外暴露方法名   

好处：
 - 隐藏了实现细节
 - 提高了代码的重用性    
 
调用：select函数名（实参列表）【from列表】;    

特点：
> 1.叫什么（函数名）
>
> 2.干什么人（函数功能）     

分类：
- 单行函数 concat、length、ifnull等
- 分组函数 做统计使用

##### 单行函数
一.字符函数
- length 获取参数值的字节个数
> select length('john');

- concat 拼接字符串
> select concat(last_name,"_",first_name) 姓名 from employees;

- upper 变大写,lower 变小写 
> select upper("john");
>
> select lower("joHn");
>
> select concat(Upper(last_name),lower(first_name)) 姓名 from employees;

- substr、substring 索引从1开始 截取指定索引处后的字符
> select substr("1234556",5) out_put; 

- inster 返回子串第一次出现的索引，如果找不到返回0
> select inster('1234567','123') as out_put;

- trim 去前后掉空格
> select trim('    1234    ') as out_put;
>
> select TRIM('aa' FROM 'aaaaaa1234aaaaaa1234aa') as out_put;

- lpad 用指定的字符实现左填充指定长度
> select lpad("123",8,"*") as out_put;
- rpad 
> select rpad("123",8,"*") as out_put;

- replace 替换

二.数学函数

- round 四舍五入
> select round(-1.55,1);

- ceil 向上取整，返回>=该参数的最小整数
> select ceil(1.02);

- floor 向小取整，返回<=该参数的最大整数
> select floor(1.02);  

- truncate 截断
> select truncate(1.02,1);  

- mod 取余
> select mod(10,3);  

三.日期函数

- now 返回当前系统日期+时间
> select now();

- curdate 返回当前系统日期，不包含时间
> select curdate();

- curtime 返回当前系统时间，不包含日期
> select curtime(); 

- year 可以获取指定的部分，年月日，时分秒
> select year(now());

- str_to_date 将字符通过指定格式转换成日期
> select str_to_date('1998-3-2','%Y-%c-%d') as out_put;

- date_format 将日期转换成字符
> select date_format(NOW(),'%Y-%c-%d') as out_put;

四.其他函数

- select version(); 
- select database(); 
- select user(); 

五.流程控制函数

- if函数：if else 的效果
> select IF(10<55,'大'，'小');

- case函数 的使用一：switch case 的效果
````sql
select salary 原始工资, department_id,
case department_id
when 30 then salary*1.1
when 40 then salary*1.2
when 50 then salary*1.3
else  salary
end as 新工资
from employees;
````

- case函数的使二：类似于 多重if


##### 分组函数

分类：
> sum 求和
>
> avg 平均值
>
> max 最大值
>
> min 最小值
>
> count 按个数

###### 1.Group By 按表达式或函数分组

案例：   
&ensp;&ensp;按员工姓名和长度分组，查询每一组员工的个人，筛选员工个数>5的有哪些
```sql
SELECT COUNT(1),LENGTH(last_name) 
FROM employees 
GROUP BY LENGTH(last_name)
HAVING COUNT(1)>5;
```
###### 2.Group By 按表多个字段分组
案例：   
&ensp;&ensp;查询每个部门每个工种的员工平均工资
```sql
SELECT
	AVG( salary ),
	department_id,
	job_id 
FROM
	employees 
GROUP BY
	department_id,
	job_id;
```
###### 3.Group By 添加排序
案例：   
&ensp;&ensp;查询每个部门每个工种的员工平均工资,并且按平均工资高低显示
```sql
SELECT
	AVG( salary ),
	department_id,
	job_id 
FROM
	employees 
GROUP BY
	department_id,
	job_id
	ORDER BY AVG(salary) DESC;
```

##### 连接查询
含义： 又称多表查询，当查询字段来自于多个表时，就会用到连接查询   

分类：  
> 按年代分类：
>> sql92标准：等值连接，非等值连接，自连接
>>
>> sql199标准（推荐）
>
> 按功能分类：
>> 内连接 ：inner
>>
>> 外连接 
>>> 左外连接：left
>>>
>>> 右外连接：right
>>>
>>> 全外连接：full
>>
>> 交叉连接：cross
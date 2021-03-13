# 								MYSQL学习笔记

## 一、命令行启动MySQL服务

```mysql
net start mysql80
```

##  二、查询

### 2.1 基本查询

#### 1)基本查询:

```mysql
select * from departments;
select last_name,email FROM employees;
SELECT * FROM employees;
```

#### 2)查询表达式：

```mysql
SELECT 100;
SELECT 100*100;
SELECT 100%90;
```

#### 3)查询函数（比如查询MySQL版本）:

```mysql
SELECT VERSION();
```

#### 4)起别名：

方式一：

```mysql
SELECT 100%90 AS 结果;
```

方式二：

```mysql
SELECT 100%90 结果;
```

##### 特殊情况：

如果有关键字或空格  别名字段叫引号（单或者双）

```mysql
SELECT 100%90 "out put";
```

#### 5)去重（DISTINCT）*

去除相同的部门编号

```mysql
SELECT DISTINCT department_id FROM employees;
```

#### 6)+ 的使用：

仅仅只有一个功能：运算符

```mysql
select 100+90; 		两个操作数都为数值型，则做加法运算
select '123'+90;	只要其中一方为字符型，试图将字符型数值转换成数值型
					如果转换成功，则继续做加法运算
select 'john'+90;	如果转换失败，则将字符型数值转换成0

select null+10; 	只要其中一方为null，则结果肯定为null
```

#### 7)字符串拼接：CONCAT(str1,str2,...)函数 *

查询名和姓 连接成一个字段，并显示为姓名

```mysql
SELECT CONCAT(last_name,first_name) AS 姓名 FROM employees;
```

#### 8)显示表的结构   DESC

```mysql
DESC employees;
```

#### 练习：

显示出表employees的全部列，各个列之间用逗号连接，列头显示成OUT_PUT，

如果查询的字段有null,使用IFNULL（）

```mysql
SELECT
	CONCAT(first_name,',',last_name,IFNULL(commission_pct,0)) 
	AS 	'OUT PUT'
FROM 
	employees;
```

### 2.2条件查询

#### 1)语法：

```mysql
select 
	查询列表
from
	表名
where
	筛选条件;
```

#### 2)分类：

##### 1、按条件表达式筛选

简单条件运算符：> < = != <> >= <=

```mysql
#案例1：查询工资>12000的员工信息
SELECT * FROM employees WHERE salary > 12000;
```

##### 2、按逻辑表达式筛选

逻辑运算符：
作用：用于连接条件表达式

&&	 || 	!

and 	or 	not

```text
&&和and：两个条件都为true，结果为true，反之为false
||或or： 只要有一个条件为true，结果为true，反之为false
!或not： 如果连接的条件本身为false，结果为true，反之为false
```
```mysql
#案例1：查询工资z在10000到20000之间的员工名、工资以及奖金
SELECT
		last_name,
		salary,
		commission_pct
FROM
		employees
WHERE
-- 		salary BETWEEN 10000 AND 20000;
		salary >= 10000 AND salary <= 20000;
		
#案例2：查询部门编号不是在90到110之间，或者工资高于15000的员工信息
SELECT * FROM employees 
	WHERE 
-- 	department_id NOT BETWEEN 90 AND 110 OR salary > 15000;
	NOT (department_id >= 90 AND department_id <= 110) OR salary > 15000;
```

#### 3)模糊查询

##### 1、like

```mysql
特点：一般和通配符搭配使用
	通配符：
	% 任意多个字符,包含0个字符
	_ 任意单个字符
```

```mysql
#案例1：查询员工名中包含字符a的员工信息
SELECT * FROM employees WHERE last_name LIKE '%a%';

#案例2：查询员工名中第三个字符为e，第五个字符为a的员工名和工资
SELECT last_name,salary FROM employees WHERE last_name LIKE '__n_a%';

#案例3：查询员工名中第二个字符为_的员工名 (转义字符)  或者用 ESCAPE '任意符号' （第二个字符为 _ ）
SELECT last_name FROM employees 
	WHERE 
-- 	last_name LIKE '_\_%';
	last_name LIKE '_$_%' ESCAPE '$';
```

##### 2、between and

```mysql
①使用between and 可以提高语句的简洁度
②包含临界值
③两个临界值不要调换顺序
```

```mysql
#案例1：查询员工编号在100到120之间的员工信息	 
SELECT * FROM employees WHERE department_id BETWEEN 100 AND 120;
```

##### 3、in

```mysql
含义：判断某字段的值是否属于in列表中的某一项
特点：
	①使用in提高语句简洁度
	②in列表的值类型必须一致或兼容
	③in列表中不支持通配符
```

```mysql
#案例：查询员工的工种编号是 IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号
SELECT 
	last_name,
	job_id
FROM 
	employees
WHERE
	job_id IN( 'IT_PROG' , 'AD_VP' , 'AD_PRES');
```

##### 4、is null 

```mysql
=或<>不能用于判断null值
is null或is not null 可以判断null值
```

```mysql
#案例1：查询没有奖金的员工名和奖金率
SELECT
	last_name,
	commission_pct
FROM 
	employees
WHERE
	commission_pct IS NULL;
```

##### 5、补充：安全等于  <=>

```mysql
#案例1：查询没有奖金的员工名和奖金率
SELECT
		last_name,
		commission_pct
FROM 
		employees
WHERE
		commission_pct <=> NULL;
	
#案例2：查询工资为12000的员工信息
SELECT 
		last_name,salary 
FROM 
		employees 
WHERE 
		salary <=>12000;
```

#is null 	对比	 <=>

IS NULL:仅仅可以判断NULL值，可读性较高，建议使用
<=>    :既可以判断NULL值，又可以判断普通的数值，可读性较低

### 2.3排序查询

#### 1）语法：

```mysql
select 查询列表
from 表名
[where  筛选条件]
order by 排序的字段或表达式;
```

#### 2）特点：

1、asc代表的是升序，可以省略，desc代表的是降序

2、order by子句可以支持单个字段、别名、表达式、函数、多个字段

3、order by子句在查询语句的最后面，除了limit子句

#### 3）按单个字段排序

```mysql
#案例：查询部门编号>=90的员工信息，并按员工编号降序
SELECT * FROM employees WHERE department_id >= 90 ORDER BY employee_id DESC;
```

#### 4）按表达式排序

```mysql
#案例：查询员工信息 按年薪降序
SELECT *,salary*12*(1+IFNULL(commission_pct,0)) AS 年薪
FROM employees
ORDER BY salary*12*(1+IFNULL(commission_pct,0)) DESC;
```

#### 5）按别名排序

```mysql
#案例：查询员工信息 按年薪升序
SELECT *,salary*12*(1+IFNULL(commission_pct,0)) AS 年薪
FROM employees
ORDER BY 年薪 DESC;
```

#### 6）按函数排序

```mysql
#案例：查询员工名，并且按名字的长度降序
SELECT LENGTH(last_name) 字节长度,last_name
FROM employees
ORDER BY LENGTH(last_name)  DESC;
```

#### 7）按多个字段排序

```mysql
#案例：查询员工信息，要求先按工资降序，再按employee_id升序
SELECT * 
FROM employees
ORDER BY salary DESC,employee_id ASC;
```

#### 8）练习：

```mysql
#1.查询员工的姓名和部门号和年薪，按年薪降序 按姓名升序
SELECT last_name,salary*12*(1+IFNULL(commission_pct,0)) AS 年薪
FROM employees
ORDER BY 年薪 DESC,last_name ASC;

#2.选择工资不在8000到17000的员工的姓名和工资，按工资降序
SELECT last_name,salary
FROM employees
WHERE salary NOT BETWEEN 8000 AND 17000
ORDER BY salary;

#3.查询邮箱中包含e的员工信息，并先按邮箱的字节数降序，再按部门号升序
SELECT * 
FROM employees
WHERE email LIKE '%e%'
ORDER BY LENGTH(email) DESC,department_id ASC;
```

### 2.4常见函数

#### 1）概念：

概念：类似于java的方法，将一组逻辑语句封装在方法体中，对外暴露方法名
好处：1、隐藏了实现细节  2、提高代码的重用性
调用：select 函数名(实参列表) 【from 表】;
特点：
	①叫什么（函数名）
	②干什么（函数功能）

#### 2）分类：

	1、单行函数
	如 concat、length、ifnull等
	2、分组函数
	功能：做统计使用，又称为统计函数、聚合函数、组函数
#### 3）常见函数

##### 一、单行函数

```mysql
字符函数：
length:获取字节个数(utf-8一个汉字代表3个字节,gbk为2个字节)、concat、substr、instr、trim、upper、lower、lpad、rpad、replace
数学函数：
round、ceil、floor、truncate、mod	
日期函数：
now、curdate、curtime、year、month、monthname、day、hour、minute、second、str_to_date、date_format
其他函数：
version、database、user
控制函数：
if、case
```

###### 1、字符函数

```mysql
#1.length 获取参数值的字节个数
SELECT LENGTH('john');
SELECT LENGTH('张三丰hahaha');
-- 查看数据库的字符集
SHOW VARIABLES LIKE '%char%';
#2.concat 拼接字符串
SELECT CONCAT(last_name,'_',first_name) 姓名 FROM employees;
#3.upper、lower
SELECT UPPER('john');
SELECT LOWER('joHn');
#示例：将姓变大写，名变小写，然后拼接
SELECT CONCAT(UPPER(last_name),LOWER(first_name)) AS 姓名 FROM employees;
#4.substr、substring
注意：索引从1开始
#截取从指定索引处后面所有字符
SELECT SUBSTR("你是傻逼吗",3) AS "out put";
#截取从指定索引处指定字符长度的字符
SELECT SUBSTR("你是傻逼吗",3,2) AS "out put";
#案例：姓名中首字符大写，其他字符小写然后用_拼接，显示出来
SELECT CONCAT(UPPER(SUBSTR(last_name,1,1)),'_',LOWER(SUBSTR(last_name,2)))  AS 啊啊啊
FROM employees;
#5.instr 返回子串第一次出现的索引，如果找不到返回0
SELECT INSTR('你是傻逼吗','傻逼') AS "out put";
SELECT INSTR('杨不殷六侠悔爱上了殷六侠','殷八侠') AS out_put;
#6.trim  去重
SELECT LENGTH(TRIM('    张翠山    ')) AS out_put;
SELECT TRIM('aa' FROM 'aaaaaaaa张aaaaaaaaaaaa翠山aaaaaaaaaaaaaaaaaaaaa')  AS out_put;
#7.lpad 用指定的字符实现左填充指定长度
SELECT LPAD("b",10,"s");
#8.rpad 用指定的字符实现右填充指定长度
SELECT RPAD("s",10,"b");
#9.replace 替换
SELECT REPLACE('周芷若周芷若张无忌爱上了周芷若','周芷若','赵敏') AS out_put;
```

###### 2、数学函数

```mysql
#round 四舍五入
SELECT ROUND(-1.5);
SELECT ROUND(-1.567,2);  保留小数点2位
#ceil 向上取整,返回>=该参数的最小整数
SELECT CEIL(-1.02);
#floor 向下取整，返回<=该参数的最大整数
SELECT FLOOR(1.2);
#truncate 截断
SELECT TRUNCATE(1.6999,1);
#mod取余
/*
mod(a,b) ：  a-a/b*b
mod(-10,-3):-10- (-10)/(-3)*（-3）=-1
*/
SELECT MOD(10,3);
```

###### 3、日期函数

```mysql
#now 返回当前系统日期+时间
SELECT NOW();
#curdate 返回当前系统日期，不包含时间
SELECT CURDATE();
#curtime 返回当前时间，不包含日期
SELECT CURTIME();
#可以获取指定的部分，年、月、日、小时、分钟、秒
SELECT YEAR(NOW()) 年;
SELECT YEAR('1998-1-1') 年;
SELECT  YEAR(hiredate) 年 FROM employees;
SELECT MONTH(NOW()) 月;
SELECT MONTHNAME(NOW()) 月;
#str_to_date 将字符通过指定的格式转换成日期
SELECT STR_TO_DATE('1998-3-2','%Y-%c-%d') AS out_put;
#查询入职日期为1992--4-3的员工信息
SELECT * FROM employees WHERE hiredate = '1992-4-3';
SELECT * FROM employees WHERE hiredate = STR_TO_DATE('4-3 1992','%c-%d %Y');
#date_format 将日期转换成字符
SELECT DATE_FORMAT(NOW(),"%y年%m月%d日");
#查询有奖金的员工名和入职日期(xx月/xx日 xx年)
SELECT last_name,DATE_FORMAT(hiredate,"%m月/%d日 %y年") AS 入职日期
FROM employees
WHERE commission_pct IS NOT NULL;
```

###### 4、其他函数

```mysql
SELECT VERSION();
SELECT DATABASE();
SELECT USER();
```

###### 5、流程控制函数

```mysql
#1.if函数： if else 的效果
SELECT IF(10>5,"大","小");
SELECT last_name,salary,IF(commission_pct IS NOT NULL,"有奖金","没奖金") AS "备注"
FROM employees;
#2.case函数的使用一： switch case 的效果
/*
java中
switch(变量或表达式){
	case 常量1：语句1;break;
	...
	default:语句n;break;
}
mysql中:
case 要判断的字段或表达式
when 常量1 then 要显示的值1或语句1;
when 常量2 then 要显示的值2或语句2;
...
else 要显示的值n或语句n;
end
*/
/*案例：查询员工的工资，要求
部门号=30，显示的工资为1.1倍
部门号=40，显示的工资为1.2倍
部门号=50，显示的工资为1.3倍
其他部门，显示的工资为原工资
*/
SELECT salary 原工资,department_id,
CASE department_id
	WHEN 30 THEN salary*100
	WHEN 40 THEN salary*10
	WHEN 90 THEN salary*18
	ELSE salary
	END AS 新工资
	FROM employees;
#3.case 函数的使用二：类似于 多重if
/*
java中：
if(条件1){
	语句1；
}else if(条件2){
	语句2；
}
...
else{
	语句n;
}

mysql中：
case 
when 条件1 then 要显示的值1或语句1
when 条件2 then 要显示的值2或语句2
。。。
else 要显示的值n或语句n
end
*/
#案例：查询员工的工资的情况
-- 如果工资>20000,显示A级别
-- 如果工资>15000,显示B级别
-- 如果工资>10000，显示C级别
-- 否则，显示D级别
SELECT salary,
CASE
WHEN salary > 20000 THEN "A级"
WHEN salary > 15000 THEN "B级"
WHEN salary > 10000 THEN "C级"
ELSE "D级" 
END AS 工资级别
FROM employees;
```

###### 6、练习：

```mysql
#1.	显示系统时间(注：日期+时间)
SELECT NOW();
#2.	查询员工号，姓名，工资，以及工资提高百分之20%后的结果（new salary）
SELECT * from employees;
SELECT employee_id,last_name,salary,salary*1.2 AS 新工资 FROM employees;
#3.	将员工的姓名按首字母排序，并写出姓名的长度（length）
SELECT LENGTH(last_name) 长度,SUBSTR(last_name,1,1) 首字母,last_name
FROM employees
ORDER BY 首字母;
#4.	做一个查询，产生下面的结果
-- <last_name> earns <salary> monthly but wants <salary*3>
-- Dream Salary
-- King earns 24000 monthly but wants 72000
SELECT CONCAT(last_name," earns ",salary," monthly but wants ",salary*3) AS "Dream Salary" FROM employees;
#5.	使用case-when，按照下面的条件：
-- job                  grade
-- AD_PRES            A
-- ST_MAN             B
-- IT_PROG             C
-- SA_REP              D
-- ST_CLERK           E
-- 产生下面的结果
-- Last_name	Job_id	Grade
-- king	AD_PRES	A
SELECT last_name,job_id AS job,
CASE job_id
	WHEN "AD_PRES" THEN "A"
	WHEN "ST_MAN" THEN "B"
	WHEN "IT_PROG" THEN "C"
	WHEN "SA_REP" THEN "D"
	WHEN "ST_CLERK" THEN "E"
	END AS "grade"
FROM employees
WHERE job_id = "AD_PRES";
```

##### 二、分组函数

###### 1、功能：

用作统计使用，又称为聚合函数或统计函数或组函数

###### 2、分类：

sum 求和、avg 平均值、max 最大值 、min 最小值 、count 计算个数

###### 3、特点：

1、sum、avg一般用于处理数值型， max、min、count可以处理任何类型
2、以上分组函数都忽略null值
3、可以和distinct搭配实现去重的运算
4、count函数的单独介绍，一般使用count(*)用作统计行数
5、和分组函数一同查询的字段要求是group by后的字段

###### 4、简单使用

```mysql
SELECT SUM(salary) FROM employees;  
SELECT AVG(salary) FROM employees;
SELECT MIN(salary) FROM employees;
SELECT MAX(salary) FROM employees;
SELECT COUNT(salary) FROM employees;
SELECT SUM(salary) 和,AVG(salary) 平均,MAX(salary) 最高,MIN(salary) 最低,COUNT(salary) 个数
FROM employees;
SELECT SUM(salary) 和,ROUND(AVG(salary),2) 平均,MAX(salary) 最高,MIN(salary) 最低,COUNT(salary) 个数
FROM employees;
```

###### 5、参数支持哪些类型

```mysql
SELECT SUM(last_name) ,AVG(last_name) FROM employees;    计算asc码
SELECT SUM(hiredate) ,AVG(hiredate) FROM employees;
SELECT MAX(last_name),MIN(last_name) FROM employees;
SELECT MAX(hiredate),MIN(hiredate) FROM employees;
SELECT COUNT(commission_pct) FROM employees;
SELECT COUNT(last_name) FROM employees;
```

###### 6、是否忽略null

```mysql
SELECT SUM(commission_pct) ,AVG(commission_pct),SUM(commission_pct)/35,SUM(commission_pct)/107 FROM employees;
SELECT MAX(commission_pct) ,MIN(commission_pct) FROM employees;
SELECT COUNT(commission_pct) FROM employees;
SELECT commission_pct FROM employees;
```

###### 7、和distinct搭配

```mysql
SELECT SUM(DISTINCT salary),SUM(salary) FROM employees;
SELECT COUNT(DISTINCT salary),COUNT(salary) FROM employees;
```

###### 8、count函数的详细介绍

```mysql
SELECT COUNT(salary) FROM employees;
SELECT COUNT(*) FROM employees;
SELECT COUNT(1) FROM employees;
效率：
MYISAM存储引擎下  ，COUNT(*)的效率高
INNODB存储引擎下，COUNT(*)和COUNT(1)的效率差不多，比COUNT(字段)要高一些
```

###### 9、和分组函数一同查询的字段有限制

```mysql
SELECT AVG(salary),employee_id  FROM employees;
```

###### 10、练习

```mysql
#1.查询公司员工工资的最大值，最小值，平均值，总和
SELECT MAX(salary) 最大值,MIN(salary) 最小值,AVG(salary) 平均值,SUM(salary) 和
FROM employees;
#2.查询员工表中的最大入职时间和最小入职时间的相差天数 （DIFFRENCE）
SELECT MAX(hiredate) 最大,MIN(hiredate) 最小,(MAX(hiredate)-MIN(hiredate))/1000/3600/24 DIFFRENCE
FROM employees;
SELECT DATEDIFF(MAX(hiredate),MIN(hiredate)) DIFFRENCE
FROM employees;
SELECT DATEDIFF(NOW(),'1994-8-4');
#3.查询部门编号为90的员工个数
SELECT COUNT(*) FROM employees WHERE department_id = 90;
```

### 2.5分组查询

#### 1）语法：

```mysql
select 查询列表
from 表
[where 筛选条件]
group by 分组的字段
[order by 排序的字段];
```

#### 2）特点：

```mysql
1、和分组函数一同查询的字段必须是group by后出现的字段
2、筛选分为两类：分组前筛选和分组后筛选
		   针对的表			       位置		    连接的关键字
分组前筛选	原始表				    group by前	   where
分组后筛选	group by后的结果集      group by后	  having
问题1：分组函数做筛选能不能放在where后面  答：不能
问题2：where——group by——having
一般来讲，能用分组前筛选的，尽量使用分组前筛选，提高效率
3、分组可以按单个字段也可以按多个字段
4、可以搭配着排序使用
```

#### 3）简单的分组

```mysql
#案例1：查询每个工种的员工平均工资
SELECT AVG(salary),job_id
FROM employees
GROUP BY job_id;
#案例2：查询每个位置的部门个数
SELECT COUNT(*),location_id FROM departments GROUP BY location_id;
```

#### 4）可以实现分组前的筛选

```mysql
#案例1：查询邮箱中包含a字符的 每个部门的最高工资
SELECT MAX(salary),department_id,email
FROM employees
WHERE email LIKE "%a%"
GROUP BY department_id;
#案例2：查询有奖金的每个领导手下员工的平均工资
SELECT MAX(salary),manager_id
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY manager_id;
```

#### 5）分组后筛选

```mysql
#案例：查询哪个部门的员工个数>5
#①查询每个部门的员工个数
SELECT COUNT(*),department_id
FROM employees
GROUP BY department_id;
#② 筛选刚才①结果
SELECT COUNT(*),department_id
FROM employees
GROUP BY department_id
HAVING COUNT(*)>5;
#案例2：每个工种有奖金的员工的最高工资>12000的工种编号和最高工资
SELECT MAX(salary),job_id
FROM employees
WHERE commission_pct is not NULL
GROUP BY job_id
HAVING MAX(salary)>12000;
#案例3：领导编号>102的每个领导手下的最低工资大于5000的领导编号和最低工资
SELECT MIN(salary),manager_id
FROM employees
WHERE manager_id>102
GROUP BY manager_id
HAVING MIN(salary) > 5000;
```

#### 6）添加排序

```mysql
#案例：每个工种有奖金的员工的最高工资>6000的工种编号和最高工资,按最高工资升序
SELECT job_id,MAX(salary) AS a
FROM employees
GROUP BY job_id
HAVING a > 6000
ORDER BY a DESC;
```

#### 7）按多个字段分组

```mysql
#案例：查询每个工种每个部门的最低工资,并按最低工资降序
SELECT MIN(salary),job_id,department_id
FROM employees
GROUP BY job_id,department_id
ORDER BY MIN(salary) DESC;
```

#### 8）练习

```mysql
#1.查询各job_id的员工工资的最大值，最小值，平均值，总和，并按job_id升序
SELECT MAX(salary),MIN(salary),AVG(salary),SUM(salary),job_id
FROM employees
GROUP BY job_id
ORDER BY job_id DESC;
#2.查询员工最高工资和最低工资的差距（DIFFERENCE）
SELECT MAX(salary)-MIN(salary) AS 差距 FROM employees;
#3.查询各个管理者手下员工的最低工资，其中最低工资不能低于6000，没有管理者的员工不计算在内
SELECT MIN(salary),manager_id
FROM employees
WHERE manager_id is not NULL
GROUP BY manager_id
HAVING MIN(salary) > 6000;
#4.查询所有部门的编号，员工数量和工资平均值,并按平均工资降序
SELECT department_id,COUNT(*),AVG(salary) a
FROM employees 
GROUP BY department_id
ORDER BY a DESC;
#5.选择具有各个job_id的员工人数
SELECT COUNT(*),job_id
FROM employees
GROUP BY job_id;
```

### 2.6连接查询

#### 1）含义：

又称多表查询，当查询的字段来自于多个表时，就会用到连接查询

#### 2）笛卡尔乘积现象：

表1 有m行，表2有n行，结果=m*n行

​		发生原因：没有有效的连接条件
​		如何避免：添加有效的连接条件

#### 3）分类：

##### ①按年代分类：

​	sql92标准:仅仅支持内连接
​	sql99标准【推荐】：支持内连接+外连接（左外和右外）+交叉连接

##### ②按功能分类：

​		内连接：
​			等值连接
​			非等值连接
​			自连接
​		外连接：
​			左外连接
​			右外连接
​			全外连接

​		交叉连接

#### 4）sql92标准

##### ①等值连接

① 多表等值连接的结果为多表的交集部分
②n表连接，至少需要n-1个连接条件
③ 多表的顺序没有要求
④一般需要为表起别名
⑤可以搭配前面介绍的所有子句使用，比如排序、分组、筛选

```mysql
#案例1：查询女神名和对应的男神名
-- SELECT NAME,boyName 
-- FROM boys,beauty
-- WHERE beauty.boyfriend_id= boys.id;
#案例2：查询员工名和对应的部门名
USE myemployees;
SELECT last_name,department_name
FROM employees a,departments b
WHERE a.department_id = b.department_id;
```

##### ②为表起别名

①提高语句的简洁度
②区分多个重名的字段

注意：如果为表起了别名，则查询的字段就不能使用原来的表名去限定

```mysql
#查询员工名、工种号、工种名
SELECT last_name,e.job_id,job_title
FROM employees e,jobs j
WHERE e.job_id = j.job_id;
```

##### ③可以加筛选

```mysql
#案例1：查询有奖金的员工名、部门名
SELECT last_name,department_name,commission_pct
FROM employees e,departments d
WHERE e.department_id=d.department_id
AND e.commission_pct IS NOT NULL;
#案例2：查询城市名中第二个字符为o的部门名和城市名
SELECT department_name,city
FROM departments d,locations l
WHERE d.location_id=l.location_id
AND d.department_name LIKE "_o%";
```

##### ④可以加分组

```mysql
#案例1：查询每个城市的部门个数
SELECT COUNT(*) 个数,city
FROM departments d,locations l
WHERE d.location_id=l.location_id
GROUP  BY city;
#案例2：查询有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资
SELECT department_name,d.manager_id,MIN(salary)
FROM employees e,departments d
WHERE e.department_id = d.department_id
AND e.commission_pct IS NOT NULL
ORDER BY department_name;
```

##### ⑤可以加排序

```mysql
#案例：查询每个工种的工种名和员工的个数，并且按员工个数降序
SELECT job_title,COUNT(*) 
FROM employees e,jobs d
WHERE e.job_id = d.job_id
GROUP BY job_title
ORDER BY COUNT(*)  DESC;
```

##### ⑥可以实现三表连接

```mysql
#案例：查询员工名、部门名和所在的城市
SELECT last_name,department_name,city
FROM employees e,departments d,locations l
WHERE e.department_id=d.department_id
AND d.location_id = l.location_id
AND l.city LIKE "s%"
ORDER BY d.department_name;
```

##### ⑦非等值连接

```mysql
#案例1：查询员工的工资和工资级别
SELECT salary,grade_level
FROM employees e,job_grades j
WHERE e.salary BETWEEN j.lowest_sal AND j.highest_sal
AND grade_level = "A";
```

##### ⑧自连接

```mysql
#案例：查询 员工名和上级的名称
SELECT e.employee_id,e.last_name,d.employee_id,d.last_name
FROM employees e,employees d 
WHERE e.employee_id=d.manager_id;
```

#### 5)sql99语法

##### ①语法：

​	select 查询列表
​	from 表1 别名 【连接类型】
​	join 表2 别名 
​	on 连接条件
​	【where 筛选条件】
​	【group by 分组】
​	【having 筛选条件】
​	【order by 排序列表】

##### ②分类：

内连接（★）：inner
外连接
	左外(★):left 【outer】
	右外(★)：right 【outer】
	全外：full【outer】
交叉连接：cross 

##### ③内连接

###### 1、语法：

select 查询列表
from 表1 别名
inner join 表2 别名
on 连接条件;

###### 2、分类：

等值
非等值
自连接

###### 3、特点：

Ⅰ：添加排序、分组、筛选
Ⅱ：inner可以省略
Ⅲ：筛选条件放在where后面，连接条件放在on后面，提高分离性，便于阅读
Ⅳ：inner join连接和sql92语法中的等值连接效果是一样的，都是查询多表的交集

###### 4、等值连接

```mysql
#案例1.查询员工名、部门名
SELECT last_name,department_name
FROM employees e
INNER JOIN 	departments d
ON e.department_id=d.department_id;
#案例2.查询名字中包含e的员工名和工种名（添加筛选）
SELECT last_name,job_title
FROM employees e
INNER JOIN jobs j
ON e.job_id= j.job_id
WHERE e.last_name LIKE "%e%";
#3. 查询部门个数>3的城市名和部门个数，（添加分组+筛选）
#①查询每个城市的部门个数
#②在①结果上筛选满足条件的
SELECT city,COUNT(*)
FROM locations l
INNER JOIN departments d 
ON l.location_id = d.location_id
GROUP BY city
HAVING COUNT(*)>0;
#案例4.查询哪个部门的员工个数>3的部门名和员工个数，并按个数降序（添加排序）
#①查询每个部门的员工个数
#② 在①结果上筛选员工个数>3的记录，并排序
SELECT department_name,COUNT(*)
FROM departments d
INNER JOIN employees e
ON d.department_id = e.department_id
GROUP BY department_name
HAVING COUNT(*)>3
ORDER BY COUNT(*) DESC;
#5.查询员工名、部门名、工种名，并按部门名降序（添加三表连接）
SELECT last_name,department_name,job_title
FROM employees e
INNER JOIN departments d ON e.department_id = d.department_id
INNER JOIN jobs j ON e.job_id = j.job_id;
```

###### 5、非等值连接

```mysql
#查询员工的工资级别
SELECT grade_level,salary
FROM employees e
INNER JOIN job_grades j 
ON e.salary BETWEEN j.lowest_sal AND j.highest_sal
ORDER BY e.salary DESC;
 #查询工资级别的个数>20的部门个数，并且按工资级别降序
SELECT COUNT(*),grade_level
FROM employees e 
INNER JOIN job_grades j
on e.salary BETWEEN j.lowest_sal AND j.highest_sal
GROUP BY grade_level
HAVING COUNT(*)>20
ORDER BY grade_level DESC;
```

6、自连接

```mysql
 #查询员工的名字、上级的名字
 SELECT m.last_name,n.last_name
 FROM employees m
 INNER JOIN employees n
 ON m.employee_id = n.manager_id;
 #查询姓名中包含字符k的员工的名字、上级的名字
 SELECT m.last_name,n.last_name
 FROM employees m
 INNER JOIN employees n
 ON m.employee_id = n.manager_id
 WHERE n.last_name LIKE "%k%";
```

##### ④外连接

应用场景：用于查询一个表中有，另一个表没有的记录

###### Ⅰ特点：

 1、外连接的查询结果为主表中的所有记录
	   如果从表中有和它匹配的，则显示匹配的值
	   如果从表中没有和它匹配的，则显示null
	   外连接查询结果=内连接结果+主表中有而从表没有的记录
 2、左外连接，left join左边的是主表
       右外连接，right join右边的是主表
 3、左外和右外交换两个表的顺序，可以实现同样的效果 
 4、全外连接=内连接的结果+表1中有但表2没有的+表2中有但表1没有的

###### Ⅱ案例：

```mysql
 #左外连接
 SELECT b.*,bo.*
 FROM boys bo
 LEFT OUTER JOIN beauty b
 ON b.`boyfriend_id` = bo.`id`
 WHERE b.`id` IS NULL;
 #案例1：查询哪个部门没有员工
 #左外
 SELECT d.*,e.employee_id
 FROM departments d
 LEFT OUTER JOIN employees e
 ON d.`department_id` = e.`department_id`
 WHERE e.`employee_id` IS NULL;
 #右外
 SELECT d.*,e.employee_id
 FROM employees e
 RIGHT OUTER JOIN departments d
 ON d.`department_id` = e.`department_id`
 WHERE e.`employee_id` IS NULL; 
 #全外
 USE girls;
 SELECT b.*,bo.*
 FROM beauty b
 FULL OUTER JOIN boys bo
 ON b.`boyfriend_id` = bo.id;
 #交叉连接
 SELECT b.*,bo.*
 FROM beauty b
 CROSS JOIN boys bo;
```

### 2.7子查询

#### 1）含义：

出现在其他语句中的select语句，称为子查询或内查询
外部的查询语句，称为主查询或外查询

#### 2）分类：

###### ①按子查询出现的位置：

select后面：
		仅仅支持标量子查询
	from后面：
	支持表子查询
where或having后面：★
	标量子查询（单行） √
	列子查询  （多行） √
	行子查询

exists后面（相关子查询）
	表子查询

###### ②按结果集的行列数不同：

标量子查询（结果集只有一行一列）
列子查询（结果集只有一列多行）
行子查询（结果集有一行多列）
表子查询（结果集一般为多行多列）

#### 3）where或having后面

1、标量子查询（单行子查询）
2、列子查询（多行子查询）
3、行子查询（多列多行）

特点：
①子查询放在小括号内
②子查询一般放在条件的右侧
③标量子查询，一般搭配着单行操作符使用

​	< >= <= = <>
列子查询，一般搭配着多行操作符使用
in、any/some、all
④子查询的执行优先于主查询执行，主查询的条件用到了子查询的结果

###### Ⅰ标量子查询★

```mysql
#案例1：谁的工资比 Abel 高?
#①查询Abel的工资
SELECT salary
FROM employees
WHERE last_name="Abel"
#②查询员工的信息，满足 salary>①结果
SELECT * 
FROM employees
WHERE salary > (
		SELECT salary
		FROM employees
		WHERE last_name="Abel"
);
#案例2：返回job_id与141号员工相同，salary比143号员工多的员工 姓名，job_id 和工资
#①查询141号员工的job_id
SELECT job_id
FROM employees
WHERE employee_id=141
#②查询143号员工的salary
SELECT salary
FROM employees
WHERE employee_id = 143
#③查询员工的姓名，job_id 和工资，要求job_id=①并且salary>②
SELECT last_name,job_id,salary
FROM employees
WHERE job_id=(
	SELECT job_id
	FROM employees
	WHERE employee_id=141
)
AND salary>(
	SELECT salary
	FROM employees
	WHERE employee_id = 143
);
#案例3：返回公司工资最少的员工的last_name,job_id和salary
#①查询公司的 最低工资
SELECT MIN(salary)
FROM employees
#②查询last_name,job_id和salary，要求salary=①
SELECT last_name,job_id,salary
FROM employees
WHERE salary=(
	SELECT MIN(salary)
	FROM employees
);
-- 报错：Invalid use of group function
-- SELECT last_name,job_id,salary
-- FROM employees
-- WHERE salary=MIN(salary);
#案例4：查询最低工资大于50号部门最低工资的部门id和其最低工资
#①查询50号部门的最低工资
SELECT MIN(salary)
FROM employees
WHERE department_id=50
#②查询每个部门的最低工资
SELECT MIN(salary)
FROM employees
GROUP BY department_id
#③ 在②基础上筛选，满足min(salary)>①
SELECT e.department_id,d.department_name,MIN(salary)
FROM employees e
LEFT JOIN departments d
ON e.department_id = d.department_id
GROUP BY department_id
HAVING MIN(salary) > (
	SELECT MIN(salary)
	FROM employees
	WHERE department_id=50
)
ORDER BY MIN(salary) DESC;
#非法使用标量子查询
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  salary
	FROM employees
	WHERE department_id = 250
);
```

###### Ⅱ列子查询（多行子查询）★

```mysql
#案例1：返回location_id是1400或1700的部门中的所有员工姓名
#①查询location_id是1400或1700的部门编号
SELECT DISTINCT department_id
FROM departments
WHERE location_id IN(1400,1700)
#②查询员工姓名，要求部门号是①列表中的某一个
SELECT last_name
FROM employees 
WHERE department_id IN (
	SELECT DISTINCT department_id
	FROM departments
	WHERE location_id IN(1400,1700)
);
-- 或者
SELECT last_name
FROM employees e 
LEFT JOIN departments d 
ON e.department_id =d.department_id
WHERE d.location_id IN(1400,1700);
#案例2：返回其它工种中比job_id为‘IT_PROG’工种任一工资低的员工的员工号、姓名、job_id 以及salary
#①查询job_id为‘IT_PROG’部门任一工资
SELECT DISTINCT salary
FROM employees
WHERE job_id="IT_PROG"
#②查询员工号、姓名、job_id 以及salary，salary<(①)的任意一个
SELECT employee_id,last_name,job_id,salary
FROM employees
WHERE salary < ALL(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id="IT_PROG"
) AND job_id <> "IT_PROG";
#或
SELECT employee_id,last_name,job_id,salary
FROM employees
WHERE salary < (
	SELECT  MIN(salary)
	FROM employees
	WHERE job_id="IT_PROG"
)AND job_id <> "IT_PROG";
```

###### Ⅲ行子查询（结果集一行多列或多行多列）

```mysql
#案例：查询员工编号最小并且工资最高的员工信息
SELECT *
FROM employees
WHERE (employee_id,salary)=(
		SELECT MIN(employee_id),MAX(salary)
		FROM employees
);
```

#### 4）select后面（仅仅支持标量子查询）

```mysql
#案例：查询每个部门的员工个数
SELECT d.*,(
	SELECT COUNT(*)
	FROM employees e 
	WHERE e.department_id = d.`department_id`
) 个数
FROM departments d;
```

#### 5）from后面（将子查询结果充当一张表，要求必须起别名）

```mysql
#案例：查询每个部门的平均工资的工资等级
#①查询每个部门的平均工资
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id
ORDER BY AVG(salary);
#②连接①的结果集和job_grades表，筛选条件平均工资 between lowest_sal and highest_sal
SELECT avg_dep.*,j.grade_level
FROM (
	SELECT AVG(salary) 平均工资,department_id
	FROM employees
	GROUP BY department_id
	ORDER BY 平均工资
) avg_dep
INNER JOIN job_grades j
ON avg_dep.平均工资 BETWEEN j.lowest_sal AND j.highest_sal;
```

#### 6）exists后面（相关子查询）

###### Ⅰ语法：

exists(完整的查询语句)
结果：
1或0

###### Ⅱ案例：

```mysql
#案例1：查询有员工的部门名
#in
SELECT department_name
FROM departments d 
WHERE d.department_id IN (
	SELECT department_id
	FROM employees
);
#exists
SELECT department_name 
FROM departments d
WHERE EXISTS(
		SELECT *
		FROM employees e
		WHERE e.department_id = d.department_id
);
#案例2：查询没有女朋友的男神信息
#in
USE girls;
SELECT bo.*
FROM boys bo
WHERE bo.id NOT IN(
	SELECT boyfriend_id
	FROM beauty
);
#exists
SELECT bo.*
FROM boys bo
where NOT EXISTS(
	SELECT boyfriend_id
	FROM beauty b
	WHERE  bo.`id`=b.`boyfriend_id`
);
```

#### 7）子查询练习

```mysql
#1.	查询和Zlotkey相同部门的员工姓名和工资
#①查询Zlotkey的部门
SELECT department_id
FROM employees
WHERE last_name = 'Zlotkey'
#②查询部门号=①的姓名和工资
SELECT last_name,salary
FROM employees
WHERE department_id=(
	SELECT department_id
	FROM employees
	WHERE last_name = 'Zlotkey'
);
#2.查询工资比公司平均工资高的员工的员工号，姓名和工资。
#①查询平均工资
SELECT AVG(salary)
FROM employees;
#②查询工资>①的员工号，姓名和工资。
SELECT employee_id,last_name,salary
FROM employees
WHERE salary>(
	SELECT AVG(salary)
	FROM employees
);
#3.查询各部门中工资比本部门平均工资高的员工的员工号, 姓名和工资
#①查询各部门的平均工资
SELECT AVG(salary),department_id
FROM employees e
GROUP BY e.department_id
#②连接①结果集和employees表，进行筛选
SELECT employee_id,last_name,salary
FROM employees e
INNER JOIN (
	SELECT AVG(salary) ag,department_id
	FROM employees e
	GROUP BY e.department_id
) avg_a
ON e.department_id = avg_a.department_id
WHERE e.salary > avg_a.ag;
#4.	查询和姓名中包含字母u的员工在相同部门的员工的员工号和姓名
#①查询姓名中包含字母u的员工的部门
SELECT department_id,last_name
FROM employees 
WHERE last_name LIKE "%u%"
#②查询部门号=①中的任意一个的员工号和姓名
SELECT employee_id,last_name
FROM employees
WHERE employee_id IN(
	SELECT DISTINCT department_id
	FROM employees 
	WHERE last_name LIKE "%u%"
);
#5. 查询在部门的location_id为1700的部门工作的员工的员工号
#①查询location_id为1700的部门
SELECT DISTINCT department_id
FROM departments 
WHERE location_id  = 1700;
#②查询部门号=①中的任意一个的员工号
SELECT employee_id,last_name
FROM employees e
WHERE department_id=ANY(
SELECT DISTINCT department_id
FROM departments 
WHERE location_id  = 1700
);
#6.查询管理者是King的员工姓名和工资
#①查询姓名为king的员工编号
SELECT employee_id
FROM employees
WHERE last_name = "K_ing"
#②查询哪个员工的manager_id = ①
SELECT last_name,salary
FROM employees
WHERE manager_id IN(
SELECT employee_id
FROM employees
WHERE last_name = "K_ing"
);
#7.查询工资最高的员工的姓名，要求first_name和last_name显示为一列，列名为 姓.名
#①查询最高工资
SELECT MAX(salary)
FROM employees;
#②查询工资=①的姓.名
SELECT CONCAT(first_name,last_name) "姓.名"
FROM employees
WHERE salary=(
SELECT MAX(salary)
FROM employees
);
```

### 2.8分页查询 ★

#### 1）语法：

select 查询列表
from 表
【join type join 表2
on 连接条件
where 筛选条件
group by 分组字段
having 分组后的筛选
order by 排序的字段】
limit 【offset,】size;

offset要显示条目的起始索引（起始索引从0开始）
size 要显示的条目个数

#### 2）特点：

①limit语句放在查询语句的最后
②公式
要显示的页数 page，每页的条目数size

select 查询列表
from 表
limit (page-1)*size,size;

page  	size=10

1		   0
2  		10
3		  20

#### 3）案例

```mysql
#案例1：查询前五条员工信息
SELECT * FROM employees LIMIT 0,5;
SELECT * FROM employees LIMIT 5;
#案例2：查询第11条——第25条
SELECT * FROM employees LIMIT 10,15;
#案例3：有奖金的员工信息，并且工资较高的前10名显示出来
SELECT * FROM employees 
WHERE commission_pct IS NOT NULL
ORDER BY salary DESC
LIMIT 0,10;
```

### 2.9联合查询

union 联合 合并：将多条查询语句的结果合并成一个结果

#### 1）语法：

查询语句1
union
查询语句2
union
...

#### 2）应用场景：

要查询的结果来自于多个表，且多个表没有直接的连接关系，但查询的信息一致时

#### 3）特点：★

1、要求多条查询语句的查询列数是一致的！
2、要求多条查询语句的查询的每一列的类型和顺序最好一致
3、union关键字默认去重，如果使用union all 可以包含重复项

#### 4）案例

```mysql
#引入的案例：查询部门编号>90或邮箱包含a的员工信息
SELECT * FROM employees WHERE email LIKE '%a%' OR department_id>90;

SELECT * FROM employees  WHERE email LIKE '%a%'
UNION
SELECT * FROM employees  WHERE department_id>90;

#案例：查询中国用户中男性的信息以及外国用户中年男性的用户信息
SELECT id,cname FROM t_ca WHERE csex='男'
UNION ALL
SELECT t_id,tname FROM t_ua WHERE tGender='male';
```

## 三、DML语言（插入、修改、删除）

### 3.1插入语句

方式一：经典的插入

语法：
insert into 表名(列名,...) values(值1,...);

```mysql
#1.插入的值的类型要与列的类型一致或兼容
-- INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
-- VALUES(13,'唐艺昕','女','1990-4-23','1898888888',NULL,2);
#2.不可以为null的列必须插入值。可以为null的列如何插入值？
#方式一：
INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
VALUES(13,'唐艺昕','女','1990-4-23','1898888888',NULL,2);
```

方式二：

语法：
insert into 表名
set 列名=值,列名=值,...

```mysql
INSERT INTO beauty
SET id=19,NAME='刘涛',phone='999';
```

#两种方式对比
#1、方式一支持插入多行,方式二不支持

#2、方式一支持子查询，方式二不支持

```mysql
INSERT INTO beauty
VALUES(23,'唐艺昕1','女','1990-4-23','1898888888',NULL,2)
,(24,'唐艺昕2','女','1990-4-23','1898888888',NULL,2)
,(25,'唐艺昕3','女','1990-4-23','1898888888',NULL,2);

INSERT INTO beauty(id,NAME,phone)
SELECT 26,'宋茜','11809866';

INSERT INTO beauty(id,NAME,phone)
SELECT id,boyname,'1234567'
FROM boys WHERE id<3;
```

### 3.2修改语句

1.修改单表的记录★

语法：
update 表名
set 列=新值,列=新值,...
where 筛选条件;

2.修改多表的记录【补充】

语法：
sql92语法：
update 表1 别名,表2 别名
set 列=值,...
where 连接条件
and 筛选条件;

sql99语法：
update 表1 别名
inner|left|right join 表2 别名
on 连接条件
set 列=值,...
where 筛选条件;

```mysql
#1.修改单表的记录
#案例1：修改beauty表中姓唐的女神的电话为13899888899
UPDATE beauty SET phone = '13899888899'
WHERE NAME LIKE '唐%';
#案例2：修改boys表中id为2的名称为张飞，魅力值 10
UPDATE boys SET boyname='张飞',usercp=10
WHERE id=2;
#2.修改多表的记录
#案例 1：修改张无忌的女朋友的手机号为114
UPDATE boys bo
INNER JOIN beauty b ON bo.`id`=b.`boyfriend_id`
SET b.`phone`='119',bo.`userCP`=1000
WHERE bo.`boyName`='张无忌';
#案例2：修改没有男朋友的女神的男朋友编号都为2号
UPDATE boys bo
RIGHT JOIN beauty b ON bo.`id`=b.`boyfriend_id`
SET b.`boyfriend_id`=2
WHERE bo.`id` IS NULL;
```

### 3.3删除语句

方式一：delete
语法：

1、单表的删除【★】
delete from 表名 where 筛选条件

2、多表的删除【补充】

sql92语法：
delete 表1的别名,表2的别名
from 表1 别名,表2 别名
where 连接条件
and 筛选条件;

sql99语法：

delete 表1的别名,表2的别名
from 表1 别名
inner|left|right join 表2 别名 on 连接条件
where 筛选条件;

方式二：truncate
语法：truncate table 表名;

```mysql
#方式一：delete
#1.单表的删除
#案例：删除手机号以9结尾的女神信息
DELETE FROM beauty WHERE phone LIKE '%9';
SELECT * FROM beauty;
#2.多表的删除
#案例：删除张无忌的女朋友的信息
DELETE b
FROM beauty b
INNER JOIN boys bo ON b.`boyfriend_id` = bo.`id`
WHERE bo.`boyName`='张无忌';
#案例：删除黄晓明的信息以及他女朋友的信息
DELETE b,bo
FROM beauty b
INNER JOIN boys bo ON b.`boyfriend_id`=bo.`id`
WHERE bo.`boyName`='黄晓明';

#方式二：truncate语句
#案例：将魅力值>100的男神信息删除
TRUNCATE TABLE boys ;
```

#delete 与 truncate【面试题★】

```tex
1.delete 可以加where 条件，truncate不能加
2.truncate删除，效率高一丢丢
3.假如要删除的表中有自增长列，
如果用delete删除后，再插入数据，自增长列的值从断点开始，
而truncate删除后，再插入数据，自增长列的值从1开始。
4.truncate删除没有返回值，delete删除有返回值
5.truncate删除不能回滚，delete删除可以回滚.
```

## 四、建表语句

### 4.1语法

```mysql
CREATE TABLE 表名(
	字段名 字段类型 列级约束,
	字段名 字段类型,
	表级约束
)
```

### 4.2常见约束

#### 1）含义：

一种限制，用于限制表中的数据，为了保证表中的数据的准确和可靠性

#### 2）分类：

六大约束

```tex
NOT NULL：非空，用于保证该字段的值不能为空，比如姓名、学号等
DEFAULT:默认，用于保证该字段有默认值，比如性别
PRIMARY KEY:主键，用于保证该字段的值具有唯一性，并且非空，比如学号、员工编号等
UNIQUE:唯一，用于保证该字段的值具有唯一性，可以为空，比如座位号
CHECK:检查约束【mysql中不支持】，比如年龄、性别
FOREIGN KEY:外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值
	在从表添加外键约束，用于引用主表中某列的值
	比如学生表的专业编号，员工表的部门编号，员工表的工种编号
```

添加约束的时机：
	1.创建表时
	2.修改表时

约束的添加分类：
	列级约束：
		六大约束语法上都支持，但外键约束没有效果		
	表级约束：		
		除了非空、默认，其他的都支持

```mysql
主键和唯一的大对比：
			保证唯一性    是否允许为空        一个表中可以有多少个      是否允许组合
主键	       √			×					至多有1个          	 √，但不推荐
唯一	      √				√					可以有多个         	 √，但不推荐
```

外键：
	1、要求在从表设置外键关系
	2、从表的外键列的类型和主表的关联列的类型要求一致或兼容，名称无要求
	3、主表的关联列必须是一个key（一般是主键或唯一）
	4、插入数据时，先插入主表，再插入从表
	删除数据时，先删除从表，再删除主表

### 4.3创建表时添加约束

#### 1）添加列级约束

语法：

直接在字段名和类型后面追加 约束类型即可。

只支持：默认、非空、主键、唯一

```mysql
CREATE TABLE stuinfo(
		id INT PRIMARY KEY,#主键
		stuName VARCHAR(10) NOT NULL,#名字非空
		seat INT UNIQUE,#座位号唯一
		age INT DEFAULT 18,#年龄默认18
		gender CHAR(1) CHECK(gender = '男'OR gender='女'),#检查性别 CHECK 没作用
		majorId INT REFERENCES major(id)#与专业表id关联
);

CREATE TABLE major(
		id INT PRIMARY KEY, #专业 ID
		majorName VARCHAR(20) #专业名字
);

#查看stuinfo中的所有索引，包括主键、外键、唯一
SHOW INDEX FROM stuinfo;
```

#### 2）添加表级约束

语法：在各个字段的最下面
 【constraint 约束名】 约束类型(字段名) 

```mysql
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo(
		id INT,
		stuName VARCHAR(10),
		seat INT,
		age INT,
		gender CHAR(1),
		majorId INT,
		
		CONSTRAINT pk PRIMARY KEY(id),#主键（mysql会默认给主键取名）
		CONSTRAINT uq UNIQUE(seat),#唯一键
		CONSTRAINT ck CHECK(gender ='男' OR gender  = '女'),#检查
		CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)#外键
);
```

```mysql
#通用的写法：★
CREATE TABLE IF NOT EXISTS stuinfo(
	id INT PRIMARY KEY,
	stuname VARCHAR(20),
	sex CHAR(1),
	age INT DEFAULT 18,
	seat INT UNIQUE,
	majorid INT,
	CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)

);
```

### 4.4修改表时添加约束

```tex
1、添加列级约束
alter table 表名 modify column 字段名 字段类型 新约束;
2、添加表级约束
alter table 表名 add 【constraint 约束名】 约束类型(字段名) 【外键的引用】;
```

```mysql
DROP TABLE IF EXISTS stuinfo;
CREATE TABLE stuinfo(
	id INT,
	stuname VARCHAR(20),
	gender CHAR(1),
	seat INT,
	age INT,
	majorid INT
)
DESC stuinfo;
#1.添加非空约束
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20)  NOT NULL;
#2.添加默认约束
ALTER TABLE stuinfo MODIFY COLUMN age INT DEFAULT 18;
#3.添加主键
#①列级约束
ALTER TABLE stuinfo MODIFY COLUMN id INT PRIMARY KEY;
#②表级约束
ALTER TABLE stuinfo ADD PRIMARY KEY(id);
#4.添加唯一
#①列级约束
ALTER TABLE stuinfo MODIFY COLUMN seat INT UNIQUE;
#②表级约束
ALTER TABLE stuinfo ADD UNIQUE(seat);
#5.添加外键
ALTER TABLE stuinfo ADD CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id); 
```

### 4.5修改表时删除约束

```mysql
#1.删除非空约束
ALTER TABLE stuinfo MODIFY COLUMN stuname VARCHAR(20) NULL;
#2.删除默认约束
ALTER TABLE stuinfo MODIFY COLUMN age INT ;
#3.删除主键
ALTER TABLE stuinfo DROP PRIMARY KEY;
#4.删除唯一
ALTER TABLE stuinfo DROP INDEX seat;
#5.删除外键
ALTER TABLE stuinfo DROP FOREIGN KEY fk_stuinfo_major;
SHOW INDEX FROM stuinfo;
#1.向表emp2的id列中添加PRIMARY KEY约束（my_emp_id_pk）
ALTER TABLE emp2 MODIFY COLUMN id INT PRIMARY KEY;
ALTER TABLE emp2 ADD CONSTRAINT my_emp_id_pk PRIMARY KEY(id);
#2.	向表dept2的id列中添加PRIMARY KEY约束（my_dept_id_pk）
#3.	向表emp2中添加列dept_id，并在其中定义FOREIGN KEY约束，与之相关联的列是dept2表中的id列。
ALTER TABLE emp2 ADD COLUMN dept_id INT;
ALTER TABLE emp2 ADD CONSTRAINT fk_emp2_dept2 FOREIGN KEY(dept_id) REFERENCES dept2(id);
```

对比：

```tex
			位置					支持的约束类型				是否可以起约束名
列级约束：	列的后面			   语法都支持，但外键没有效果		不可以
表级约束：	所有列的下面			  默认和非空不支持，其他支持		可以（主键没有效果）
```

### 4.6标识列

又称为自增长列
含义：可以不用手动的插入值，系统提供默认的序列值
特点：
1、标识列必须和主键搭配吗？不一定，但要求是一个key
2、一个表可以有几个标识列？至多一个！
3、标识列的类型只能是数值型
4、标识列可以通过 SET auto_increment_increment=3;设置步长
可以通过 手动插入值，设置起始值

```mysql
DROP TABLE IF EXISTS tab_identity;
CREATE TABLE tab_identity(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(20) 
);
```

## 五、事务（TCL）

### 5.1定义

一个或一组sql语句组成一个执行单元，这个执行单元要么全部执行，要么全部不执行。

### 5.2特性：ACID

```mysql
原子性：一个事务不可再分割，要么都执行要么都不执行
一致性：一个事务执行会使数据从一个一致状态切换到另外一个一致状态
隔离性：一个事务的执行不受其他事务的干扰
持久性：一个事务一旦提交，则会永久的改变数据库的数据.
```

### 5.3事务的创建

#### 1）隐式事务：

​	  事务没有明显的开启和结束的标记
​	  比如insert、update、delete语句

#### 2）显式事务：

​	事务具有明显的开启和结束的标记，前提：必须先设置自动提交功能为禁用，set autocommit=0;

#### 3）事务的使用：

```mysql
步骤1：开启事务
set autocommit=0;
start transaction;可选的
步骤2：编写事务中的sql语句(select insert update delete)
语句1;
语句2;
...

步骤3：结束事务
commit;提交事务
rollback;回滚事务

savepoint 节点名;设置保存点
```

4）事务的隔离级别：

```mysql
				   脏读		不可重复读			幻读
read uncommitted：	√		   √				√     (读取未提交内容)
read committed：  	×		   √				√		(读取提交内容)
repeatable read： 	×		   ×				√		(可重读)
serializable	  	×          ×                 ×		(可串行化)
```

```tex
mysql中默认 第三个隔离级别 repeatable read
oracle中默认第二个隔离级别 read committed
查看隔离级别（MySQL8中隔离级别的变量跟之前的版本不一样，之前是tx_isolation，MySQL8改成了transaction_isolation。）
select @@transaction_isolation;
设置隔离级别
set session|global transaction isolation level 隔离级别;
```

#### 4）普通事务的使用：

```mysql
#开启事务
SET autocommit = 0;
START TRANSACTION;  #可以省略
#编写一组事务的语句
UPDATE account SET balance=500 WHERE uname="张三";
UPDATE account SET balance=1500 WHERE uname="李四";
#结束事务
#ROLLBACK;  事务提交之后就不能回滚
commit;  #提交事务
```

#### 5）对于delete和truncate的处理的区别

```mysql
#delete过程如果出现错误，事务是可以回滚的，但是truncate操作时是不会造成回滚的
SET autocommit=0;
START TRANSACTION;

DELETE FROM account;
ROLLBACK;
```

#### 6)savepoint 的使用

```mysql
SET autocommit=0;
START TRANSACTION;
DELETE FROM account WHERE id=25;
SAVEPOINT a;#设置保存点
DELETE FROM account WHERE id=28;
ROLLBACK TO a;#回滚到保存点
```

## 六、视图

### 6.1含义

虚拟表，和普通表一样使用，mysql5.1版本出现的新特性，是通过表动态生成的数据

### 6.2表与视图的对比

```mysql
			创建语法的关键字		是否实际占用物理空间					使用
视图		   create view			   只是保存了sql逻辑			增删改查，只是一般不能增删改
表		   create table				保存了数据					增删改查
```

### 6.3视图的创建

语法：

create view 视图名
as
查询语句;

练习：

```mysql
#1.查询姓名中包含a字符的员工名、部门名和工种信息
#①创建
CREATE OR REPLACE VIEW myv1 
AS 
SELECT last_name,department_name,job_title
FROM employees e
JOIN departments d ON e.department_id  = d.department_id
JOIN jobs j ON j.job_id  = e.job_id;
#②使用
SELECT * FROM myv1;
SELECT * FROM myv1 WHERE last_name LIKE 'a%';
#2.查询各部门的平均工资级别
#①创建视图查看每个部门的平均工资
CREATE OR REPLACE VIEW myv2
AS
SELECT AVG(salary) ag,department_id 
FROM employees
GROUP BY department_id
ORDER BY ag DESC;
#②使用
SELECT myv2.ag,j.grade_level
FROM myv2 
INNER JOIN job_grades j
ON myv2.ag BETWEEN j.lowest_sal AND j.highest_sal;
#3.查询平均工资最低的部门信息
SELECT * FROM myv2 ORDER BY ag LIMIT 1;
#4.查询平均工资最低的部门名和工资
CREATE VIEW myv3
AS
SELECT * FROM myv2 ORDER BY ag LIMIT 1;

SELECT d.*,m.ag
FROM myv3 m
INNER  JOIN departments d
ON m.`department_id`=d.`department_id`;
```

### 6.4视图的修改

```mysql
#方式一：
/*
create or replace view  视图名
as
查询语句;
*/
SELECT * FROM myv3 
CREATE OR REPLACE VIEW myv3
AS
SELECT AVG(salary),job_id
FROM employees
GROUP BY job_id;
#方式二：
/*
语法：
alter view 视图名
as 
查询语句;
*/
ALTER VIEW myv3
AS
SELECT * FROM employees;
```

### 6.5视图的删除

语法：drop view 视图名,视图名,...;

```mysql
DROP VIEW emp_v1,emp_v2,myv3;
```

### 6.6查看视图

```mysql
DESC myv3;

SHOW CREATE VIEW myv3;
```

### 6.7视图的更新

```mysql
1.插入
INSERT INTO myv1 VALUES('张飞','zf@qq.com');
#2.修改
UPDATE myv1 SET last_name = '张无忌' WHERE last_name='张飞';
#3.删除
DELETE FROM myv1 WHERE last_name = '张无忌';

#具备以下特点的视图不允许更新
#①包含以下关键字的sql语句：分组函数、distinct、group  by、having、union或者union all
CREATE OR REPLACE VIEW myv1
AS
SELECT MAX(salary) m,department_id
FROM employees
GROUP BY department_id;
SELECT * FROM myv1;
#更新
UPDATE myv1 SET m=9000 WHERE department_id=10;
#②常量视图
CREATE OR REPLACE VIEW myv2
AS
SELECT 'john' NAME;
SELECT * FROM myv2;
#更新
UPDATE myv2 SET NAME='lucy';
#③Select中包含子查询
CREATE OR REPLACE VIEW myv3
AS
SELECT department_id,(SELECT MAX(salary) FROM employees) 最高工资
FROM departments;
#更新
SELECT * FROM myv3;
UPDATE myv3 SET 最高工资=100000;
#④join
CREATE OR REPLACE VIEW myv4
AS
SELECT last_name,department_name
FROM employees e
JOIN departments d
ON e.department_id  = d.department_id;
#更新
SELECT * FROM myv4;
UPDATE myv4 SET last_name  = '张飞' WHERE last_name='Whalen';
INSERT INTO myv4 VALUES('陈真','xxxx');
#⑤from一个不能更新的视图
CREATE OR REPLACE VIEW myv5
AS
SELECT * FROM myv3;
#更新
SELECT * FROM myv5;
UPDATE myv5 SET 最高工资=10000 WHERE department_id=60;
#⑥where子句的子查询引用了from子句中的表
CREATE OR REPLACE VIEW myv6
AS
SELECT last_name,email,salary
FROM employees
WHERE employee_id IN(
	SELECT  manager_id
	FROM employees
	WHERE manager_id IS NOT NULL
);
#更新
SELECT * FROM myv6;
UPDATE myv6 SET salary=10000 WHERE last_name = 'k_ing';
```

## 七、变量

### 7.1分类

```tex
系统变量：
	全局变量
	会话变量
自定义变量：
	用户变量
	局部变量
```

### 7.2系统变量

说明：变量由系统定义，不是用户定义，属于服务器层面
注意：全局变量需要添加global关键字，会话变量需要添加session关键字，如果不写，默认会话级别

使用步骤：

```mysql
1、查看所有系统变量
show global|【session】variables;
2、查看满足条件的部分系统变量
show global|【session】 variables like '%char%';
3、查看指定的系统变量的值
select @@global|【session】系统变量名;
4、为某个系统变量赋值
方式一：
set global|【session】系统变量名=值;
方式二：
set @@global|【session】系统变量名=值;
```

#### 1）全局变量

```mysql
作用域：针对于所有会话（连接）有效，但不能跨重启
#①查看所有全局变量
SHOW GLOBAL VARIABLES;
#②查看满足条件的部分系统变量
SHOW GLOBAL VARIABLES LIKE '%char%';
#③查看指定的系统变量的值
SELECT @@global.autocommit;  #查看自动提交
SELECT @@transaction_isolation;  #查看隔离级别
#④为某个系统变量赋值
SET @@global.autocommit=0;
SET GLOBAL autocommit=0;
```

#### 2)会话变量

```mysql
作用域：针对于当前会话（连接）有效

#①查看所有会话变量
SHOW SESSION VARIABLES;
#②查看满足条件的部分会话变量
SHOW SESSION VARIABLES LIKE '%char%';
#③查看指定的会话变量的值
SELECT @@autocommit;
SELECT @@session.tx_isolation;
#④为某个会话变量赋值
SET @@session.tx_isolation='read-uncommitted';
SET SESSION tx_isolation='read-committed';
```

### 7.3自定义变量

说明：变量由用户自定义，而不是系统提供的
使用步骤：
1、声明
2、赋值
3、使用（查看、比较、运算等）

#### 1）用户变量

```mysql
作用域：针对于当前会话（连接）有效，作用域同于会话变量

#赋值操作符：=或:=
#①声明并初始化
SET @变量名=值;
SET @变量名:=值;
SELECT @变量名:=值;

#②赋值（更新变量的值）
#方式一：
	SET @变量名=值;
	SET @变量名:=值;
	SELECT @变量名:=值;
#方式二：
	SELECT 字段 INTO @变量名
	FROM 表;
#③使用（查看变量的值）
SELECT @变量名;
```

#### 2）局部变量

```mysql
作用域：仅仅在定义它的begin end块中有效
应用在 begin end中的第一句话

#①声明
DECLARE 变量名 类型;
DECLARE 变量名 类型 【DEFAULT 值】;
#②赋值（更新变量的值）
#方式一：
	SET 局部变量名=值;
	SET 局部变量名:=值;
	SELECT 局部变量名:=值;
#方式二：
	SELECT 字段 INTO 具备变量名
	FROM 表;
#③使用（查看变量的值）
SELECT 局部变量名;

#案例：声明两个变量，求和并打印
#用户变量
SET @m=1;
SET @n=1;
SET @sum=@m+@n;
SELECT @sum;

#局部变量
DECLARE m INT DEFAULT 1;
DECLARE n INT DEFAULT 1;
DECLARE SUM INT;
SET SUM=m+n;
SELECT SUM;
```

#### 3）对比

```mysql
			作用域						定义位置						语法
用户变量	当前会话				  会话的任何地方				加@符号，不用指定类型
局部变量	定义它的BEGIN END中 	     BEGIN END的第一句话			一般不用加@,需要指定类型
```

## 八、存储过程和函数

存储过程和函数：类似于java中的方法
好处：
1、提高代码的重用性
2、简化操作

### 8.1存储过程

#### 1）含义：

一组预先编译好的SQL语句的集合，理解成批处理语句
1、提高代码的重用性
2、简化操作
3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

#### 2）创建

```mssql
CREATE PROCEDURE 存储过程名(参数列表)
BEGIN
	存储过程体（一组合法的SQL语句）
END

#注意：
1、参数列表包含三部分
参数模式  参数名  参数类型
举例：
in stuname varchar(20)
参数模式：
in：该参数可以作为输入，也就是该参数需要调用方传入值
out：该参数可以作为输出，也就是该参数可以作为返回值
inout：该参数既可以作为输入又可以作为输出，也就是该参数既需要传入值，又可以返回值
2、如果存储过程体仅仅只有一句话，begin end可以省略
存储过程体中的每条sql语句的结尾要求必须加分号。
存储过程的结尾可以使用 delimiter 重新设置
语法：
delimiter 结束标记
案例：
delimiter $
```

#### 3）调用

```mysql
CALL 存储过程名(实参列表);
```

#### 4）案例

```mysql
#1.空参列表
#案例：插入到admin表中五条记录

SELECT * FROM admin;
-- DROP TABLE IF EXISTS admin;
-- CREATE TABLE  admin(
-- 	id INT PRIMARY KEY AUTO_INCREMENT,
-- 	username VARCHAR(20),
-- 	password VARCHAR(20)
-- );
-- 
-- SELECT * FROM admin;
DELIMITER $
CREATE PROCEDURE myp2()
BEGIN
	INSERT INTO admin(username,password) 
	VALUES('john1','0000'),('lily','0000'),('rose','0000'),('jack','0000'),('tom','0000');
END $
#调用
CALL myp2()$
#2.创建带in模式参数的存储过程
#案例1：创建存储过程实现 根据女神名，查询对应的男神信息
CREATE PROCEDURE myp2(IN beautyName VARCHAR(20))
BEGIN
	SELECT bo.*
	FROM boys bo
	RIGHT JOIN beauty b ON bo.id = b.boyfriend_id
	WHERE b.name=beautyName;
END $
#调用
CALL myp2('柳岩')$
#案例2 ：创建存储过程实现，用户是否登录成功
CREATE PROCEDURE myp4(IN username VARCHAR(20),IN PASSWORD VARCHAR(20))
BEGIN
	DECLARE result INT DEFAULT 0;#声明并初始化变量
	
	SELECT COUNT(*) INTO result#赋值
	FROM admin
	WHERE admin.username = username
	AND admin.password = PASSWORD;
	
	SELECT IF(result>0,'成功','失败');#使用
END $
#调用
CALL myp4('张飞','8888')$
#3.创建out 模式参数的存储过程
#案例1：根据输入的女神名，返回对应的男神名
CREATE PROCEDURE myp6(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20))
BEGIN
	SELECT bo.boyname INTO boyname
	FROM boys bo
	RIGHT JOIN
	beauty b ON b.boyfriend_id = bo.id
	WHERE b.name=beautyName ;
	
END $
#案例2：根据输入的女神名，返回对应的男神名和魅力值
CREATE PROCEDURE myp7(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20),OUT usercp INT) 
BEGIN
	SELECT boys.boyname ,boys.usercp INTO boyname,usercp
	FROM boys 
	RIGHT JOIN
	beauty b ON b.boyfriend_id = boys.id
	WHERE b.name=beautyName ;	
END $
#调用
CALL myp7('小昭',@name,@cp)$
SELECT @name,@cp$
#4.创建带inout模式参数的存储过程
#案例1：传入a和b两个值，最终a和b都翻倍并返回
CREATE PROCEDURE myp8(INOUT a INT ,INOUT b INT)
BEGIN
	SET a=a*2;
	SET b=b*2;
END $
#调用
SET @m=10$
SET @n=20$
CALL myp8(@m,@n)$
SELECT @m,@n$
```

#### 5）删除存储过程

```mysql
#语法：drop procedure 存储过程名
DROP PROCEDURE p1;
DROP PROCEDURE p2,p3;#×（只能删除一个）
```

#### 6）查看存储过程的信息

```mysql
DESC myp2;×
SHOW CREATE PROCEDURE  myp2;
```

### 8.2函数

#### 1）含义：

一组预先编译好的SQL语句的集合，理解成批处理语句
1、提高代码的重用性
2、简化操作
3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

#### 2）区别：

存储过程：可以有0个返回，也可以有多个返回，适合做批量插入、批量更新
函数：有且仅有1 个返回，适合做处理数据后返回一个结果

#### 3）创建

```mysql
CREATE FUNCTION 函数名(参数列表) RETURNS 返回类型
BEGIN
	函数体
END

注意：
1.参数列表 包含两部分：
参数名 参数类型

2.函数体：肯定会有return语句，如果没有会报错
如果return语句没有放在函数体的最后也不报错，但不建议
return 值;
3.函数体中仅有一句话，则可以省略begin end
4.使用 delimiter语句设置结束标记
```

#### 4）调用语法

```mysql
SELECT 函数名(参数列表)
```

#### 5）案例

```mysql
#1.无参有返回
#案例：返回公司的员工个数
CREATE FUNCTION myf1() RETURNS INT
BEGIN

	DECLARE c INT DEFAULT 0;#定义局部变量
	SELECT COUNT(*) INTO c#赋值
	FROM employees;
	RETURN c;
	
END $
SELECT myf1()$
#2.有参有返回
#案例1：根据员工名，返回它的工资
CREATE FUNCTION myf2(empName VARCHAR(20)) RETURNS DOUBLE
BEGIN
	SET @sal=0;#定义用户变量 
	SELECT salary INTO @sal   #赋值
	FROM employees
	WHERE last_name = empName;
	
	RETURN @sal;
END $
SELECT myf2('k_ing') $
#案例2：根据部门名，返回该部门的平均工资
CREATE FUNCTION myf3(deptName VARCHAR(20)) RETURNS DOUBLE
BEGIN
	DECLARE sal DOUBLE ;
	SELECT AVG(salary) INTO sal
	FROM employees e
	JOIN departments d ON e.department_id = d.department_id
	WHERE d.department_name=deptName;
	RETURN sal;
END $
SELECT myf3('IT')$
```

#### 6）查看函数

```mysql
SHOW CREATE FUNCTION myf3;
```

#### 7）删除函数

```mysql
DROP FUNCTION myf3;
```

#### 8）经典案例

```mysql
#案例
#一、创建函数，实现传入两个float，返回二者之和
CREATE FUNCTION test_fun1(num1 FLOAT,num2 FLOAT) RETURNS FLOAT
BEGIN
	DECLARE SUM1 FLOAT DEFAULT 0;
	SET SUM1=num1+num2;
	RETURN SUM1;
END $

SELECT test_fun1(1,2)$
```

## 九、流程控制结构

### 9.1分类：

顺序、分支、循环

### 9.2分支结构

#### 1）if函数

```mysql
语法：if(条件,值1，值2)
功能：实现双分支
应用在begin end中或外面
```

#### 2）case结构

```mysql
语法：
情况1：类似于switch
case 变量或表达式
when 值1 then 语句1;
when 值2 then 语句2;
...
else 语句n;
end 

情况2：
case 
when 条件1 then 语句1;
when 条件2 then 语句2;
...
else 语句n;
end 

应用在begin end 中或外面
```

#### 3）if结构

```mysql
语法：
if 条件1 then 语句1;
elseif 条件2 then 语句2;
....
else 语句n;
end if;
功能：类似于多重if

只能应用在begin end 中
```

4）案例

```mysql
#案例1：创建函数，实现传入成绩，如果成绩>90,返回A，如果成绩>80,返回B，如果成绩>60,返回C，否则返回D
CREATE FUNCTION test_if(score FLOAT) RETURNS CHAR
BEGIN
	DECLARE ch CHAR DEFAULT 'A';
	IF score>90 THEN SET ch='A';
	ELSEIF score>80 THEN SET ch='B';
	ELSEIF score>60 THEN SET ch='C';
	ELSE SET ch='D';
	END IF;
	RETURN ch;	
END $

SELECT test_if(87)$

#案例2：创建存储过程，如果工资<2000,则删除，如果5000>工资>2000,则涨工资1000，否则涨工资500
CREATE PROCEDURE test_if_pro(IN sal DOUBLE)
BEGIN
	IF sal<2000 THEN DELETE FROM employees WHERE employees.salary=sal;
	ELSEIF sal>=2000 AND sal<5000 THEN UPDATE employees SET salary=salary+1000 WHERE employees.`salary`=sal;
	ELSE UPDATE employees SET salary=salary+500 WHERE employees.`salary`=sal;
	END IF;
END $

CALL test_if_pro(2100)$

#案例1：创建函数，实现传入成绩，如果成绩>90,返回A，如果成绩>80,返回B，如果成绩>60,返回C，否则返回D
CREATE FUNCTION test_case(score FLOAT) RETURNS CHAR
BEGIN 
	DECLARE ch CHAR DEFAULT 'A';
	CASE 
	WHEN score>90 THEN SET ch='A';
	WHEN score>80 THEN SET ch='B';
	WHEN score>60 THEN SET ch='C';
	ELSE SET ch='D';
	END CASE;	
	RETURN ch;
END $

SELECT test_case(56)$
```

### 9.3循环结构

#### 1）分类：

while、loop、repeat

循环控制：
iterate类似于 continue，继续，结束本次循环，继续下一次
leave 类似于  break，跳出，结束当前所在的循环

#### 2）while

```mysql
语法：
【标签:】while 循环条件 do
	循环体;
end while【 标签】;

联想：
while(循环条件){
	循环体;
}
```

#### 3）loop

```mysql
语法：
【标签:】loop
	循环体;
end loop 【标签】;
可以用来模拟简单的死循环
```

#### 4）repeat

```mysql
语法：
【标签：】repeat
	循环体;
until 结束循环的条件
end repeat 【标签】;
```

5）案例

```mysql
#1.没有添加循环控制语句
#案例：批量插入，根据次数插入到admin表中多条记录
DROP PROCEDURE pro_while1$
CREATE PROCEDURE pro_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 1;
	WHILE i<=insertCount DO
		INSERT INTO admin(username,`password`) VALUES(CONCAT('Rose',i),'666');
		SET i=i+1;
	END WHILE;	
END $

CALL pro_while1(100)$

/*

int i=1;
while(i<=insertcount){
	//插入	
	i++;
}
*/

#2.添加leave语句

#案例：批量插入，根据次数插入到admin表中多条记录，如果次数>20则停止
TRUNCATE TABLE admin$
DROP PROCEDURE test_while1$
CREATE PROCEDURE test_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 1;
	a:WHILE i<=insertCount DO
		INSERT INTO admin(username,`password`) VALUES(CONCAT('xiaohua',i),'0000');
		IF i>=20 THEN LEAVE a;
		END IF;
		SET i=i+1;
	END WHILE a;
END $

CALL test_while1(100)$

#3.添加iterate语句
#案例：批量插入，根据次数插入到admin表中多条记录，只插入偶数次
TRUNCATE TABLE admin$
DROP PROCEDURE test_while1$
CREATE PROCEDURE test_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 0;
	a:WHILE i<=insertCount DO
		SET i=i+1;
		IF MOD(i,2)!=0 THEN ITERATE a;
		END IF;
		
		INSERT INTO admin(username,`password`) VALUES(CONCAT('xiaohua',i),'0000');
		
	END WHILE a;
END $

CALL test_while1(100)$

/*
int i=0;
while(i<=insertCount){
	i++;
	if(i%2==0){
		continue;
	}
	插入
}
*/
```


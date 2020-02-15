---
layout: post
title: SQL实战3--数据处理进阶
categories: Database
description: SQL练习题--数据处理进阶
keywords: SQL, Database
---

# 批量插入数据
## 题目描述
对于表actor批量插入如下数据
```
CREATE TABLE IF NOT EXISTS actor (
actor_id smallint(5) NOT NULL PRIMARY KEY,
first_name varchar(45) NOT NULL,
last_name varchar(45) NOT NULL,
last_update timestamp NOT NULL DEFAULT (datetime('now','localtime')))
```

| actor_id  |  first_name | last_name  | last_update |
| ---- | ---- | ---- | ---- |
| 1  | PENELOPE |   GUINESS |  2006-02-15 12:34:33 |
| 2 |  NICK  |  WAHLBERG   |  2006-02-15 12:34:33 |

## 答案
```
insert into actor values (1, 'PENELOPE', 'GUINESS', '2006-02-15 12:34:33'),
                         (2, 'NICK', 'WAHLBERG', '2006-02-15 12:34:33')

-- UNION SELECT
insert into actor
select 1, 'PENELOPE', 'GUINESS', '2006-02-15 12:34:33'
union select 2,'NICK','WAHLBERG','2006-02-15 12:34:33'
```

# 批量插入数据，不使用replace操作
## 题目描述
对于表actor批量插入如下数据,如果数据已经存在，请忽略，不使用replace操作
```
CREATE TABLE IF NOT EXISTS actor (
actor_id smallint(5) NOT NULL PRIMARY KEY,
first_name varchar(45) NOT NULL,
last_name varchar(45) NOT NULL,
last_update timestamp NOT NULL DEFAULT (datetime('now','localtime')))
```

|actor_id  |  first_name | last_name |  last_update|
| ---- | ---- | ---- | ---- |
|'3'| 'ED' |   'CHASE'| '2006-02-15 12:34:33'|

## 答案
```
-- SQLITE
insert or ignore into actor
values(3,'ED','CHASE','2006-02-15 12:34:33');
-- MySQL
insert IGNORE into actor
values(3,'ED','CHASE','2006-02-15 12:34:33');
```
## 笔记
>如果不存在则插入，如果存在则忽略<br>
>`INSERT OR IGNORE INTO tablename VALUES(...);`<br>
>如果不存在则插入，如果存在则替换<br>
>`INSERT OR REPLACE INTO tablename VALUES(...);`<br>
>这里指的存在表示的是unique属性的列值存在的情况下，unique表示键值唯一

# 删除emp_no重复的记录，只保留最小的id对应的记录
## 题目描述
删除emp_no重复的记录，只保留最小的id对应的记录。
```
CREATE TABLE IF NOT EXISTS titles_test (
id int(11) not null primary key,
emp_no int(11) NOT NULL,
title varchar(50) NOT NULL,
from_date date NOT NULL,
to_date date DEFAULT NULL);

insert into titles_test values
('1', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('2', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('3', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('4', '10004', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('5', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('6', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('7', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01');
```
## 答案
```
delete from titles_test where id not in
(select min(id) from titles_test group by emp_no)
```

# 将所有to_date为9999-01-01的全部更新为NULL,且from_date更新为2001-01-01
## 题目描述
将所有to_date为9999-01-01的全部更新为NULL,且from_date更新为2001-01-01。
```
CREATE TABLE IF NOT EXISTS titles_test (
id int(11) not null primary key,
emp_no int(11) NOT NULL,
title varchar(50) NOT NULL,
from_date date NOT NULL,
to_date date DEFAULT NULL);

insert into titles_test values ('1', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('2', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('3', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('4', '10004', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('5', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('6', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('7', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01');
```
## 答案
```
update titles_test set to_date = null, from_date = '2001-01-01'
where to_date = '9999-01-01'
-- set 字段名1=字段值1,字段名2=字段值2,用逗号","分隔,不是用空格分隔
```

# 将id=5以及emp_no=10001的行数据替换成id=5以及emp_no=10005
## 题目描述
将id=5以及emp_no=10001的行数据替换成id=5以及emp_no=10005,其他数据保持不变，使用replace实现。
```
CREATE TABLE IF NOT EXISTS titles_test (
id int(11) not null primary key,
emp_no int(11) NOT NULL,
title varchar(50) NOT NULL,
from_date date NOT NULL,
to_date date DEFAULT NULL);

insert into titles_test values ('1', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('2', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('3', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('4', '10004', 'Senior Engineer', '1995-12-03', '9999-01-01'),
('5', '10001', 'Senior Engineer', '1986-06-26', '9999-01-01'),
('6', '10002', 'Staff', '1996-08-03', '9999-01-01'),
('7', '10003', 'Senior Engineer', '1995-12-03', '9999-01-01');
```
## 答案
```
replace into titles_test 
values (5, 10005, 'Senior Engineer', '1986-06-26', '9999-01-01')
```
## 笔记
全字段更新替换。由于 REPLACE 的新记录中 id=5，与表中的主键 id=5 冲突，故会替换掉表中 id=5 的记录，否则会插入一条新记录（例如新插入的记录 id = 10）。并且要将所有字段的值写出，否则将置为空。可参考：
http://blog.csdn.net/zhangjg_blog/article/details/23267761

# 获取emp_v和employees相同的数据
## 题目描述
存在如下的视图：

`create view emp_v as select * from employees where emp_no >10005;`

如何获取emp_v和employees有相同的数据？
```
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```
## 答案
```
-- intersect求交集
select * from employees intersect select * from emp_v

select v.* from emp_v v, employees e where v.emp_no = e.emp_no
```

# 将所有获取奖金的员工当前的薪水增加10%
## 题目描述
将所有获取奖金的员工当前的薪水增加10%。
```
create table emp_bonus(
emp_no int not null,
recevied datetime not null,
btype smallint not null);

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL, PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
UPDATE salaries SET salary = salary * 1.1 WHERE emp_no IN
(SELECT s.emp_no FROM salaries AS s INNER JOIN emp_bonus AS eb 
ON s.emp_no = eb.emp_no AND s.to_date = '9999-01-01')

-- oj bug
update salaries set salary = salary * 1.1 
where emp_no in (select emp_no from emp_bonus)
```

# 针对库中的所有表生成select count(*)对应的SQL语句
## 题目描述
针对库中的所有表生成select count(*)对应的SQL语句
## 答案
```
-- sqlite3
select "select count(*) from " || name || ";" as cnts
from sqlite_master where type = 'table'

-- MySQL
select concat("select count(*) from "," ",table_name,";") as cnts
from (select table_name from information_schema.tables) as new;
```

# 将employees表中的所有员工的last_name和first_name通过(')连接起来
## 题目描述
将employees表中的所有员工的last_name和first_name通过(')连接起来。
```
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```
## 答案
```
-- sqlite3
select last_name || "'" || first_name from employees;
-- MySQL
select concat(last_name, "'", first_name) from employees;
```

# 查找字符串'10,A,B' 中逗号','出现的次数cnt
## 题目描述
查找字符串'10,A,B' 中逗号','出现的次数cnt。
## 答案
```
-- 用replace()函数把  '10,A,B'  中的  ','  替换为  ' '  空
select length('10,A,B')-length(replace('10,A,B', ',', ''))
```

# 获取Employees中的first_name，查询按照first_name最后两个字母，按照升序进行排列
## 题目描述
获取Employees中的first_name，查询按照first_name最后两个字母，按照升序进行排列
```
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```
## 答案
```
select first_name from employees order by substr(first_name,length(first_name)-1)

select first_name from employees order by substr(first_name,-2)
```
## 笔记
`substr(X,Y,Z)` 或 `substr(X,Y)` 函数的使用：
>X是要截取的字符串。Y是字符串的起始位置（注意第一个字符的位置为1，而不为0），取值范围是±(1~length(X))，当Y等于length(X)时，则截取最后一个字符；当Y等于负整数-n时，则从倒数第n个字符处截取。Z是要截取字符串的长度，取值范围是正整数，若Z省略，则从Y处一直截取到字符串末尾；若Z大于剩下的字符串长度，也是截取到字符串末尾为止。

# 按照dept_no进行汇总，属于同一个部门的emp_no按照逗号进行连接，结果给出dept_no以及连接出的结果employees
## 题目描述
按照dept_no进行汇总，属于同一个部门的emp_no按照逗号进行连接，结果给出dept_no以及连接出的结果employees
```
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
```
## 答案
```
-- sqlite3
select dept_no, group_concat(emp_no) as employees
from dept_emp group by dept_no

-- MySQL
select dept_no,group_concat(emp_no SEPARATOR ',') as employees
from dept_emp group by dept_no;
```
## 笔记
SQLite的聚合函数group_concat(X,Y)，其中X是要连接的字段，Y是连接时用的符号，可省略，默认为逗号。此函数必须与 GROUP BY 配合使用

# 查找排除当前最大、最小salary之后的员工的平均工资avg_salary
## 题目描述
查找排除当前最大、最小salary之后的员工的平均工资avg_salary。
```
CREATE TABLE `salaries` ( `emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
select avg(salary) as avg_salary from salaries
where salary not in 
    (select max(salary) from salaries where to_date = '9999-01-01')
and (select min(salary) from salaries where to_date = '9999-01-01')
and to_date = '9999-01-01';
```

# 分页查询employees表，每5行一页，返回第2页的数据
## 题目描述
分页查询employees表，每5行一页，返回第2页的数据
```
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```
## 答案
```
--  limit  x, y： x:表示偏移量， y:表示获取的数据数量
select * from employees limit 5,5
```

# 获取所有员工的emp_no、部门编号dept_no以及对应的bonus类型btype和received ，没有分配具体的员工不显示
## 题目描述
获取所有员工的emp_no、部门编号dept_no以及对应的bonus类型btype和received ，没有分配具体的员工不显示
```
CREATE TABLE `dept_emp` ( 
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

create table emp_bonus(
emp_no int not null,
received datetime not null,
btype smallint not null);
```
## 答案
```
select e.emp_no, de.dept_no, b.btype, b.received from
employees e left join emp_bonus b on e.emp_no = b.emp_no 
join dept_emp de on e.emp_no = de.emp_no
```

# 使用含有关键字exists查找未分配具体部门的员工的所有信息
## 题目描述
使用含有关键字exists查找未分配具体部门的员工的所有信息。
```
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
```
## 答案
```
select e.* from employees e where not exists 
(select emp_no from dept_emp where emp_no = e.emp_no)

SELECT * FROM employees WHERE emp_no NOT IN 
(SELECT emp_no FROM dept_emp)
```
## 笔记
IN 语句：只执行一次
>确定给定的值是否与子查询或列表中的值相匹配。in在查询的时候，首先查询子查询的表，然后将内表和外表做一个笛卡尔积，然后按照条件进行筛选。所以相对内表比较小的时候，in的速度较快。

EXISTS语句：执行[外表条数]次
>指定一个子查询，检测行的存在。遍历循环外表，然后看外表中的记录有没有和内表的数据一样的。匹配上就将结果放入结果集中。<br>
exists强调的是是否返回结果集，不要求知道返回什么, 比如：`select name from student where sex = 'm' and mark exists(select 1 from grade where ...)`,只要exists引导的子句有结果集返回，那么exists这个条件就算成立了,大家注意返回的字段始终为1，如果改成“select 2 from grade where ...”，那么返回的字段就是2，这个数字没有意义。所以exists子句不在乎返回什么，而是在乎是不是有结果集返回。exists与in最大的区别在于in引导的子句只能返回一个字段，比如：`select name from student where sex = 'm' and mark in (select 1,2,3 from grade where ...) `,in子句返回了三个字段，这是不正确的，exists子句是允许的，但in只允许有一个字段返回，在1，2，3中随便去了两个字段即可。

# 获取有奖金的员工相关信息
## 题目描述
获取有奖金的员工相关信息。
```
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

create table emp_bonus(
emp_no int not null,
recevied datetime not null,
btype smallint not null);

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL, PRIMARY KEY (`emp_no`,`from_date`));
```
给出emp_no、first_name、last_name、奖金类型btype、对应的当前薪水情况salary以及奖金金额bonus。 bonus类型btype为1其奖金为薪水salary的10%，btype为2其奖金为薪水的20%，其他类型均为薪水的30%。 当前薪水表示to_date='9999-01-01'
## 答案
```
-- 运用四则运算解出：（注意要除以10.0，如果除以10的话，结果的小数位会被舍去）
select e.emp_no, e.first_name, e.last_name, b.btype, s.salary, (s.salary*b.btype/10.0) as bonus
from employees e join emp_bonus b on e.emp_no = b.emp_no 
join salaries s on e.emp_no = s.emp_no 
where s.to_date='9999-01-01'

-- case
SELECT e.emp_no, e.first_name, e.last_name, b.btype, s.salary, 
(CASE b.btype 
 WHEN 1 THEN s.salary * 0.1
 WHEN 2 THEN s.salary * 0.2
 ELSE s.salary * 0.3 END) AS bonus
FROM employees AS e INNER JOIN emp_bonus AS b ON e.emp_no = b.emp_no
INNER JOIN salaries AS s ON e.emp_no = s.emp_no AND s.to_date = '9999-01-01'
```

# 统计salary的累计和running_total
## 题目描述
统计salary的累计和running_total，其中running_total为前几个员工的salary累计和，其他以此类推。 具体结果如下Demo展示。
```
CREATE TABLE `salaries` ( `emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
select s1.emp_no, s1.salary, 
(select sum(s2.salary) from salaries s2 
 where s2.emp_no <= s1.emp_no and s2.to_date = '9999-01-01') as running_total
from salaries s1 where s1.to_date = '9999-01-01'

-- 窗口函数
select emp_no,salary,
sum(salary) over(order by emp_no) as running_total
from salaries
where to_date= '9999-01-01';
```

# 对于employees表中，给出奇数行的first_name
## 题目描述
对于employees表中，给出奇数行的first_name
```
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```
## 答案
```
select e1.first_name from employees e1 where
(select count(*) from employees e2
where e1.first_name <=e2.first_name)%2=1;
```



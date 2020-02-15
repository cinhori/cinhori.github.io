---
layout: post
title: SQL实战1--数据查询
categories: Database
description: SQL练习题-查询
keywords: SQL, Database
---

# 查找最晚入职员工的所有信息
## 题目描述
查找最晚入职员工的所有信息
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
```SQL
-- 查找多条
select t.* from employees t where t.hire_date = (select max(hire_date) from employees);

-- 查找一条
select t.* from employees t order by t.hire_date desc limit 1;
```

# 查找入职员工时间排名倒数第三的员工所有信息
## 题目描述
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
```SQL
--未考虑重复问题
select * from employees order by hire_time desc limit 2,1

-- 同一天入职的按一个计算
select * from employees where hire_date = (
    select distinct hire_date from employees order by hire_date desc limit 2,1
)
```

`LIMIT m,n` : 表示从第m+1条开始，取n条数据；
`LIMIT n` ： 表示从第0条开始，取n条数据，是limit(0,n)的缩写。

# 查找当前薪水详情以及其对应部门编号dept_no
## 题目描述
查找各个部门当前(to_date='9999-01-01')领导当前薪水详情以及其对应部门编号dept_no
```
CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL,
`emp_no` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```SQL
select salaries.*,dept_manager.dept_no 
from salaries inner join dept_manager on dept_manager.emp_no = salaries.emp_no where dept_manager.to_date = '9999-01-01' and salaries.to_date = '9999-01-01';
```
题目要求是薪水情况以及部门编号，再结合输出情况dept_no 被放到了最后一列，可见主表是“salaries”。这里顺序错了就会提示：您的代码无法通过所有用例。

## 笔记
* （inner）JOIN: 如果表中有至少一个匹配，则返回行
* LEFT JOIN: 即使右表中没有匹配，也从左表返回所有的行
* RIGHT JOIN: 即使左表中没有匹配，也从右表返回所有的行
* FULL JOIN: 只要其中一个表中存在匹配，就返回行

# 查找所有已经分配部门的员工的last_name和first_name
## 题目描述
查找所有已经分配部门的员工的last_name和first_name
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
```
## 答案
```
select employees.last_name, employees.first_name, dept_emp.dept_no 
from employees inner join dept_emp on employees.emp_no = dept_emp.emp_no;
```

# 查找所有员工的last_name和first_name以及对应部门编号dept_no，也包括展示没有分配具体部门的员工
## 题目描述
查找所有员工的last_name和first_name以及对应部门编号dept_no，也包括展示没有分配具体部门的员工
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
```
## 答案
```
select employees.last_name, employees.first_name, dept_emp.dept_no 
from employees left join dept_emp on employees.emp_no = dept_emp.emp_no;
```

# 查找所有员工入职时候的薪水情况，给出emp_no以及salary， 并按照emp_no进行逆序
## 题目描述
查找所有员工入职时候的薪水情况，给出emp_no以及salary， 并按照emp_no进行逆序
```
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
-- 内连接
SELECT e.emp_no, s.salary FROM employees e INNER JOIN salaries s
ON e.emp_no = s.emp_no AND e.hire_date = s.from_date
ORDER BY e.emp_no DESC

--并列查询
SELECT e.emp_no, s.salary FROM employees AS e, salaries AS s
WHERE e.emp_no = s.emp_no AND e.hire_date = s.from_date
ORDER BY e.emp_no DESC
```
## 笔记
内连接是取左右两张表的交集形成一个新表，用FROM并列两张表后仍然还是两张表。如果还要对新表进行操作则要用内连接。从效率上看应该FROM并列查询比较快，因为不用形成新表。

# 查找薪水涨幅超过15次的员工号emp_no以及其对应的涨幅次数t
## 题目描述
查找薪水涨幅超过15次的员工号emp_no以及其对应的涨幅次数t
```
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
SELECT emp_no,count(emp_no) as t from salaries group by emp_no having t>15;
```

# 找出所有员工当前具体的薪水salary情况
## 题目描述
找出所有员工当前(to_date='9999-01-01')具体的薪水salary情况，对于相同的薪水只显示一次,并按照逆序显示
```
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
select salary from salaries where to_date='9999-01-01' group by salary order by salary desc

select distinct salary from salaries where to_date = '9999-01-01' order by salary desc
```
## 笔记
对于distinct与group by的使用: 
1. 当对系统的性能高并数据量大时使用group by 
2. 当对系统的性能不高时使用数据量少时两者皆可 
3. 尽量使用group by

# 获取所有部门当前manager的当前薪水情况
## 题目描述
获取所有部门当前manager的当前薪水情况，给出dept_no, emp_no以及salary，当前表示to_date='9999-01-01'
```
CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL,
`emp_no` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
select m.dept_no dept_no, m.emp_no emp_no, s.salary salary from dept_manager m inner join salaries s on m.emp_no = s.emp_no where m.to_date = '9999-01-01' and s.to_date = '9999-01-01'
```

# 获取所有非manager的员工emp_no
## 题目描述
获取所有非manager的员工emp_no
```
CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL,
`emp_no` int(11) NOT NULL,
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
```
## 答案
```
-- 使用NOT IN选出在employees但不在dept_manager中的emp_no记录
select e.emp_no from employees e where e.emp_no not in (select m.emp_no from dept_manager m)

-- 先使用LEFT JOIN连接两张表，再从此表中选出dept_no值为NULL对应的emp_no记录
SELECT emp_no FROM (SELECT * FROM employees LEFT JOIN dept_manager
ON employees.emp_no = dept_manager.emp_no)
WHERE dept_no IS NULL

SELECT employees.emp_no FROM employees LEFT JOIN dept_manager
ON employees.emp_no = dept_manager.emp_no
WHERE dept_no IS NULL
```

# 获取所有员工当前的manager
## 题目描述
获取所有员工当前的manager，如果当前的manager是自己的话结果不显示，当前表示to_date='9999-01-01'。
结果第一列给出当前员工的emp_no,第二列给出其manager对应的manager_no。
```
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL,
`emp_no` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
```
## 答案
```
select e.emp_no as emp_no, m.emp_no as manager_no from dept_emp e inner join dept_manager m on e.dept_no = m.dept_no where e.emp_no != m.emp_no and m.to_date='9999-01-01'; 
```

# 获取所有部门中当前员工薪水最高的相关信息
## 题目描述
获取所有部门中当前员工薪水最高的相关信息，给出dept_no, emp_no以及其对应的salary
```
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
--错误答案
SELECT d.dept_no, s.emp_no, MAX(s.salary) AS salary FROM salaries AS s 
INNER JOIN dept_emp As d ON d.emp_no = s.emp_no 
WHERE d.to_date = '9999-01-01' AND s.to_date = '9999-01-01'
GROUP BY d.dept_no

--正确答案：链接：https://www.nowcoder.com/
SELECT dept_no,emp_no,salary from(
SELECT D.dept_no AS dept_no,S.emp_no AS emp_no,S.salary AS salary from salaries AS S JOIN dept_emp as D on S.emp_no=D.emp_no
where D.to_date='9999-01-01' and S.to_date='9999-01-01' 
ORDER BY salary DESC
) as b
GROUP BY dept_no
```

# 从titles表获取按照title进行分组
## 题目描述
从titles表获取按照title进行分组，每组个数大于等于2，给出title以及对应的数目t。
```
CREATE TABLE IF NOT EXISTS "titles" (
`emp_no` int(11) NOT NULL,
`title` varchar(50) NOT NULL,
`from_date` date NOT NULL,
`to_date` date DEFAULT NULL);
```
## 答案
```
select title, count(emp_no) t from titles group by title having count(title) >= 2;
```

# 从titles表获取按照title进行分组,注意对于重复的emp_no进行忽略。
## 题目描述
从titles表获取按照title进行分组，每组个数大于等于2，给出title以及对应的数目t。
注意对于重复的emp_no进行忽略。
```
CREATE TABLE IF NOT EXISTS `titles` (
`emp_no` int(11) NOT NULL,
`title` varchar(50) NOT NULL,
`from_date` date NOT NULL,
`to_date` date DEFAULT NULL);
```
## 答案
```
select title, count(distinct emp_no) as t from titles group by title having t >= 2
```
## 笔记
where和having的不同之处在于，**where是查找之前的限定，而having是查找之后**。

# 查找employees表
## 题目描述
查找employees表所有emp_no为奇数，且last_name不为Mary的员工信息，并按照hire_date逆序排列
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
select * from employees where emp_no % 2 != 0 and last_name != 'Mary' order by hire_date desc
```

# 统计出当前各个title类型对应的员工当前薪水对应的平均工资
## 题目描述
统计出当前各个title类型对应的员工当前（to_date='9999-01-01'）薪水对应的平均工资。结果给出title以及平均工资avg。
```
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));

CREATE TABLE IF NOT EXISTS "titles" (
`emp_no` int(11) NOT NULL,
`title` varchar(50) NOT NULL,
`from_date` date NOT NULL,
`to_date` date DEFAULT NULL);
```
## 答案
```
select t.title, avg(s.salary) 
from titles t left join salaries s on t.emp_no = s.emp_no 
where t.to_date='9999-01-01' and s.to_date='9999-01-01' group by t.title
```

# 获取当前薪水第二多的员工的emp_no以及其对应的薪水salary
## 题目描述
获取当前（to_date='9999-01-01'）薪水第二多的员工的emp_no以及其对应的薪水salary
```
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
select emp_no, salary from salaries where to_date = '9999-01-01' order by salary desc limit 1,1

-- 考虑薪水去重，链接：https://www.nowcoder.com/questionTerminal/8d2c290cc4e24403b98ca82ce45d04db
select emp_no, salary from salaries
where to_date = '9999-01-01' and salary = (select distinct salary from salaries order by salary desc limit 1,1)
```

# 获取当前薪水第二多的员工的emp_no以及其对应的薪水salary，不准使用order by
## 题目描述
查找当前薪水(to_date='9999-01-01')排名第二多的员工编号emp_no、薪水salary、last_name以及first_name，不准使用order by
```
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
SELECT e.emp_no, MAX(s.salary) AS salary, e.last_name, e.first_name 
FROM employees AS e INNER JOIN salaries AS s ON e.emp_no = s.emp_no
WHERE s.to_date = '9999-01-01' AND s.salary 
NOT IN (SELECT MAX(salary) FROM salaries WHERE to_date = '9999-01-01')
```

# 查找所有员工的last_name和first_name以及对应的dept_name
## 题目描述
查找所有员工的last_name和first_name以及对应的dept_name，也包括暂时没有分配部门的员工
```
CREATE TABLE `departments` (
`dept_no` char(4) NOT NULL,
`dept_name` varchar(40) NOT NULL,
PRIMARY KEY (`dept_no`));

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
```
## 答案
```
-- 多表连接查询
select e.last_name last_name, e.first_name first_name, d.dept_name dept_name from employees e 
left join dept_emp de on e.emp_no = de.emp_no 
left join departments d on de.dept_no = d.dept_no
```

# 查找员工编号emp_no为10001其自入职以来的薪水salary涨幅值growth
## 题目描述
查找员工编号emp_no为10001其自入职以来的薪水salary涨幅值growth
```
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
-- 默认当前为薪水最高，入职时为最低薪水
select (max(salary)-min(salary)) growth from salaries where emp_no = 10001

-- 当前薪水-入职薪水
SELECT ( 
(SELECT salary FROM salaries WHERE emp_no = 10001 ORDER BY to_date DESC LIMIT 1) -
(SELECT salary FROM salaries WHERE emp_no = 10001 ORDER BY to_date ASC LIMIT 1)
) AS growth
```

# 查找所有员工自入职以来的薪水涨幅情况
## 题目描述
查找所有员工自入职以来的薪水涨幅情况，给出员工编号emp_no以及其对应的薪水涨幅growth，并按照growth进行升序
```
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
-- 内层用LEFT JOIN，外层用INNER JOIN（内层也可以改用 INNER JOIN）
SELECT sCurrent.emp_no, (sCurrent.salary-sStart.salary) AS growth 
FROM (
    SELECT s.emp_no, s.salary FROM employees e 
    LEFT JOIN salaries s ON e.emp_no = s.emp_no 
    WHERE s.to_date = '9999-01-01') AS sCurrent
INNER JOIN (
    SELECT s.emp_no, s.salary FROM employees e 
    LEFT JOIN salaries s ON e.emp_no = s.emp_no 
    WHERE s.from_date = e.hire_date) AS sStart
ON sCurrent.emp_no = sStart.emp_no
ORDER BY growth

-- 内外都层用FROM并列查询
SELECT sCurrent.emp_no, (sCurrent.salary-sStart.salary) AS growth 
FROM (
    SELECT s.emp_no, s.salary FROM employees e, salaries s 
        WHERE e.emp_no = s.emp_no AND s.to_date = '9999-01-01') AS sCurrent, (
    SELECT s.emp_no, s.salary FROM employees e, salaries s 
        WHERE e.emp_no = s.emp_no AND s.from_date = e.hire_date) AS sStart
WHERE sCurrent.emp_no = sStart.emp_no
ORDER BY growth
```

# 统计各个部门的工资记录数
## 题目描述
统计各个部门的工资记录数，给出部门编码dept_no、部门名称dept_name以及次数sum
```
CREATE TABLE `departments` (
`dept_no` char(4) NOT NULL,
`dept_name` varchar(40) NOT NULL,
PRIMARY KEY (`dept_no`));

CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
select de.dept_no, dp.dept_name, count(s.salary) as sum 
from (dept_emp as de inner join salaries as s on de.emp_no = s.emp_no) 
inner join departments as dp on de.dept_no = dp.dept_no 
group by de.dept_no
```

# ***对所有员工的当前薪水按照salary进行按照1-N的排名，相同salary并列且按照emp_no升序排列
## 题目描述
对所有员工的当前(to_date='9999-01-01')薪水按照salary进行按照1-N的排名，相同salary并列且按照emp_no升序排列
```
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
SELECT s1.emp_no, s1.salary, COUNT(DISTINCT s2.salary) AS rank
FROM salaries AS s1, salaries AS s2
WHERE s1.to_date = '9999-01-01'  AND s2.to_date = '9999-01-01' AND s1.salary <= s2.salary
GROUP BY s1.emp_no
ORDER BY s1.salary DESC, s1.emp_no ASC
```
## 笔记
SQL Select语句完整的执行顺序：

>1. from子句组装来自不同数据源的数据；
>2. where子句基于指定的条件对记录行进行筛选；
>3. group by子句将数据划分为多个分组；
>4. 使用聚集函数进行计算；
>5. 使用having子句筛选分组；
>6. 计算所有的表达式；
>7. select 的字段；
>8. 使用order by对结果集进行排序。

# 获取所有非manager员工当前的薪水情况
## 题目描述
获取所有非manager员工当前的薪水情况，给出dept_no、emp_no以及salary ，当前表示to_date='9999-01-01'
```
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL,
`emp_no` int(11) NOT NULL,
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

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
-- 三表连接
select dep.dept_no, ee.emp_no, sa.salary 
from employees ee join salaries sa on ee.emp_no = sa.emp_no 
join dept_emp dep on dep.emp_no = ee.emp_no 
where dep.to_date = '9999-01-01' and sa.to_date = '9999-01-01' 
    and ee.emp_no not in (select emp_no from dept_manager)

-- 两表连接
SELECT de.dept_no, s.emp_no, s.salary 
FROM dept_emp AS de 
INNER JOIN salaries AS s 
    ON s.emp_no = de.emp_no AND s.to_date = '9999-01-01'
WHERE de.emp_no NOT IN (
    SELECT emp_no FROM dept_manager WHERE to_date = '9999-01-01')
```

# 获取员工其当前的薪水比其manager当前薪水还高的相关信息
## 题目描述
获取员工其当前的薪水比其manager当前薪水还高的相关信息，当前表示to_date='9999-01-01',
结果第一列给出员工的emp_no，
第二列给出其manager的manager_no，
第三列给出该员工当前的薪水emp_salary,
第四列给该员工对应的manager当前的薪水manager_salary
```
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL,
`emp_no` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
-- 一张表
select emp.emp_no, ma.emp_no as manager_no, sa.salary as emp_salary, sa1.salary as manager_salary from 
dept_manager ma join salaries sa1 on ma.emp_no = sa1.emp_no, 
dept_emp emp join salaries sa on emp.emp_no = sa.emp_no where
ma.dept_no = emp.dept_no and 
emp_salary > manager_salary and 
ma.to_date = '9999-01-01' and 
sa1.to_date = '9999-01-01' and 
emp.to_date = '9999-01-01' and 
sa.to_date = '9999-01-01'

-- 先创建两张表
SELECT sem.emp_no AS emp_no, sdm.emp_no AS manager_no, sem.salary AS emp_salary, sdm.salary AS manager_salary
FROM (SELECT s.salary, s.emp_no, de.dept_no FROM salaries s INNER JOIN dept_emp de
ON s.emp_no = de.emp_no AND s.to_date = '9999-01-01' ) AS sem, 
(SELECT s.salary, s.emp_no, dm.dept_no FROM salaries s INNER JOIN dept_manager dm
ON s.emp_no = dm.emp_no AND s.to_date = '9999-01-01' ) AS sdm
WHERE sem.dept_no = sdm.dept_no AND sem.salary > sdm.salary
```

# 汇总各个部门当前员工的title类型的分配数目
## 题目描述
汇总各个部门当前员工的title类型的分配数目，结果给出部门编号dept_no、dept_name、其当前员工所有的title以及该类型title对应的数目count
```
CREATE TABLE `departments` (
`dept_no` char(4) NOT NULL,
`dept_name` varchar(40) NOT NULL,
PRIMARY KEY (`dept_no`));

CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

CREATE TABLE IF NOT EXISTS `titles` (
`emp_no` int(11) NOT NULL,
`title` varchar(50) NOT NULL,
`from_date` date NOT NULL,
`to_date` date DEFAULT NULL);
```
## 答案
```
-- 用 GROUP BY 同时对 de.dept_no 和 t.title 进行分组
select emp.dept_no, dep.dept_name, t.title, count(t.title) as count 
from titles t 
join dept_emp emp on t.emp_no = emp.emp_no and t.to_date = '9999-01-01' 
    and emp.to_date = '9999-01-01' 
join departments dep on emp.dept_no = dep.dept_no 
group by emp.dept_no, t.title
```

# ***给出每个员工每年薪水涨幅超过5000的员工
## 题目描述
给出每个员工每年薪水涨幅超过5000的员工编号emp_no、薪水变更开始日期from_date以及薪水涨幅值salary_growth，并按照salary_growth逆序排列。
提示：在sqlite中获取datetime时间对应的年份函数为strftime('%Y', to_date)
```
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```
## 答案
```
select s2.emp_no, s2.from_date, (s2.salary - s1.salary) salary_growth
from salaries s1, salaries s2
where s1.emp_no = s2.emp_no 
and salary_growth > 5000
and (strftime("%Y",s2.to_date) - strftime("%Y",s1.to_date) = 1 
     or strftime("%Y",s2.from_date) - strftime("%Y",s1.from_date) = 1 )
order by salary_growth desc
```

# *** 查找描述信息中包括robot的电影对应的分类名称以及电影数目
## 题目描述
查找描述信息中包括robot的电影对应的分类名称以及电影数目，而且还需要该分类对应电影数量>=5部
```
CREATE TABLE IF NOT EXISTS film (
film_id smallint(5)  NOT NULL DEFAULT '0',
title varchar(255) NOT NULL,
description text,
PRIMARY KEY (film_id));

CREATE TABLE category  (
category_id  tinyint(3)  NOT NULL ,
name  varchar(25) NOT NULL, `last_update` timestamp,
PRIMARY KEY ( category_id ));

CREATE TABLE film_category  (
film_id  smallint(5)  NOT NULL,
category_id  tinyint(3)  NOT NULL, `last_update` timestamp);
```
## 答案
```
select c.name, count(fc.film_id) from
 (select category_id, count(film_id) as category_num from
     film_category  group by category_id having count(film_id)>=5) as cc,
 film as f, film_category as fc, category as c
where  f.description like '%robot%'
and f.film_id = fc.film_id
and c.category_id = fc.category_id
and c.category_id=cc.category_id and count >= 5
```

# 使用join查询方式找出没有分类的电影id以及名称
## 题目描述
使用join查询方式找出没有分类的电影id以及名称
```
CREATE TABLE IF NOT EXISTS film (
film_id smallint(5)  NOT NULL DEFAULT '0',
title varchar(255) NOT NULL,
description text,
PRIMARY KEY (film_id));

CREATE TABLE category  (
category_id  tinyint(3)  NOT NULL ,
name  varchar(25) NOT NULL, `last_update` timestamp,
PRIMARY KEY ( category_id ));

CREATE TABLE film_category  (
film_id  smallint(5)  NOT NULL,
category_id  tinyint(3)  NOT NULL, `last_update` timestamp);
```
## 答案
```
select fi.film_id, fi.title from 
film fi left join film_category fc on fi.film_id = fc.film_id 
where fc.category_id is null
```

# 使用子查询的方式找出属于Action分类的所有电影对应的title,description
## 题目描述
使用子查询的方式找出属于Action分类的所有电影对应的title,description
```
CREATE TABLE IF NOT EXISTS film (
film_id smallint(5)  NOT NULL DEFAULT '0',
title varchar(255) NOT NULL,
description text,
PRIMARY KEY (film_id));

CREATE TABLE category  (
category_id  tinyint(3)  NOT NULL ,
name  varchar(25) NOT NULL, `last_update` timestamp,
PRIMARY KEY ( category_id ));

CREATE TABLE film_category  (
film_id  smallint(5)  NOT NULL,
category_id  tinyint(3)  NOT NULL, `last_update` timestamp);
```
## 答案
```
-- 子查询
select f.title,f.description from film as f
where f.film_id in (select fc.film_id from film_category as fc
               where fc.category_id in (select c.category_id from category as c
                                        where c.name = 'Action'));

-- 连接
select f.title,f.description
from film as f inner join film_category as fc on f.film_id = fc.film_id
               inner join category as c on c.category_id = fc.category_id
where c.name = 'Action';
```

# 获取`select * from employees`对应的执行计划
## 题目描述
获取`select * from employees`对应的执行计划
## 答案
```
explain select * from employees
```
## 笔记
>链接：https://www.nowcoder.com/questionTerminal/18f30bb19fd34abebcf7e6397c7fb5d8?f=discussion

>在SQLite数据库中，可以用 "EXPLAIN" 关键字或 "EXPLAIN QUERY PLAN" 短语，用于描述表的细节，具体说明与用法可参考：  
http://www.runoob.com/sqlite/sqlite-explain.html  
http://www.sqlite.org/lang_explain.html  

# 将employees表的所有员工的last_name和first_name拼接起来作为Name
## 题目描述
将employees表的所有员工的last_name和first_name拼接起来作为Name，中间以一个空格区分
```
CREATE TABLE `employees` ( `emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```
## 答案
```
select last_name || " " || first_name as Name from employees
```
## 笔记
MySQL、SQL Server、Oracle等数据库支持CONCAT方法，
而本题所用的SQLite数据库只支持用连接符号"||"来连接字符串
CONCAT方法：
`select CONCAT(CONCAT(last_name," "),first_name) as name  from employees`
或者
`select CONCAT(last_name," "，first_name) as name  from employees` 
本题中使用：
`select last_name||" "||first_name as name  from employees`
---
layout: post
title: SQL实战2--表结构
categories: Database
description: SQL练习题-表结构
keywords: SQL, Database
---

# 创建一个actor表，包含如下列信息
## 题目描述
创建一个actor表，包含如下列信息

|列表 | 类型 | 是否为NULL |含义|
|----|----|----|----|
|actor_id |   smallint(5) |not null  |  主键id|
|first_name | varchar(45) |not null  |  名字|
|last_name  | varchar(45) |not null |   姓氏|
|last_update| timestamp  | not null |   最后更新时间，默认是系统的当前时间|

## 答案
```
-- SQLite
create table actor (
    actor_id smallint(5) not null primary key,
    first_name varchar(45) not null,
    last_name varchar(45) not null,
    last_update timestamp NOT NULL DEFAULT (datetime('now','localtime'))
)

-- MySQL
create table actor(
actor_id allint(5) not null,
first_name varchar(45) not null,
last_name varchar(45) not null,
last_update timestamp not null default current_timestamp,
primary key(actor_id));
```



# 创建一个actor_name表，将actor表中的所有first_name以及last_name导入改表
## 题目描述
对于如下表actor，其对应的数据为:

|actor_id   | first_name | last_name |  last_update|
| ---- | ---- | ---- | ---- |
|1  | PENELOPE   | GUINESS| 2006-02-15 12:34:33|
|2  | NICK   | WAHLBERG|    2006-02-15 12:34:33|

创建一个actor_name表，将actor表中的所有first_name以及last_name导入改表。 actor_name表结构如下：

|列表 | 类型 | 是否为NULL| 含义|
| ---- | ---- | ---- | ---- |
|first_name | varchar(45)| not null |   名字|
|last_name  | varchar(45) |not null   | 姓氏|

## 答案
```
-- SQLITE
create table actor_name as
select first_name,last_name from actor;

-- MySQL
create table actor_name [as]
select first_name,last_name from actor;
```

# 对first_name创建唯一索引uniq_idx_firstname，对last_name创建普通索引idx_lastname
## 题目描述
针对如下表actor结构创建索引：
```
CREATE TABLE IF NOT EXISTS actor (
actor_id smallint(5) NOT NULL PRIMARY KEY,
first_name varchar(45) NOT NULL,
last_name varchar(45) NOT NULL,
last_update timestamp NOT NULL DEFAULT (datetime('now','localtime')))
对first_name创建唯一索引uniq_idx_firstname，对last_name创建普通索引idx_lastname
```
## 答案
```
create unique index uniq_idx_firstname on actor(first_name);
create index idx_lastname on actor(last_name);
```
## 笔记
>CREATE INDEX 语句用于在表中创建索引。在不读取整个表的情况下，索引使数据库应用程序可以更快地查找数据。<br><br>
**用户无法看到索引，它们只能被用来加速搜索/查询**。注意：更新一个包含索引的表需要比更新一个没有索引的表更多的时间，这是由于索引本身也需要更新。因此，理想的做法是仅仅在常常被搜索的列（以及表）上面创建索引。<br><br>
给指定表或者视图的某列添加索引使用语句：`CREATE [UNIQUE/...] INDEX indexName ON tableName(colName);`<br>
唯一的索引意味着两个行不能拥有相同的索引值。

# 针对actor表创建视图actor_name_view
## 题目描述
针对actor表创建视图actor_name_view，只包含first_name以及last_name两列，并对这两列重新命名，first_name为first_name_v，last_name修改为last_name_v：
```
CREATE TABLE IF NOT EXISTS actor (
actor_id smallint(5) NOT NULL PRIMARY KEY,
first_name varchar(45) NOT NULL,
last_name varchar(45) NOT NULL,
last_update timestamp NOT NULL DEFAULT (datetime('now','localtime')))
```
## 答案
```
-- 方法一：CREATE VIEW ... AS ... 的 AS 是创建视图语法中的一部分，而后面的两个 AS 只是为字段创建别名
create view actor_name_view as
select first_name first_name_v, last_name last_name_v from actor

-- 方法二：直接在视图名的后面用小括号创建视图中的字段名
CREATE VIEW actor_name_view (fist_name_v, last_name_v) AS
SELECT first_name, last_name FROM actor 
```

# 针对salaries表emp_no字段创建索引idx_emp_no
## 题目描述
针对salaries表emp_no字段创建索引idx_emp_no，查询emp_no为10005, 使用强制索引。
```
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
create index idx_emp_no on salaries(emp_no);
```
## 答案
```
-- SQLite中，使用 INDEXED BY 语句进行强制索引查询
select * from salaries INDEXED BY idx_emp_no WHERE emp_no = 10005

-- MySQL中，使用 FORCE INDEX 语句进行强制索引查询
SELECT * FROM salaries FORCE INDEX idx_emp_no WHERE emp_no = 10005
```

# 在last_update后面新增加一列名字为create_date
## 题目描述
存在actor表，包含如下列信息：
```
CREATE TABLE IF NOT EXISTS actor (
actor_id smallint(5) NOT NULL PRIMARY KEY,
first_name varchar(45) NOT NULL,
last_name varchar(45) NOT NULL,
last_update timestamp NOT NULL DEFAULT (datetime('now','localtime')));
```
现在在last_update后面新增加一列名字为create_date, 类型为datetime, NOT NULL，默认值为'0000 00:00:00'
## 答案
```
alter table actor 
add create_date datetime not null default '0000-00-00 00:00:00'
```

# 构造一个触发器audit_log
## 题目描述
构造一个触发器audit_log，在向employees_test表中插入一条数据的时候，触发插入相关的数据到audit中。
```
CREATE TABLE employees_test(
ID INT PRIMARY KEY NOT NULL,
NAME TEXT NOT NULL,
AGE INT NOT NULL,
ADDRESS CHAR(50),
SALARY REAL
);

CREATE TABLE audit(
EMP_no INT NOT NULL,
NAME TEXT NOT NULL
);
```
## 答案
```
create trigger audit_log after insert on employees_test
begin
insert into audit values(NEW.ID,NEW.NAME);
end
```
## 笔记
触发器的定义就是说某个条件成立的时候，触发器里面所定义的语句就会被自动的执行。因此触发器不需要人为的去调用，也不能调用。<br>
语法：
>1.create trigger ：创建触发器<br>
2.触发器要说明是在after 还是before事务发生时触发<br>
3.要指明是insert 、delete、update操作<br>
4.on 表名<br>
5.begin和end之间写触发的动作<br>
6.new 关键字表示更新后的表的字段 ，old表示更新前的表的字段<br>

# 将titles_test表名修改为titles_2017
## 题目描述
将titles_test表名修改为titles_2017。
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
-- sqlite3，必须要加to
alter table titles_test rename to titles_2017
-- mysql，不用加to
alter table titles_test rename titles_2017
```

# 在audit表上创建外键约束，其emp_no对应employees_test表的主键id
## 题目描述
在audit表上创建外键约束，其emp_no对应employees_test表的主键id。
```
CREATE TABLE employees_test(
ID INT PRIMARY KEY NOT NULL,
NAME TEXT NOT NULL,
AGE INT NOT NULL,
ADDRESS CHAR(50),
SALARY REAL
);

CREATE TABLE audit(
EMP_no INT NOT NULL,
create_date datetime NOT NULL
);
```
## 答案
```
-- sqlite3
drop table audit;
create table audit(
    EMP_no int not null,
    create_date datetime not null,
    FOREIGN KEY(EMP_no) REFERENCES employees_test(ID));
-- MySQL
alter table audit
add foreign key(emp_no) references employees_test(id)
```
## 笔记
PRIMARY KEY 约束唯一标识数据库表中的每条记录。主键必须包含唯一的值。
主键列不能包含 NULL 值。<br>
FOREIGN KEY 约束用于预防破坏表之间连接的动作。FOREIGN KEY 约束也能防止非法数据插入外键列，因为它必须是它指向的那个表中的值之一。
---
layout: post
title: python 语法基础
categories: python
description: python 语法基础
keywords: python
---

# 基础
## 注释
```
# 单行注释
'''
多行注释1
多行注释2
'''
```
## 中文支持
```
# _*_ coding:utf-8 _*_
# coding=utf-8
```
## 变量类型

| 类型 | 符号 | 举例 |
|----|----|---|
|整数|int|1, 0x12ab|
|浮点数|float|1.23, 1e23|
|字符串|str|'123'|
|布尔值|bool|True, False|
|列表|list|[1, 2]|
|元组|tuple|(1, 2)|
|字典|dict|{1:2, 3:4}|

通过`type(var_name)`可以知道变量类型

## 格式化输出

| 格式符号 | 转换 |
|----|----|
| %c | 字符 |
| %s | 通过str()字符串转换来格式化 |
| %i | 有符号十进制整数 |
| %d | 有符号十进制整数 |
| %u | 无符号十进制整数 |
| %o | 八进制整数 |
| %x | 十六进制整数（小写字母） |
| %X | 十六进制整数（大写字母） |
| %e | 索引符号(小写'e') |
| %E | 索引符号(小写'E')  |
| %f | 浮点实数 |
| %g | %f和%e的简写 |
| %G | %f和%E的简写 |

换行输出： `\n`

```python
>>> a = str(2)
>>> print('%c' % a)
2
>>> print('The quick brown fox', 'jumps over', 'the lazy dog')
The quick brown fox jumps over the lazy dog
```

## 输入
```python
# input()接受表达式输入
>>>b = input()
123
>>>b
123
```

## 常用的数据类型转换

| 函数 | 说明 |
|----|----|
|int(x[,base])|将x转换为一个整数|
|long(x[,base])|将x转换为一个长整数|
|float(x)|将x转换为一个浮点数|
|complex(real[,imag])|创建一个复数|
|str(x)|将对象x转换为字符串|
|repr(x)|将对象x转换为表达式字符串|
|eval(str)|用来计算在字符串中的有效Python表达式，并返回一个对象|
|tuple(s)|将序列s转换为一个元组|
|list(s)|将序列s转换为一个列表|
|chr(x)|将整数转换为字符|
|unichr(x)|将整数转换为Unicode字符|
|ord(x)|将字符转换为它的整数值|
|hex(x)|将整数转换为十六进制字符串|
|oct(x)|将整数转换为八进制字符串|

## 判断语句
```python
if condition:
	#do some thing
	if condition1.1:
		#do
	else:
		#do
elif condition2:
	#do
elif condition3:
	#do
else:
	#do other
```

### 附：猜拳游戏

```python
import random

    player = input('请输入：剪刀(0)  石头(1)  布(2):')

    player = int(player)

    computer = random.randint(0,2)

    # 用来进行测试
    #print('player=%d,computer=%d',(player,computer))

    if ((player == 0) and (computer == 2)) or ((player ==1) and (computer == 0)) or ((player == 2) and (computer == 1)):
        print('获胜，哈哈，你太厉害了')
    elif player == computer:
        print('平局，要不再来一局')
    else:
        print('输了，不要走，洗洗手接着来，决战到天亮')
```

## 循环语句
```python
#while循环
while condition:
	#do
	while condition2:
		#do

#for循环
for var in (list or string):
	#do
else:#循环不满足条件时执行
	#do
```

# 函数
## 示例
```python
def fun_name():
    "函数说明，通过help()可查看"
    #do

def test(a, b):
    print(a, b)

>>>print(1, 2)
1 2
>>>print(b = 1, a = 2)
2 1
>>>print(b = 1, 2)
SystaxError

def test(a, b):
    return a + b

>>>a = test(1, 2)
3

def test(a, b):
    return a, b
>>>a, b = test(1, 2)
>>>a, b
(1, 2)
```

## 函数类型
- 无参数，无返回值
- 无参数，有返回值
- 有参数，有返回值
- 有参数，有返回值

## 变量
 - 在函数外边定义的变量叫做全局变量
 - 全局变量能够在所有的函数中进行访问
 - 如果在函数中修改全局变量，那么就需要使用`global`进行声明，否则出错
 - **如果全局变量的名字和局部变量的名字相同，那么使用的是局部变量的**
 - 在函数中不使用global声明全局变量时不能修改全局变量的本质是不能修改全局变量的指向，即不能将全局变量指向新的数据。
 - 对于不可变类型的全局变量来说，因其指向的数据不能修改，所以不使用global时无法修改全局变量。
 - 对于可变类型的全局变量来说，因其指向的数据可以修改，所以不使用global时也可修改全局变量。

## 参数
### 缺省参数
调用函数时，缺省参数的值如果没有传入，则被认为是默认值。**带有默认值的参数一定要位于参数列表的最后面。**
```python
def printinfo( name, age = 35 ):
   # 打印任何传入的字符串
   print "Name: ", name
   print "Age ", age

# 调用printinfo函数
printinfo(name="miki" )
printinfo( age=9,name="miki" )
```
### 不定长参数
有时可能需要一个函数能处理比当初声明时更多的参数。这些参数叫做不定长参数，声明时不会命名。

基本语法如下：
```
def functionname([formal_args,] *args, **kwargs):
   "函数_文档字符串"
   function_suite
   return [expression]
```
加了星号（\*）的变量args会存放所有未命名的变量参数，args为元组；而加**的变量kwargs会存放命名参数，即形如key=value的参数， kwargs为字典。

### 引用传参
```python
>>> def selfAdd(a):
...     """自增"""
...     a += a
...
>>> a_int = 1
>>> a_int
1
>>> selfAdd(a_int)
>>> a_int
1
>>> a_list = [1, 2]
>>> a_list
[1, 2]
>>> selfAdd(a_list)
>>> a_list
[1, 2, 1, 2]
```

```python
>>> def selfAdd(a):
...     """自增"""
...     a = a + a   # 我们更改了函数体的这句话
...
>>> a_int = 1
>>> a_int
1
>>> selfAdd(a_int)
>>> a_int
1
>>> a_list = [1, 2]
>>> a_list
[1, 2]
>>> selfAdd(a_list)
>>> a_list
[1, 2]      # 为局部变量
```
## 匿名函数
用lambda关键词能创建小型匿名函数。这种函数得名于省略了用def声明函数的标准步骤。

lambda函数的语法只包含一个语句，如下：
`lambda [arg1 [,arg2,.....argn]]:expression`
如下实例：
```
sum = lambda arg1, arg2: arg1 + arg2

#调用sum函数
print "Value of total : ", sum( 10, 20 )
print "Value of total : ", sum( 20, 20 )
```
以上实例输出结果：
```
Value of total :  30
Value of total :  40
```
Lambda函数能接收任何数量的参数但只能返回一个表达式的值
匿名函数不能直接调用print，因为lambda需要一个表达式














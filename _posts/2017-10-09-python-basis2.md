---
layout: post
title: python 字符串、列表、元组、字典
categories: python
description: 字符串、列表、元组、字典
keywords: python
---

# 字符串
1. 输出  
`print '%s'%str`
2. 输入  
`username = input()`
3. 下标  
字符串实际上就是字符的数组，所以也支持下标索引。
4. 切片  
切片的语法：[起始:结束:步长]
注意：选取的区间属于**左闭右开**型，即从"起始"位开始，到"结束"位的前一位结束（不包含结束位本身)。

## 常见操作

|函数|用途|用法|
|----|----|----|
|find|检测 str 是否包含在 mystr中，如果是返回开始的索引值，否则返回-1|`mystr.find(str, start=0, end=len(mystr))`|
|index|同find()，只不过如果str不在 mystr中会报一个异常|`mystr.index(str, start=0, end=len(mystr)) `|
|count|返回 str在start和end之间在mystr里面出现的次数 |`mystr.count(str, start=0, end=len(mystr))`|
|replace |把 mystr 中的 str1 替换成 str2,如果 count 指定，则替换不超过 count 次|`mystr.replace(str1, str2,  mystr.count(str1))`|
|split|以 str 为分隔符切片 mystr，如果 maxsplit有指定值，则仅分隔 maxsplit 个子字符串|`mystr.split(str=" ", 2) `|
|capitalize|把字符串的第一个字符大写|`mystr.capitalize()`|
|title|把字符串的每个单词首字母大写||
|startswith|检查字符串是否是以 obj 开头, 是则返回 True，否则返回 False|`mystr.startswith(obj)`|
|endswith|检查字符串是否以obj结束，如果是返回True,否则返回 False|`mystr.endswith(obj)`|
|lower|转换 mystr 中所有大写字符为小写 |`mystr.lower()` |
|upper|转换 mystr 中的小写字母为大写|`mystr.upper()` |
|ljust |返回一个原字符串左对齐,并使用空格填充至长度 width 的新字符串|`mystr.ljust(width) `|
|rjust|返回一个原字符串右对齐,并使用空格填充至长度 width 的新字符串|`mystr.rjust(width)`|
|center|返回一个原字符串居中,并使用空格填充至长度 width 的新字符串|`mystr.center(width)`|
|lstrip|删除 mystr 左边的空白字符|`mystr.lstrip()`|
|rstrip|删除 mystr 字符串末尾的空白字符|`mystr.rstrip() `|
|strip|删除mystr字符串两端的空白字符||
|rfind|类似于 find()函数，不过是从右边开始查找.|`mystr.rfind(str, start=0,end=len(mystr) )`|
|rindex|类似于 index()，不过是从右边开始|`mystr.rindex( str, start=0,end=len(mystr))`|
|partition|把mystr以str分割成三部分,str前，str和str后|`mystr.partition(str)`|
|rpartition|类似于 partition()函数,不过是从右边开始|`mystr.rpartition(str)`|
|splitlines|按照行分隔，返回一个包含各行作为元素的列表|`mystr.splitlines()`|
|isalpha|如果 mystr 所有字符都是字母 则返回 True,否则返回 False|`mystr.isalpha()`|
|isdigit|如果 mystr 只包含数字则返回 True 否则返回 False|`mystr.isdigit()`|
|isalnum|如果 mystr 所有字符都是字母或数字则返回 True,否则返回 False|`mystr.isalnum() `|
|isspace|如果 mystr 中只包含空格，则返回 True，否则返回 False|`mystr.isspace()`|
|join|mystr 中每个字符后面插入str,构造出一个新的字符串|`mystr.join(str)`|

# 列表
1. 格式  
变量A的类型为列表
`namesList = ['xiaoWang','xiaoZhang','xiaoHua']`
比C语言的数组强大的地方在于列表中的元素可以是不同类型的
`testList = [1, 'a']`
2. 输出：下标、循环

## 常见操作
### 添加元素("增"append, extend, insert)
- append  
通过append可以向列表末尾添加元素
 

```
#定义变量A，默认有3个元素
A = ['xiaoWang','xiaoZhang','xiaoHua']

print("-----添加之前，列表A的数据-----")
for tempName in A:
    print(tempName)

#提示、并添加元素
temp = input('请输入要添加的学生姓名:')
A.append(temp)

print("-----添加之后，列表A的数据-----")
for tempName in A:
    print(tempName)
```


- extend  
通过extend可以将另一个集合中的元素逐一添加到列表中

```
>>> a = [1, 2]
>>> b = [3, 4]
>>> a.append(b)
>>> a
[1, 2, [3, 4]]
>>> a.extend(b)
>>> a
[1, 2, [3, 4], 3, 4]
```

- insert
insert(index, object) 在指定位置index前插入元素object


```
>>> a = [0, 1, 2]
>>> a.insert(1, 3)
>>> a
[0, 3, 1, 2]
```

### 修改元素("改")
修改元素的时候，要通过下标来确定要修改的是哪个元素，然后才能进行修改
demo:
```
#定义变量A，默认有3个元素
A = ['xiaoWang','xiaoZhang','xiaoHua']

print("-----修改之前，列表A的数据-----")
for tempName in A:
   print(tempName)

#修改元素
A[1] = 'xiaoLu'

print("-----修改之后，列表A的数据-----")
for tempName in A:
   print(tempName)
```
结果:
```
-----修改之前，列表A的数据-----
xiaoWang
xiaoZhang
xiaoHua
-----修改之后，列表A的数据-----
xiaoWang
xiaoLu
xiaoHua
```

### 查找元素("查"in, not in, index, count)
所谓的查找，就是看看指定的元素是否存在
- in, not in  
python中查找的常用方法为：
- in（存在）,如果存在那么结果为true，否则为false
- not in（不存在），如果不存在那么结果为true，否则false 


demo
```
#待查找的列表
nameList = ['xiaoWang','xiaoZhang','xiaoHua']

#获取用户要查找的名字
findName = input('请输入要查找的姓名:')

#查找是否存在
if findName in nameList:
   print('在字典中找到了相同的名字')
else:
   print('没有找到')
```


- index, count

index和count与字符串中的用法相同
```
>>> a = ['a', 'b', 'c', 'a', 'b']
>>> a.index('a', 1, 3) # 注意是左闭右开区间
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: 'a' is not in list
>>> a.index('a', 1, 4)
3
>>> a.count('b')
2
>>> a.count('d')
0
```

### 删除元素("删"del, pop, remove)
类比现实生活中，如果某位同学调班了，那么就应该把这个条走后的学生的姓名删除掉；在开发中经常会用到删除这种功能。

列表元素的常用删除方法有：
- del：根据下标进行删除
- pop：删除最后一个元素
- remove：根据元素的值进行删除，**只删除第一个指定的值**

demo:(del)
```
movieName = ['加勒比海盗','骇客帝国','第一滴血','指环王','霍比特人','速度与激情']

print('------删除之前------')
for tempName in movieName:
    print(tempName)

del movieName[2]

print('------删除之后------')
for tempName in movieName:
    print(tempName)
```
结果:
```
------删除之前------
加勒比海盗
骇客帝国
第一滴血
指环王
霍比特人
速度与激情
------删除之后------
加勒比海盗
骇客帝国
指环王
霍比特人
速度与激情
```
demo:(pop)
```
movieName = ['加勒比海盗','骇客帝国','第一滴血','指环王','霍比特人','速度与激情']

print('------删除之前------')
for tempName in movieName:
    print(tempName)

movieName.pop()

print('------删除之后------')
for tempName in movieName:
    print(tempName)
```
结果:
```
------删除之前------
加勒比海盗
骇客帝国
第一滴血
指环王
霍比特人
速度与激情
------删除之后------
加勒比海盗
骇客帝国
第一滴血
指环王
霍比特人
```
demo:(remove)
```
movieName = ['加勒比海盗','骇客帝国','第一滴血','指环王','霍比特人','速度与激情']

print('------删除之前------')
for tempName in movieName:
    print(tempName)

movieName.remove('指环王')

print('------删除之后------')
for tempName in movieName:
    print(tempName)
```
结果:
```
------删除之前------
加勒比海盗
骇客帝国
第一滴血
指环王
霍比特人
速度与激情
------删除之后------
加勒比海盗
骇客帝国
第一滴血
霍比特人
速度与激情
```

### 排序(sort, reverse)
sort方法是将list按特定顺序重新排列，默认为由小到大，参数reverse=True可改为倒序，由大到小。
reverse方法是将list逆置。
二者都是**永久性**修改列表
sorted()会临时排序
```
>>> a = [1, 4, 2, 3]
>>> a
[1, 4, 2, 3]
>>> a.reverse()
>>> a
[3, 2, 4, 1]
>>> a.sort()
>>> a
[1, 2, 3, 4]
>>> a.sort(reverse=True)
>>> a
[4, 3, 2, 1]
>>> sorted(a)
[1, 2, 3, 4]
>>> a
[4, 3, 2, 1]
```

# 元组
Python的元组与列表类似，不同之处在于**元组的元素不能修改**。元组使用小括号，列表使用方括号。index和count与字符串和列表中的用法相同

# 字典
1. 变量info为字典类型：
`info = {'name':'班长', 'id':100, 'sex':'f', 'address':'地球亚洲中国北京'}`
说明：
- 字典和列表一样，也能够存储多个数据
- 列表中找某个元素时，是根据下标进行的，字典中找某个元素时，是根据'名字'（就是冒号:前面的那个值，例如上面代码中的'name'、'id'、'sex'）
- 字典的每个元素由2部分组成，键:值。例如 'name':'班长' ,'name'为键，'班长'为值
2. 根据键访问值
`info = {'name':'班长', 'id':100, 'sex':'f', 'address':'地球亚洲中国北京'}`

```
print(info['name'])
print(info['address'])
```
结果:

```
班长
地球亚洲中国北京
```
若访问不存在的键，则会报错：

```
>>> info['age']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'age'
```
在我们不确定字典中是否存在某个键而又想获取其值时，可以使用get方法，还可以设置默认值:

```
>>> age = info.get('age')
>>> age #'age'键不存在，所以age为None
>>> type(age)
<type 'NoneType'>
>>> age = info.get('age', 18) # 若info中不存在'age'这个键，就返回默认值18
>>> age
18
```

## 常见操作
### 修改元素
字典的每个元素中的数据是可以修改的，只要通过key找到，即可修改
demo:
```
info = {'name':'班长', 'id':100, 'sex':'f', 'address':'地球亚洲中国北京'}
newId = input('请输入新的学号')
info['id'] = int(newId)
print('修改之后的id为%d:'%info['id'])
```
### 添加元素
demo:访问不存在的元素
```
info = {'name':'班长', 'sex':'f', 'address':'地球亚洲中国北京'}
print('id为:%d'%info['id'])
#报错
```
如果在使用 变量名['键'] = 数据 时，这个“键”在字典中，不存在，那么就会新增这个元素

demo:添加新的元素
```
info = {'name':'班长', 'sex':'f', 'address':'地球亚洲中国北京'}
# print('id为:%d'%info['id'])#程序会中断运行，因为访问了不存在的键
newId = input('请输入新的学号')
info['id'] = newId
print('添加之后的id为:%d'%info['id'])
```
结果:
```
请输入新的学号188
添加之后的id为: 188
```

### 删除元素
对字典进行删除操作，有一下几种：
- del
- clear()
demo:del删除指定的元素

```
info = {'name':'班长', 'sex':'f', 'address':'地球亚洲中国北京'}
print('删除前,%s'%info['name'])
del info['name']
print('删除后,%s'%info['name'])
# KeyError
```
demo:del删除整个字典
```
info = {'name':'monitor', 'sex':'f', 'address':'China'}
print('删除前,%s'%info)
del info
print('删除后,%s'%info)
# NameError
```
demo:clear清空整个字典
```
info = {'name':'monitor', 'sex':'f', 'address':'China'}
print('清空前,%s'%info)
info.clear()
print('清空后,%s'%info)
#{}
```

### len()
测量字典中，键值对的个数
### keys()
返回一个包含字典所有KEY的列表
### values()
返回一个包含字典所有value的列表
### items()
返回一个包含所有（键，值）元祖的列表
### has_key(key)
dict.has_key(key)如果key在字典中，返回True，否则返回False

# 遍历
通过for ... in ...:的语法结构，我们可以遍历字符串、列表、元组、字典等数据结构。
注意python语法的缩进
## 字符串遍历
```
>>> a_str = "hello itcast"
>>> for char in a_str:
...     print(char,end=' ')
...
h e l l o   i t c a s t
```
## 列表遍历
```
>>> a_list = [1, 2, 3, 4, 5]
>>> for num in a_list:
...     print(num,end=' ')
...
1 2 3 4 5
```
## 元组遍历
```
>>> a_turple = (1, 2, 3, 4, 5)
>>> for num in a_turple:
...     print(num,end=" ")
1 2 3 4 5
```
## 字典遍历
1. 遍历字典的key（键）
`for key in dict.keys()`
2. 遍历字典的value（值）
`for value in dict.values():`
3. 遍历字典的项（元素）
`for item in dict.items():`
4. 遍历字典的key-value（键值对）
`for key, value in dict.items():`

## 如何实现带下标索引的遍历
```
>>> chars = ['a', 'b', 'c', 'd']
>>> i = 0
>>> for chr in chars:
...     print("%d %s"%(i, chr))
...     i += 1
...
0 a
1 b
2 c
3 d
```
enumerate()
```
>>> chars = ['a', 'b', 'c', 'd']
>>> for i, chr in enumerate(chars):
...     print i, chr
...
0 a
1 b
2 c
3 d
```

# 公共方法
## 运算符

|运算符|表达式|结果|描述|支持的数据类型|
|----|----|----|----|----|
|+|[1,2]+[3,4]|[1, 2, 3, 4]|合并|字符串、列表、元组|
|\*|'hi'*4|'hihihihi'|复制|字符串、列表、元组|
|in|3 in (1, 2, 3)|True|存在|字符串、列表、元组、字典|
|not in|4 in (1, 2, 3)|False|不存在|字符串、列表、元组、字典|

## 内置函数

|序号|方法|描述|
|----|----|----|
|1|cmp(item1, item2)|比较两个值|
|2|len(item)|计算容器中元素个数|
|3|max(item)|返回容器中元素最大值|
|4|min(item)|返回容器中元素最小值|
|5|del(item)|删除变量|

# 引用
在python中，值是靠引用来传递来的。

我们可以用id()来判断两个变量是否为同一个值的引用。 我们可以将id值理解为那块内存的地址标示。
```
>>> a = 1
>>> b = a
>>> id(a) 
13033816
>>> id(b)   # 注意两个变量的id值相同
13033816
>>> a = 2
>>> id(a)   # 注意a的id值已经变了
13033792
>>> id(b)   # b的id值依旧
13033816
>>> a = [1, 2]
>>> b = a
>>> id(a)
139935018544808
>>> id(b)
139935018544808
>>> a.append(3)
>>> a
[1, 2, 3]
>>> id(a)
139935018544808
>>> id(b)       # 注意a与b始终指向同一个地址
139935018544808
```






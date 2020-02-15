---
layout: post
title: python 面向对象
categories: python
description: python 面向对象
keywords: OOP, python
---

# 定义
```python
class 类名:
	def __init__():
        pass
       
    方法列表
	def __str__(self):#print 对象
        msg = " "
        return msg
    
对象名 = 类名()
```
类名 的命名规则按照"大驼峰",方法名的命名规则按照"小驼峰"

# 魔法方法
`__main__()`

`__str__()`

`__init__()`创建对象后，python解释器默认调用__init__()方法

`__del__()`当删除一个对象时，python解释器默认调用

# 对象属性
Python中没有像Java中public和private这些关键字来区别公有属性和私有属性，它是以**属性命名方式**来区分，如果在属性名前面加了2个下划线'__'，则表明该属性是私有属性，否则为公有属性（方法也是一样，方法名前面加了2个下划线的话表示该方法是私有的，否则为公有的）。

# 继承
## 单继承
```python
# 定义一个父类，如下:
class Cat(object):

    def __init__(self, name, color="白色"):
        self.name = name
        self.color = color

    def run(self):
        print("%s--在跑"%self.name)


# 定义一个子类，继承Cat类如下:
class Bosi(Cat):

    def setNewName(self, newName):
        self.name = newName

    def eat(self):
        print("%s--在吃"%self.name)
```
 - 子类在继承的时候，在定义类时，小括号()中为父类的名字
 - 父类的属性、方法，会被继承给子类
 - 私有的属性，不能通过对象直接访问，但是可以通过方法访问
 - 私有的方法，不能通过对象直接访问
 - 私有的属性、方法，不会被子类继承，也不能被访问
 - 一般情况下，私有的属性、方法都是不对外公布的，往往用来做内部的事情，起到安全的作用

## 多继承
```python
# 定义一个父类
class A:
    def printA(self):
        print('----A----')

# 定义一个父类
class B:
    def printB(self):
        print('----B----')

# 定义一个子类，继承自A、B
class C(A,B):
    def printC(self):
        print('----C----')

obj_C = C()
obj_C.printA()
obj_C.printB()
```
 - python中是可以多继承的
 - 父类中的方法、属性，子类会继承
 - 如果父类A和父类B中，有一个同名的方法，那么通过子类去调用的时候，调用前面的那个

# 重写
所谓重写，就是子类中，有一个和父类相同名字的方法，在子类中的方法会覆盖掉父类中同名的方法
# 多态
多态的概念是应用于Java和C#这一类强类型语言中，而Python崇尚“鸭子类型”。  
所谓多态：定义时的类型和运行时的类型不一样，此时就成为多态

# 类属性&实例属性
**如果需要在类外修改类属性，必须通过类对象去引用然后进行修改。如果通过实例对象去引用，会产生一个同名的实例属性，这种方式修改的是实例属性，不会影响到类属性，并且之后如果通过实例对象去引用该名称的属性，实例属性会强制屏蔽掉类属性，即引用的是实例属性，除非删除了该实例属性。**
```python
class People(object):
    name = 'Tom'  #公有的类属性
    __age = 12     #私有的类属性

p = People()

print(p.name)           #正确
print(People.name)      #正确
print(p.__age)            #错误，不能在类外通过实例对象访问私有的类属性
print(People.__age)        #错误，不能在类外通过类对象访问私有的类属性

class People(object):
    address = '山东' #类属性
    def __init__(self):
        self.name = 'xiaowang' #实例属性
        self.age = 20 #实例属性

p = People()
p.age =12 #实例属性
print(p.address) #正确
print(p.name)    #正确
print(p.age)     #正确

print(People.address) #正确
print(People.name)    #错误
print(People.age)     #错误

class People(object):
    country = 'china' #类属性

print(People.country)
p = People()
print(p.country)
p.country = 'japan' 
print(p.country)      #实例属性会屏蔽掉同名的类属性
print(People.country)
del p.country    #删除实例属性
print(p.country)
```

# 类方法&静态方法
## 类方法
类对象所拥有的方法，需要用修饰器@classmethod来标识其为类方法，对于类方法，第一个参数必须是类对象，一般以cls作为第一个参数（当然可以用其他名称的变量作为其第一个参数，但是大部分人都习惯以'cls'作为第一个参数的名字，就最好用'cls'了），能够通过实例对象和类对象去访问。类方法还有一个用途就是可以对类属性进行修改：
```python
class People(object):
    country = 'china'

    #类方法，用classmethod来进行修饰
    @classmethod
    def getCountry(cls):
        return cls.country

p = People()
print p.getCountry()    #可以用过实例对象引用
print People.getCountry()    #可以通过类对象引用
```
## 静态方法
需要通过修饰器@staticmethod来进行修饰，静态方法不需要多定义参数
```python
class People(object):
    country = 'china'

    @staticmethod
    #静态方法
    def getCountry():
        return People.country

print People.getCountry()
```
# 异常
```python
try:
	#do
except (IOException, NameError) as result:
	#do
else:
	#do
finally:
	#do
```
## 自定义异常
```python
class ShortInputException(Exception):
    '''自定义的异常类'''
    def __init__(self, length, atleast):
        #super().__init__()
        self.length = length
        self.atleast = atleast

def main():
    try:
        s = input('请输入 --> ')
        if len(s) < 3:
            # raise引发一个你定义的异常
            raise ShortInputException(len(s), 3)
    except ShortInputException as result:#x这个变量被绑定到了错误的实例
        print('ShortInputException: 输入的长度是 %d,长度至少应是 %d'% (result.length, result.atleast))
    else:
        print('没有异常发生.')

main()
```
# 模块
模块就好比是工具包，要想使用这个工具包中的工具(就好比函数)，就需要导入这个模块
在Python中用关键字import来引入某个模块，比如要引用模块math，就可以在文件最开始的地方用import math来引入。
形如:
`import module1,mudule2...`
在调用math模块中的函数时，必须这样引用：
`模块名.函数名`

当你导入一个模块，Python解析器对模块位置的搜索顺序是：

1. 当前目录
2. 如果不在当前目录，Python则搜索在shell变量PYTHONPATH下的每个目录。
3. 如果都找不到，Python会察看默认路径。UNIX下，默认路径一般为/usr/local/lib/python/
4. 模块搜索路径存储在system模块的sys.path变量中。变量里包含当前目录，PYTHONPATH和由安装过程决定的默认目录。

## 自定义模块
每个Python文件都可以作为一个模块，模块的名字就是文件的名字。
- 定义

```python
#test.py
def add(a,b):
    return a+b
```
- 调用

```python
# main.py
import test

result = test.add(11,22)
print(result)
```

- 测试  
可以根据__name__变量的结果能够判断出，是直接执行的python脚本还是被引入执行的，从而能够有选择性的执行测试代码

```python
if __name__ == 'main':
	#test
```

- 如果一个文件中有`__all__`变量，那么也就意味着这个变量中的元素，不会被`from xxx import *`时导入

- 包将有联系的模块组织在一起，即放到同一个文件夹下，并且在这个文件夹创建一个名字为`__init__.py `文件，那么这个文件夹就称之为包。有效避免模块名称冲突问题，让应用组织结构更加清晰，`__init__.py` 控制着包的导入行为,如果`__init__.py`为空则仅仅是把这个包导入，不会导入包中的模块





















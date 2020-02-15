---
layout: post
title: JVM类加载
categories: Java
description: ClassLoader
keywords: ClassLoader, JVM, Java
---

# 类装载器工作机制 
类装载器就是寻找类的字节码文件并构造出类在JVM内部表示对象的组件。在Java中，类装载器把一个类装入JVM中，要经过以下步骤： 
  1. 装载：查找和导入Class文件； 
  2. 链接：执行校验、准备和解析步骤，其中解析步骤是可以选择的： 
      1. 校验：检查载入Class文件数据的正确性； 
      2. 准备：给类的静态变量分配存储空间； 
      3. 解析：将符号引用转成直接引用(解析阶段在某些情况下可以在初始化阶段后再开始，这时为了支持Java语言的**动态绑定**
这些阶段会依次**开始**，但是不是**依次进行**)； 
  3. 初始化：对类的静态变量、静态代码块执行初始化工作。 

以下5种情况立即对类进行初始化：----**主动引用**
>1. 使用new关键字实例化对象、读取或设置一个类的静态字段(被final修饰、已在编译期把结果放入常量池的静态字段除外)的时候，以及调用一个类的静态方法的时候
>2. 使用java.lang.reflect包的方法对类进行反射调用
>3. 当初始化一个类时，如果发现其父类还没有进行过初始化，则需要先触发其父类的初始化
>4. 当虚拟机启动时，用户需要指定一个要执行的主类(包含main()方法的那个类)
>5. 当使用JDK1.7 的动态语言支持时，如果一个java.lang.invoke.MethodHandle实例最后的解析结果**REF_getStatic**, **REF_putStatic**, **REF_invokeStatic**的方法句柄，并且这个方法句柄所对应的类没有进行初始化

被动引用：此时不会触发类加载
>1. 通过子类引用父类的静态字段，不会导致**子类**的初始化。**对于静态字段，只有直接定义这个字段的类才会被初始化**
>2. 通过数组定义来引用类，不会触发此类的初始化
>3. 常量在编译阶段会存入调用类的常量池中，本质上并没有直接引用到定义常量的类，因此不会触发定义常量的类的初始化。因为在编译阶段通过**常量传播优化**，已经将其他类的常量放在了本类的常量池中

对接口触发初始化的情况与类基本相同，不同点在上述第3种：
**当一个类在初始化时，要求其父类全部都已经初始化过了，但是一个接口在初始化时，并不要求其父类接口全部都完成了初始化，只有在真正使用到父接口的时候才会初始化**

# 类加载的过程
## 装载
装载阶段主要是将java字节码以二进制的方式读入到jvm内存中，然后将二进制数据流按照字节码规范解析成jvm内部的运行时数据结构。java只对字节码进行了规范，并没有对内部运行时数据结构进行规定，不同的jvm实现可以采用不同的数据结构，这些运行时数据结构是保存在jvm的方法区中。当一个类的二进制解析完毕后，jvm最终会在堆上生成一个java.lang.Class类型的实例对象(并没有明确在java堆中，对于HotSpot虚拟机来说，Class对象比较特殊，它虽然是对象，但是存放在**方法区**中)，通过这个对象可以访问到该类在方法区的内容。  
装载阶段jvm需要完成3件事情：
>1. 通过一个类的全限定名来获取定义此类的二进制字节流
    >获取途径：(1) 从本地文件系统中读取
(2) 从网络上加载（典型应用：Java Applet）
(3) 从jar，zip，war等压缩文件中加载
(4) 通过动态将java源文件动态编译产生（jsp的动态编译）
(5) 通过程序直接生成。
>2. 将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构
>3. 在内存中生成一个代表这个类的java.lang.Class对象，作为方法区这个类的各种数据的访问入口

一个**非数组类**的加载过程可控性很强，可以使用**系统提供的引导类加载器**完成，也可以由用户**自定义的类加载器**完成(重写一个类加载器的loadClass())
一个**数组类**本身不通过类加载器创建，而是由java虚拟机直接创建。

## 校验
这一阶段的目的是**确保Class文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全**，比如验证这个类是不是符合字节码的格式、变量与方法是不是有重复、数据类型是不是有效、继承与实现是否合乎标准等等。验证阶段的工作量在JVM的类加载子系统中占了相当大的一部分，按照验证的内容不同又可以细分为4个阶段
>1. 文件格式验证
>2. 元数据验证
>3. 字节码验证
>4. 符号引用验证

## 准备
这一阶段的目的是**正式为类静态变量分配内存并设置类静态变量初始化，这些变量所使用的内存都将在方法区分配**  
*注意：是为静态变量分配内存，不包括实例变量，此时初始化的值为0*
**`public static final int a = 1234;对于static final类型的变量直接初始化为设定的值。**
在jvm中各类型的初始值如下：  
>int,byte,char,long,float,double 默认初始值为0  
boolean 为false（在jvm内部用int表示boolean，因此初始值为0）  
reference类型为null  

## 解析
这一阶段的目的是**JVM将常量池内的符号引用替换为直接引用的过程**。
>符号引用：以一组符号来描述所引用的目标，符号可以是任何形式的字面量，只要无歧义即可
>直接引用：可以是直接指向目标的指针、相对偏移量或是一个能间接定位到目标的句柄

解析动作主要针对**类、接口、字段、类方法、接口方法、方法类型、方法句柄和调用点限定符等**7类符号引用  
1. 解析过程主要针对于常量池中的`CONSTANT_Class_info`, `CONSTANT_Fieldref_info`, `CONSTANT_Methodref_info`及`CONSTANT_InterfaceMethodref_info`四种常量。  
2. jvm规范并没有规定解析阶段发生的时间，只是规定了在执行`anewarray`, `checkcast`, `getfield`, `getstatic`, `instanceof`,`invokeinterface`,`invokespecial`, `invokespecial`, `invokestatic`, `invokevirtual`, `multinewaary`, `new`, `putfield`, `putstatic`这13个指令应用于符号指令时，先对它们进行解析，获取它们的直接引用.  
3. jvm对于每个加载的类都会有在内部创建一个运行时常量池（参考上面图示），在解析之前是以字符串的方式将符号引用保存在运行时常量池中，在程序运行过程中当需要使用某个符号引用时，就会促发解析的过程，解析过程就是通过符号引用查找对应的类实体，然后用直接引用替换符号引用。由于符号引用已经被替换成直接引用，因此后面再次访问时，无需再次解析，直接返回直接引用。

## 初始化
这一阶段根据程序员通过程序制定的主观计划去初始化变量和其他资源，即**初始化阶段是执行类构造器`<clinit()>`方法的过程**。
>`<clinit>()`方法是由编译器自动收集类中的所有类变量的赋值动作和静态语句块中的语句合并产生的。**静态语句块中只能访问到定义在静态语句块之前的变量，定义在它之后的变量，在前面的静态语句块可以赋值，但是不能访问**  
>`<clinit>()`方法不需要显式地调用父类构造器，JVM会保证子类的`<clinit>()`方法执行之前，父类的`<clinit>()`方法已经执行完毕。
>由于父类的`<clinit>()`方法先执行，意味着父类中定义的静态语句块要优先于子类的变量赋值操作
>`<clinit>()`对于类或接口**不是必需**的，如果类中没有静态语句块和赋值操作，就不会生成`<clinit>()`方法
>接口中不能使用静态语句块，但任然有变量初始化的赋值操作，所以接口也会生成`<clinit>()`方法。但是接口不需要先执行父接口的`<clinit>()`方法，只有当父接口中定义的变量使用时父接口才会初始化。
>JVM会保证一个类的`<clinit>()`在多线程环境中被正确的加锁、同步，如果多个线程同时初始化一个类，那么只有一个线程会执行，其他会挂起。

### 初始化执行时机
jvm规范明确规定了初始化执行条件，只要满足以下四个条件之一，就会执行初始化工作
(1) 通过new关键字实例化对象、读取或设置类的静态变量、调用类的静态方法(对应new,getstatic,putstatic,invokespecial这四条字节码指令）。
(2) 通过反射方式执行以上行为时。
(3) 初始化子类的时候，会触发父类的初始化。
(4) 作为程序入口直接运行时的主类。
### 初始化过程
初始化过程包括两步：  
(1) 如果类存在直接父类，并且父类没有被初始化则对直接父类进行初始化。
(2) 如果类当前存在`<clinit>()`方法，则执行`<clinit>()`方法。
需要注意的是接口（interface）的初始化并不要求先初始化它的父接口。（接口不能有static块）

### `<clinit>()`方法存在的条件
并不是每个类都有`<clinit>()`方法,如下情况下不会有`<clinit>()`方法：
1. 类没有静态变量也没有静态语句块  
2. 类中虽然定义了静态变量，但是没有给出明确的初始化语句。
3. 如果类中仅包含了final static的静态变量的初始化语句，而且初始化语句采用编译时常量表达时，也不会有`<clinit>()`方法。

例子：
```
public class ConstantExample {  
  
    public static final int   a = 10;  
    public static final float b = a * 2.0f;  
}  
```
编译之后用 javap -verbose ConstantExample查看字节码,显示如下：

```
{  
public static final int a;  
  Constant value: int 10  
public static final float b;  
  Constant value: float 20.0f  
public ConstantExample();  
  Code:  
   Stack=1, Locals=1, Args_size=1  
   0:   aload_0  
   1:   invokespecial   #15; //Method java/lang/Object."<init>":()V  
   4:   return  
  LineNumberTable:   
   line 12: 0  
  
  LocalVariableTable:   
   Start  Length  Slot  Name   Signature  
   0      5      0    this       LConstantExample;  
  
}  
```
这里由于编译器直接10，当作常量来处理，看到是没有`<clinit>()`方法存在的。可以当作常量来处理的类型包括基本类型和String类型
对于其他类型：
```
public class ConstantExample1 {  
  
    public static final int   a = 10;  
    public static final float b = a * 2.0f;  
    public static final Date  c = new Date();  
}  
```
这里虽然c被声明成final，但是仍然会产生<clinit>()方法，如下所示：
```
{  
public static final int a;  
  Constant value: int 10  
public static final float b;  
  Constant value: float 20.0f  
public static final java.util.Date c;  
  
static {};  
  Code:  
   Stack=2, Locals=0, Args_size=0  
   0:   new #17; //class java/util/Date  
   3:   dup  
   4:   invokespecial   #19; //Method java/util/Date."<init>":()V  
   7:   putstatic   #22; //Field c:Ljava/util/Date;  
   10:  return  
  LineNumberTable:   
   line 19: 0  
   line 14: 10  
```

### 并发性
在同一个类加载器域下，每个类只会被初始化一次，当多个线程都需要初始化同一个类，这时只允许一个线程执行初始化工作，其他线程则等待。当初始化执行完后，该线程会通知其他等待的线程。

类装载工作由ClassLoader及其子类负责，ClassLoader是一个重要的Java运行时系统组件，它负责在运行时查找和装入Class字节码文件。JVM在运行时会产生三个ClassLoader：根装载器、ExtClassLoader（扩展类装载器）和AppClassLoader（系统类装载器）。其中，根装载器不是ClassLoader的子类，它使用C++编写，因此我们在Java中看不到它，根装载器负责装载JRE的核心类库，如JRE目标下的rt.jar、charsets.jar等。ExtClassLoader和AppClassLoader都是ClassLoader的子类。其中ExtClassLoader负责装载JRE扩展目录ext中的JAR类包；AppClassLoader负责装载Classpath路径下的类包。 

这三个类装载器之间存在父子层级关系，即根装载器是ExtClassLoader的父装载器，ExtClassLoader是AppClassLoader的父装载器。

JVM装载类时使用“全盘负责委托机制”，“全盘负责”是指当一个ClassLoader装载一个类时，除非显式地使用另一个ClassLoader，**该类所依赖及引用的类也由这个ClassLoader载入**；“委托机制”是指先委托父装载器寻找目标类，只有在找不到的情况下才从自己的类路径中查找并装载目标类。这一点是从安全角度考虑的，试想如果有人编写了一个恶意的基础类（如java.lang.String）并装载到JVM中将会引起多么可怕的后果。但是由于有了“全盘负责委托机制”，java.lang.String永远是由根装载器来装载的，这样就避免了上述事件的发生。

# 类与类加载器
定义：JVM设计团队把类加载阶段中**“通过一个类的全限定名来获取描述此类的二进制字节码”**这个动作放到JVM外部去实现，以便让应用程序自己决定如何去获取所需要的类。

## jvm运行时内存结构划分
在正式介绍之前，先看看jvm内存结构划分：
![Alt text](/images/posts/2017-08-30-classloader-1.png)

结合垃圾回收机制，将堆细化：
![Alt text](/images/posts/2017-08-30-classloader-2.png)

在加载阶段主要用到的是方法区
方法区是可供各条线程共享的运行时内存区域。存储了每一个类的结构信息，例如运行时常量池（Runtime Constant Pool）、字段和方法数据、构造函数和普通方法的字节码内容、还包括一些在类、实例、接口初始化时用到的特殊方法。
如果把方法的代码看作它的“静态”部分，而把一次方法调用需要记录的临时数据看做它的“动态”部分，那么每个方法的代码是只有一份的，存储于JVM的方法区中；每次某方法被调用，则在该调用所在的线程的的Java栈上新分配一个栈帧，用于存放临时数据，在方法返回时栈帧自动撤销。

## 在使用过程中类，对象在方法区和堆上的分布状态
先上代码
```
public class TestThread extends Thread implements Cloneable {  
  
    public static void main(String[] args) {  
        TestThread t = new TestThread();  
        t.start();  
    }  
}  
```
上面这代码中TestThread及相关类在jvm运行的存储和引用情况如下图所示：
![Alt text](/images/posts/2017-08-30-classloader-t.png)

其中 t 作为TestThread对象的一个引用存储在线程的栈帧空间中，Thread对象及类型数据对应的Class对象实例都存储在堆上，类型数据存储在方法区，前面讲到了，TestThread的类型数据中的符号引用在解析过程中会被替换成直接引用，因此TestThread类型数据中会直接引用到它的父类Thread及它实现的接口Cloneable的类型数据。
在同一个类加载器空间中，对于全限定名相同的类，只会存在唯一的一份类的实例及类型数据。实际上类的实例数据和其对应的Class对象是相互引用的。

## 类加载器
类加载器作用：
1. 类加载阶段加载类
2. **类加载器和类本身一同确立其在JVM中的唯一性**
    >判断两个类是否相等：只有这两个类是由同一个类加载器加载的前提下才有意义，否则即使两个类来源于同一个Class文件，被同一个JVM加载，只要加载它们的类加载器不同，那这两个类就必定不相同。

## 双亲委派模型
从jvm的角度来说，只存在两类加载器，一类是由**c++实现的启动类加载器，是jvm的一部分**，一类是**由java语言实现的应用程序加载器，独立在jvm之外，并且全都继承自抽象类`java.lang.ClassLoader`**。

从开发人员的角度，有3中系统提供的类加载器：
1. 启动类加载器(Bootstrap ClassLoader)：由C++代码实现，负责加载存放在%JAVA_HOME%\lib目录中的，或者通被-Xbootclasspath参数所指定的路径中的，并且被java虚拟机识别的(仅按照文件名识别，如rt.jar，名字不符合的类库，即使放在指定路径中也不会被加载)类库到虚拟机的内存中，启动类加载器无法被java程序直接引用。
2. 扩展类加载器(Extension ClassLoader)：由`sun.misc.Launcher$ExtClassLoader`实现，负责加载`<JAVA_HOME>\lib\ext`目录中的，或者被`java.ext.dirs`系统变量所指定的路径中的所有类库，开发者可以直接使用扩展类加载器。
3.  应用程序类加载器(Application ClassLoader)：这个类加载器由`sun.misc.Launcher$AppClassLoader`实现，负责加载用户类路径classpath上所指定的类库，是类加载器ClassLoader中的getSystemClassLoader()方法的返回值，开发者可以直接使用应用程序类加载器，如果程序中没有自定义过类加载器，该加载器就是程序中**默认的类加载器**。

双亲委派模型：除了顶层的启动类加载器外，其余所有的类加载器都应当有自己的父类加载器。这里类加载器的父子关系是**使用组合关系来复用父加载器的代码**

工作过程：**如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把请求委派给父类加载器去完成，每一个层次的类加载器都是如此，因此所有的加载请求最终都应该传送给顶层的启动类加载器，只有当父类加载器反馈自己无法完成这个加载请求时，子加载器才会尝试自己去加载。**

好处：Java类随着它的类加载器一起具备了一种带有优先级的层次关系。---以Object举例

实现代码：位于`java.lang.ClassLoader`的loadClass()中。

## 双亲委派模型的破坏
1. 如果定义自己的类加载器时直接覆盖loadClass()方法，会破坏双亲委派模型，建议覆盖`findClass()`中。
2. 基础类要调用会用户的代码，会破坏双亲委派模型。由此引入线程上下文类加载器(Thread Context ClassLoader)，让父类加载器请求子类加载器去完成类加载的动作。典型例子：JNDI, JDBC, JCE等。
3. 用户堆程序动态性的追求，如代码热替换、模块热部署等。

## 两种主动加载方式
在java中有两种办法可以在应用程序中主动加载类:
一种是Class类的forName静态方法
```java
public static Class<?> forName(String className)   
                throws ClassNotFoundException   
//允许指定是否初始化，并且指定类的类加载器  
public static Class<?> forName(String name, boolean initialize, ClassLoader loader) throws ClassNotFoundException   
```
另一种就是ClassLoader中的loadClass方法
```java
 protected synchronized Class<?> loadClass(String name, boolean resolve) //第二个参数表示是否在转载完后进行连接（解析）  
    throws ClassNotFoundException  
  
public Class<?> loadClass(String name) throws ClassNotFoundException  
```
上面这两种方式是有区别的，如下例所示
```java
public class InitialClass {  
  
    public static int i;  
    static {  
        i = 1000;  
        System.out.println("InitialClass is init");  
    }  
  
}  

public class InitClassTest {  
  
    public static void main(String[] args) throws MalformedURLException, ClassNotFoundException {  
        Class classFromForName = Class.forName("com.alibaba.china.jianchi.example.InitialClass",  
                                               true,  
                                               new URLClassLoader(  
                                                                  new URL[] { new URL(  
                                                                                      "file:/home/tanfeng/workspace/springStudy/bin/") },  
                                                                  InitClassTest.class.getClassLoader()));  
  
        Class classFromClassLoader = (new URLClassLoader(  
                                                         new URL[] { new URL(  
                                                                             "file:/home/tanfeng/workspace/springStudy/bin/") },  
                                                         InitClassTest.class.getClassLoader())).loadClass("com.alibaba.china.jianchi.example.InitialClass");  
  
    }  
}  
```
通过运行可以考到用Class.forName()方法会将装载的类初始化，而ClassLoader.loadClass()方法则不会。
我们经常会看到在数据库操作时，会用Class.forName()的方式加载驱动类，而不是ClassLoader.loadClass()方法，为何要这样呢？
来看看MySQL的驱动类实现，可以看到在类的初始化阶段，它会将自己注册到驱动管理器中（static块）。
```
package com.mysql.jdbc;  
public class Driver extends NonRegisteringDriver implements java.sql.Driver {  
  
    static {  
        try {  
            java.sql.DriverManager.registerDriver(new Driver());  
        } catch (SQLException E) {  
            throw new RuntimeException("Can't register driver!");  
        }  
    }  
      ... ...  
}  
```

## 自定义类加载器的应用
### Tomcat中类加载器分析
tomcat中通过自定义一组类加载器，解决了以下几个问题：

1. 部署在一个服务器上的两个Web应用程序自身所使用的Java类库是相互隔离的。
2. 部署在一个服务器上的两个Web应用程序可以共享服务器提供的java共用类库。
3. 服务器尽可能的保证自身安全不受部署的Web应用程序影响。
4. 支持对JSP的HotSwap功能。


### tomcat的目录结构

tomcat主要根据根据java类库的共享范围，分为4组目录：
1. common目录：能被Tomcat和所有Web应用程序共享。
2. server目录：仅能被Tomcat使用，其他Web应用程序不可见。
3. Shared目录：可以被所有Web应用程序共享，对Tomcat不可见。
4. WEB-INF目录：只能被当前Web应用程序使用，对其他web应用程序不可见。


### tomcat自定义类加载器
![tomcat自定义类加载器](/images/posts/2017-08-30-classloader-3.png)

这几个类加载器分别对应加载`/common/*`、`/server/*`、`/shared/*`和` /WEB-INF/*`类库， 其中Webapp类加载器和Jsp类加载器会存在多个，每个Web应用对应一个Webapp类加载器。
CommonClassLoader加载的类可以被CatalinaClassLoader和ShareClassLoader使用；CatalinaClassLoader加载的类和ShareClassLoader加载的类相互隔离； WebappClassLoader可以使用ShareClassLoader加载的类，但各个WebappClassLoader间相互隔离；JspClassLoader仅能用JSP文件编译的class文件。

# ClassLoader重要方法 

在Java中，ClassLoader是一个抽象类，位于java.lang包中。下面对该类的一些重要接口方法进行介绍： 

`Class loadClass(String name)`

name参数指定类装载器需要装载类的名字，必须使用**全限定类名**，如com.baobaotao. beans.Car。该方法有一个重载方法loadClass(String name ,boolean resolve)，resolve参数告诉类装载器是否需要解析该类。在初始化类之前，应考虑进行类解析的工作，但并不是所有的类都需要解析，如果JVM只需要知道该类是否存在或找出该类的超类，那么就不需要进行解析。 

`Class defineClass(String name, byte[] b, int off, int len)`

将类文件的字节数组转换成JVM内部的java.lang.Class对象。字节数组可以从本地文件系统、远程网络获取。name为字节数组对应的全限定类名。 

`Class findSystemClass(String name)`

从本地文件系统载入Class文件，如果本地文件系统不存在该Class文件，将抛出ClassNotFoundException异常。该方法是JVM默认使用的装载机制。    

`Class findLoadedClass(String name)`

调用该方法来查看ClassLoader是否已装入某个类。如果已装入，那么返回java.lang.Class对象，否则返回null。如果强行装载已存在的类，将会抛出链接错误。   

`ClassLoader getParent()`

获取类装载器的父装载器，除根装载器外，所有的类装载器都有且仅有一个父装载器，ExtClassLoader的父装载器是根装载器，因为根装载器非Java编写，所以无法获得，将返回null。 

除JVM默认的三个ClassLoader以外，可以编写自己的第三方类装载器，以实现一些特殊的需求。类文件被装载并解析后，在JVM内将拥有一个对应的java.lang.Class**类描述对象**，该类的实例都拥有指向这个类描述对象的引用，而类描述对象又拥有指向关联ClassLoader的引用，如图3.4所示。 

![ClassLoader](/images/posts/2017-08-30-classloader-4.png)

每一个类在JVM中都拥有一个对应的java.lang.Class对象，它提供了类结构信息的描述。数组、枚举、注解以及基本Java类型（如int、double等），甚至void都拥有对应的Class对象。这些Class没有public的构造方法。Class对象是在装载类时由JVM通过调用类装载器中的defineClass()方法自动构造的。 


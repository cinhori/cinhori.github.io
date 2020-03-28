---
layout: post
title: Windows PowerShell笔记
categories: PowerShell
description: Learn Windows PowerShell in a Month of Lunches,Third Edition
keywords: PowerShell
---

查看PS版本:`$PSVersionTable`
更新帮助文档：`Update-Help`
获取帮助文档:
```
# man 为 help 的别称
help get-content 
help get-content -full # 获取完整的位置参数
help get-content -examples # 获取示例
help Get-EventLog -ShowWindow #在客户端获取文档
help get-content -online # 获取在线帮助
man get-content
get-help get-content | more
help *log #支持通配符
```
获取帮助：
```
get-command *event*
Get-Command -Noun *event*
Get-Command -Verb *event*
Get-Command *log* -Type Cmdlet
```

参数值：
- string:字母、数字、空格，有空格则需加引号
- int、int32、int64
- DateTime
- string[]:可接收数组、集合、列表类型的字符串

使用（）强制命令先执行

save-help -DestinationPath “存储路径”
Update-Help -SourcePath “存放离线数据的目录”

重定向：
把命令的输出保存到文件中，‘>’为覆盖，’>>’追加。  
管道：
把上一条命令的输出作为下一条命令的输入。



默认键入一个字符串，powershell会将它原样输出，如果该字符串是一个命令或者启动程序，在字符串前加‘&’可以执行命令，或者启动程序。

Cmd.exe 通过 /c 来接收命令参数，在Cmd中help可以查看可用的命令，所以可以通过Cmd /c help 查找可用的Cmd控制台命令

Get-Alias | where {$_.definition.startswith('S')}  
通过where对数组元素进行遍历，$_代表当前元素，alias的Definition为String类型，因为powershell支持.net，.net中的string类有一个方法Startswith。

Get-Alias | Group-Object Definition | sort -Descending Count

Powershell调用入口的优先级
别名：控制台首先会寻找输入是否为一个别名，如果是，执行别名所指的命令。因此我们可以通过别名覆盖任意powershell命令，因为别名的优先级最高。

函数：如果没有找到别名，会继续寻找函数，函数类似别名，只不过它包含了更多的powershell命令。因此可以自定义函数扩充cmdlet 把常用的参数给固化进去。

命令：如果没有找到函数，控制台会继续寻找命令，即cmdlet，powershell的内部命令。

脚本：没有找到命令，继续寻找扩展名为“.ps1”的Powershell脚本。

文件：没有找到脚本，会继续寻找文件，如果没有可用的文件，控制台会抛出异常。

变量：${} 大小写不敏感

查看正在使用的变量
Powershell将变量的相关信息的记录存放在名为variable:的驱动中。如果要查看所有定义的变量，可以直接遍历variable:

查找变量
因为有虚拟驱动variable:的存在，可以象查找文件那样使用通配符查找变量。例如要查询以value打头的变量名: `ls variable:value*`

验证变量是否存在
验证一个变量是否存在，仍然可以象验证文件系统那样，使用cmdlet Test-Path。为什么？因为变量存在变量驱动器中。
```
PS C:\test> Test-Path variable:value1
True
```

为了管理变量，powershell提供了五个专门管理变量的命令Clear-Variable，Get-Variable，New-Variable，Remove-Variable，Set-Variable。因为虚拟驱动器variable:的存在，clear，remove，set打头的命令可以被代替。但是Get-Variable，New-Variable。却非常有用new-variable可以在定义变量时，指定变量的一些其它属性，比如访问权限。同样Get-Variable也可以获取这些附加信息。

about_Automatic_Variables

|automatic_variable | description |
|----|----|
|$_|包含管道对象中的当前对象|
|$Args|包含由未声明参数和/或传递给函数、脚本或脚本块的参数值组成的数组。在创建函数时可以声明参数，方法是使用 param 关键字或在函数名称后添加以圆括号括起、逗号分隔的参数列表|
|$ForEach|包含 ForEach-Object 循环的枚举数。可以对 $ForEach 变量的值使用枚举数的属性和方法|
|$Home|包含用户的主目录的完整路径。此变量等效于 %homedrive%%homepath% 环境变量。|
|$PsVersionTable|包含一个只读哈希表，该哈希表显示有关在当前会话中运行的 Windows PowerShell 版本的详细信息|
|$Pwd|包含一个路径对象，该对象表示当前目录的完整路径|

子表达式: 由 $+圆括号+表达式 构成的变量属于子表达式变量，这样的变量会先计算表达式，然后把表达式的值返回。

Powershell支持四个作用域：全局、当前、私有和脚本
在运行脚本时使用一个原点和空格，Powershell解释器就不会为脚本本身创建自己的变量作用域，它会共享当前控制台的作用域，
$global
全局变量，在所有的作用域中有效，如果你在脚本或者函数中设置了全局变量，即使脚本和函数都运行结束，这个变量也任然有效。

$script
脚本变量，只会在脚本内部有效，包括脚本中的函数，一旦脚本运行结束，这个变量就会被回收。

$private
私有变量，只会在当前作用域有效，不能贯穿到其他作用域。

$local
默认变量，可以省略修饰符，在当前作用域有效，其它作用域只对它有只读权限。

类型自适应也称作“弱类型”
定义变量时可以在变量前的中括号中加入数据类型
一般专业的程序员或者脚本开发者更喜欢使用“强类型”，哪怕在赋值时类型不兼容的报错，他们也乐意接受。喜欢使用强类型的另一个原因是：每一个数据类型都有属于自己的函数。例如DateTime,和XML，尽管这两种类型都可以用纯文本表示，但是使用强类型[DateTime]和[XML],对于数据操作起来更方便

常用的对管道结果进一步处理的命令有：

Compare-Object: 比较两组对象。
ConvertTo-Html: 将 Microsoft .NET Framework 对象转换为可在 Web 浏览器中显示的 HTML。
Export-Clixml: 创建对象的基于 XML 的表示形式并将其存储在文件中。
Export-Csv: 将 Microsoft .NET Framework 对象转换为一系列以逗号分隔的、长度可变的 (CSV) 字符串，并将这些字符串保存到一个 CSV 文件中。
ForEach-Object: 针对每一组输入对象执行操作。
Format-List: 将输出的格式设置为属性列表，其中每个属性均各占一行显示。
Format-Table: 将输出的格式设置为表。
Format-Wide: 将对象的格式设置为只能显示每个对象的一个属性的宽表。
Get-Unique: 从排序列表返回唯一项目。
Group-Object: 指定的属性包含相同值的组对象。
Import-Clixml: 导入 CLIXML 文件，并在 Windows PowerShell 中创建相应的对象。
Measure-Object: 计算对象的数字属性以及字符串对象（如文本文件）中的字符数、单词数和行数。
more: 对结果分屏显示。
Out-File: 将输出发送到文件。
Out-Null: 删除输出，不将其发送到控制台。
Out-Printer: 将输出发送到打印机。
Out-String: 将对象作为一列字符串发送到主机。
Select-Object: 选择一个对象或一组对象的指定属性。它还可以从对象的数组中选择唯一对象，也可以从对象数组的开头或末尾选
择指定个数的对象。
Sort-Object: 按属性值对象进行排序。
Tee-Object: 将命令输出保存在文件或变量中，并将其显示在控制台中。
Where-Object: 创建控制哪些对象沿着命令管道传递的筛选器。

PS脚本执行权限：
- Restricted——默认的设置，不允许任何script运行
- AllSigned——只能运行经过数字证书签名的script
- RemoteSigned——运行本地的script不需要数字签名，但是运行从网络上下载的script就必须要有数字签名
- Unrestricted——允许所有的script运行

> 以管理员运行：`Set-ExecutionPolicy RemoteSigned`
> 

修改HKEY_CLASSES_ROOT\Microsoft.PowerShellScript.1\Shell\的默认为0即可双击执行脚本

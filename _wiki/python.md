---
layout: wiki
title: Python库
categories: Python
description: Python常用库
keywords: Python 
---

# 数据持久化
## pickle
pickle 实现了对一个 Python 对象结构的二进制序列化和反序列化

下列类型可以被打包：
- None、True 和 False
- 整数、浮点数、复数
- str、byte、bytearray
- 只包含可打包对象的集合，包括 tuple、list、set 和 dict
- 定义在模块顶层的函数（使用 def 定义，lambda 函数则不可以）
- 定义在模块顶层的内置函数
- 定义在模块顶层的类
- 某些类实例，这些类的 __dict__ 属性值或 __getstate__() 函数的返回值可以被打包

## configparser
读取windows中ini类型的配置文件

文件格式：
```
[DEFAULT]
ServerAliveInterval = 45
Compression = yes
CompressionLevel = 9
ForwardX11 = yes
  
[bitbucket.org]
User = Atlan
```

## csv
CSV是英文Comma Separate Values（逗号分隔值）的缩写，顾名思义，文档的内容是由 “,” 分隔的一列列的数据构成的。CSV文档是一种编辑方便，可视化效果极佳的数据存储方式。
```
import csv
# 内置

```

## pandas
The [pandas I/O API](https://pandas.pydata.org/pandas-docs/stable/user_guide/io.html) is a set of top level reader functions accessed like pandas.read_csv() that generally return a pandas object. The corresponding writer functions are object methods that are accessed like DataFrame.to_csv(). Below is a table containing available readers and writers.

|Format Type|Data Description|Reader|Writer|
|----|----|----|----|
|text| CSV| read_csv|to_csv|
|text|Fixed-Width Text File|read_fwf|
|text|JSON|read_json|to_json|
|text|HTML|read_html|to_html|
|text|Local clipboard|read_clipboard|to_clipboard|
| |MS Excel|read_excel|to_excel|
|binary|OpenDocument|read_excel|
|binary|HDF5 Format|read_hdf|to_hdf|
|binary|Feather Format|read_feather|to_feather|
|binary|Parquet Format|read_parquet|to_parquet|
|binary|ORC Format|read_orc|
|binary|Msgpack|read_msgpack|to_msgpack|
|binary|Stata|read_stata|to_stata|
|binary|SAS|read_sas|
|binary|SPSS|read_spss|
|binary|Python Pickle Format|read_pickle| to_pickle|
|SQL|SQL|read_sql|to_sql|
|SQL|Google BigQuery|read_gbq|to_gbq|
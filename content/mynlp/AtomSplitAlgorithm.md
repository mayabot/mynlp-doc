---
title: "AtomSplitAlgorithm"
draft: false
---

## AtomSplitAlgorithm

这个基于规则的分词算法，是对CWS和CORE的补充。在Core和Cws的Pipeline中都默认添加了。

特长是处理固定格式的构词逻辑。AtomSplitAlgorithm的实现非常容易扩展，而且速度超级快。

处理的模式：

* 数字
   * 整数
   * 浮点数 100.2
   * 负数
* 英文单词
  
  例如 apple
* 中文数字

  一万两千八
* 时间短语

   1998-01-01
   1998年2月3日
   8月9日
   3个月
   等等各种常见的时间格式
   
* 数量词

  一双
  两个
  三十二千克
  52赫兹
  等等N多种组合
   
* Email

### 特别之处
和Hanlp中的处理方式是不同，这么多种模式，如果用正则表达式，性能相当的差。如果用状态机实现，那么代码复杂极高。
如果用硬编码，那么代码不(you)忍(chou)直(you)视(chang)。
AtomSplitAlgorithm巧妙利用DAT和独特的模板方法，实现对原始文本一次扫描就可以识别上千个模式，速度极快。

在模式定义上，也提供了类似表达式的语法，方便扩展和自定义模式。

系统默认添加了如下模式：
```java
//时间
    treeMap["周Z"] = TIME
    treeMap["星期Z"] = TIME

    //年月日
    treeMap.addTemplate("{N[2,4]}年{N[1,2]}月{N[1,2]}{(日|号)}", TIME)
    treeMap.addTemplate("{N[2,4]}-{N[2]}-{N[2]}", TIME)

    //年
    treeMap.addTemplate("{N|Z[2,4]}{(年|年度)}", TIME)

    //年月
    treeMap.addTemplate("{N|Z[2,4]}年{N[1,2]}月", TIME)

    //月
    treeMap.addTemplate("{N|Z[1,2]}{(月|月份)}", TIME)

    //日
    treeMap.addTemplate("{N[1,2]}{(日|号)}", TIME)
    treeMap.addTemplate("{Z[1-3]}{(日|号)}", TIME)

    //数字 正负 整数 浮点数
    treeMap.addTemplate("{N[1-50]}", NUMBER)
    treeMap.addTemplate("-{N[1-50]}", NUMBER)

    treeMap.addTemplate("{Z[1-30]}", NUMBER)

    treeMap.addTemplate("{Z|N[1-10]}{(元|串|事|册|丘|下|丈|丝|举|具|包|厘|刀|分|列|则|剂|副|些|匝|队|部|出|个)}", MQ)
    treeMap.addTemplate("{Z|N[1-10]}{(介|令|份|伙|件|任|倍|儋|亩|记|双|发|叠|节|茎|通|造|遍|道)}", MQ)

    treeMap.addTemplate("{Z|N[1-10]}{(公里|米|千米|厘米|毫米|微米|纳米|飞米|km|dm|cm|mm|μm|nm|fm)}", MQ)
    treeMap.addTemplate("{Z|N[1-10]}{(kg|dg|cg|mg|公斤|斤|克)}", MQ)
    treeMap.addTemplate("{Z|N[1-10]}{(tb|gb|mb|kb|字节|兆)}", MQ)
    treeMap.addTemplate("{N[1-10]}{A[1-3]}", MQ)

    //单词
    treeMap.addTemplate("{A[2-100]}", WORD)

    //连接符号
    treeMap.addTemplate("-", CONNECT)
```

- Z代表中文数字
- N代表数字，

>企业版中提供了更完整的模式设置！
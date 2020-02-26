---
title: "人名识别"
draft: false

---
## 人名识别

底层采用感知机实现，对模型进行了优化，最终F1达94.91，如果剔除古汉语人名的影响，F1可以更高。
这里的人名识别是基于字符输入的，不需要预先分词，该模块可以脱离Lexer单独使用。

在Lexer中，和core、atom等一样都属于基础分词算法插件。

### 单独调用

```java
PerceptronPersonNameService nameService = Mynlps.instanceOf(PerceptronPersonNameService.class);
List<PersonName> names = nameService.findName("有一次孩子说要送给张贺年老师一张贺年卡");

System.out.println(names);
```

输出
```text
[PersonName(name=张贺年, offset=9)]
```
可以看到【一张贺年卡】中的【张贺年】并没有被识别为人名，具有一定的歧义解决能力。

### Core分词器中使用
在Core中已经内置了人名识别，只要开启对应功能即可。
`builder.withPersonName()`

### 内存需求
约50M左右

### 模型训练

人名识别，单线程感知机训练。
数据集混合PKU+CNCORPUS，5%作为测试集,95%来训练。

186轮次迭代

```
#ITER 186/200
train use 4442 ms

正确率(P) 95.36 , 召回率(R) 94.46 , F1 94.91
```

---
title: "用户自定义词典"
draft: false
---


### 资源文件

默认mynlp-segment没有导入资源文件。

```text
compile 'com.mayabot.mynlp.resource:mynlp-resource-custom:1.0.0'
```
MAVEN
```xml
<dependency>
  <groupId>com.mayabot.mynlp.resource</groupId>
  <artifactId>mynlp-resource-custom</artifactId>
  <version>1.0.0</version>
</dependency>
```

### 使用
```java
Lexers.coreBuilder()
      .withCustomDictionary()
```

`withCustomDictionary`方法默认会安装自定义词典，custom-dict/CustomDictionary.txt。
这个词典其实就是测试目的，没有几个词。

资源包中包含：
-  custom-dict/CustomDictionary.txt
-  custom-dict/上海地名.txt
-  custom-dict/人名词典.txt
-  custom-dict/全国地名大全.txt
-  custom-dict/机构名词典.txt
-  custom-dict/现代汉语补充词库.txt
这几个预置词典。

词典格式：

```text
上呼吸道感染
不可逆反应
不定方程
龙江剧
龙涎香
天猫
京东
```

指定使用的哪些词典：
```text
// 在程序启动之初：
Mynlps.install((mynlp -> {
            mynlp.set(DefaultCustomDictionary.dictPathSetting,
                    "custom-dict/上海地名.txt,custom-dict/现代汉语补充词库.txt");
        }));
```
这里也可以指定你项目自定义词典文件。

### 自定义词典逻辑

自定义词，只能对已经初步的分词结果进行合并操作。

"第几套房" 在第一遍分词完成后得到`第、几、套房`，此时我们自定义词典中存在"第几套",也不能改变分词结果，因为你需要合并"第几套"，必须破拆
"套房"这个词汇，这种情况是自定义词典不能做到的，这个需要在【分词纠错】中去实现。

**这就是在其他NLP工具中一直讨论自定义词典优先级的问题根源。**

用户自定义词典，只能去做合并，比如"利用碎片化的时间"，分成"利用、碎片、化、的、时间"，此时定义词典加入`碎片化`，可以影响分词结果。

也就是说不能通过自定义词典影响基础分词算法导致的错误问题，得通过其他解决方案。

### 更多的扩展

`CustomDictionary`接口的其他实现类`FileCustomDictionary` `MemCustomDictionary`，当然
你也可以自己实现接口。
下面举例MemCustomDictionary
  


```java
MemCustomDictionary customDictionary = new MemCustomDictionary();

customDictionary.addWord("新闻中心");

customDictionary.rebuild();

Lexer lexer = Lexers.coreBuilder()
        .withCustomDictionary(customDictionary)
        .build();

System.out.println(lexer.scan("新闻中心的电话是什么"));
```

当Lexer创建后，你可以继续对MemCustomDictionary的内容进行变化，调用`build()`后生效。

通过这个例子，可以解决下面几个问题：

- 自定义词典存储在数据库中
- 及时生效
- 多用户场景。多Lexer实例，每个实例都有独立的自定义词典
- 还有我没有想到的场景:)


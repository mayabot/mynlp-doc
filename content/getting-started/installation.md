---
title: "安装"
draft: false
weight: 1
---
mynlp多个功能被划分在不同的模块中，下面演示分词模块：

GRADLE
```
compile 'com.mayabot.mynlp:mynlp-segment:3.0.1'
```
或者MAVEN
```xml
<dependency>
  <groupId>com.mayabot.mynlp</groupId>
  <artifactId>mynlp-segment</artifactId>
  <version>3.0.1</version>
</dependency>
```

模块（artifactId） | 功能 
------ | ------------
mynlp-core | 基础功能 Guice、logger、资源、基础数据结构和算法
mynlp-perceptron | 通用序列标注感知机
mynlp-segment | 分词
mynlp-classification | 文本分类
mynlp-lucene | lucene 分析器接口实现
mynlp-pinyin | 文字转拼音
mynlp-summary | 文本摘要
mynlp-transform | 繁简体互转

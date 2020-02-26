---
title: "结构化感知机"
draft: false
weight: 6
description: "平均感知机和结构化感知机"
---

感知机参考[《基于结构化平均感知机的分词器Java实现》](http://www.hankcs.com/nlp/segment/implementation-of-word-segmentation-device-java-based-on-structured-average-perceptron.html)

先不管感知机具体算法和数学原理，这个工具的作用就是序列标注学习和解码。
```
假定序列：
a1 a2 a3 a3 a.. an
每个序列对应标注代码:
0 0 1 2 2 3 4 .. 1

感知机可以学习标注行为，然后未知的输入：
b1 b2 b3 bn
进行估计对应标签代码。
```

这就是感知机抽象的功能，就是一种快速高效的序列标注技术。对应的其他技术有CRF(条件随机场)、LSTM等。

我们可以把中文分词、词性标注、命名实体识别等问题转换为序列标注解码过程。

```xml
<dependency>
  <groupId>com.mayabot.mynlp</groupId>
  <artifactId>mynlp-perceptron</artifactId>
  <version>{{< version >}}</version>
</dependency>
```


是一个通用的、高性能感知机框架。mynlp其他感知机分词、词性标注、人名识别、NER等都是基于这个框架开发的。
当然你可以基于这个框架解决其他序列标注问题，下面我们将演示如果切分拼音流。(wanzhengdepinyin => wan zheng de pin yin)

你只需告知感知机框架两件事情：
- 特征提取函数
- label编码
- 原始语料如果转换为(输入=标签)二元组
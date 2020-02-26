---
title: "基础组件"
draft: false
---


## CharNormalize
接口定义如下：
```java
interface CharNormalize {
    /**
     * 对char数组里面的字符进行规范化操作，常见的有最小化和宽体字符处理
     * @param text
     */
    void normal(char[] text);
}
```
MynlpTokenizer处理的是char[]，CharNormalize是在分词正式计算之前可以对char进行转换。

CharNormalize实现类 | 功能
--- | ---
LowerCaseCharNormalize | 字母小写化
Full2halfCharNormalize | 全角字符转半角字符
UpperCaseCharNormalize | 字母大写化

在WordnetTokenizerBuilder中默认启用了LowerCaseCharNormalize和Full2halfCharNormalize。

## WordnetInitializer
接口定义如下：
```java
interface WordnetInitializer extends MynlpComponent {
    void fill(Wordnet wordnet);
}
```

WordnetInitializer实现类 | 功能
--- | ---
CoreDictionaryInitializer | 根据CoreDictionary分词
CrfBaseSegmentInitializer | CRF算法分词
AtomSegmenterInitializer | 识别出字符串、数字（浮点数）、等固定模式。
ConvertAbstractWordInitializer | 给一些合成词添加词性
TimeStringProcessor | 简单的时间词汇

## WordpathProcessor


WordpathProcessor实现 | 功能
--- | ---
CombineProcessor | 通用的合并处理器，Email、书名、单词数字连接符
CorrectionProcessor | 分词纠错
CorrectionProcessor | 分词纠错
CustomDictionaryProcessor | 自定义词典
CustomPatternProcessor | 自定义正则表达式合并处理器
PartOfSpeechTaggingComputerProcessor | 词性分析

## OptimizeWordPathProcessor
OptimizeWordPathProcessor是WordpathProcessor一个特殊实现，它是一个容器，包含了
一组OptimizeWordPathProcessor接口的实现。
他们内部包含了多个识别器。

OptimizeWordPathProcessor | 功能
--- | ---
PlaceRecognition | 地名识别
OrganizationRecognition | 组织结构识别
PersonRecognition | 人名识别

之所以需要把它们合并在一起，是因为他们会导致不同的分词路径产生，还需要进行一次路径选择算法来调和。

## WordTermCollector
WordTermCollector实现类 | 功能
--- | ---
SentenceCollector | 收集唯一确定的分词结果。比如把"商品和服务"收集为 \[商品 , 和, 服务\]
SentenceIndexWordCollector | 面向索引分词收集器，把超过一定长度的词进一步细分。

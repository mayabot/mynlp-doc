---
title: "核心概念"
draft: false
---

在涉及具体分词算法之前，mynlp先进行了接口抽象，主要目的是让分词逻辑组件化、插件化。

## 基础接口介绍
接口|描述
--- | ---
CharNormalize | 字符规则化
MynlpAnalyzer | 面向Reader的分词器
MynlpTokenizer | 面向String的分词器
MynlpTokenizerBuilder | MynlpTokenizer构建器
WordSplitAlgorithm | 分词算法
WordpathProcessor | wordpath处理器
BestPathAlgorithm | Wordnet最优路径算法
WordTermCollector | 结果收集器，从Wordnet和WordPath中提取分词结果

这些接口和下面介绍的wordnet、wordpath都和具体的特定的分词算法无关，都是为了规范和统一分词过程而设计的。

## PipelineTokenizer

`MynlpTokenizer`是分词器的接口定义，但是我们并不打算为每一个分词器算法实现一天特别的Class，原因有很多，最主要的是
不同分词器之间会有很多重复的功能代码。我们这里只提供一个实现`PipelineTokenizer`。

PipelineTokenizer本身并不关心分词算法和业务逻辑是什么，它把分词逻辑分拆委托给其他接口，它只是负责执行流程控制。
也就是利用管线把各种部件组合起来，mynlp内部预先实现了各种部件，如email、自定义词典、纠错、词性分析等。系统的扩展性
也正是得益于这这pipeline模式，您可以自由装配和扩展实现。

PipelineTokenizer由WordSplitAlgorithm、WordpathProcessor（多个）、WordTermCollector、BestPathAlgorithm组成。



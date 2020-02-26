# Lexer 词法分析器

## 架构设计

分词算法：
- 基础分词算法：
    - 核心词典 + 二元语言模型
    - 感知机分词
    - 原子分词：
            - 日期识别
                - 包含各种常见日期描述
            - 数字识别
                - 整数
                - 浮点数
                - 负数
                - 中文数字
            - 英文单词
            - 连接符号
            - 数量词
- 功能：
    - 自定义词典
    - 分词纠错
    - 词性分析
    - 子词二次切分
    - 正则表达式
    - 人名识别
    - NER命名实体识别
    - 索引分词模式

在ansj、hanlp一个特定分词器的实现通常是由一个固定的Class实现，比如`StandardTokenizer`、`NLPTokenizer`等，
每个分词器可以控制开关去开启特性。

这种实现方式的缺点在于：
- 代码重复
- 代码重用复杂
- 不灵活
    - 比如：融合感知机分词和原子分词
    - 融合感知机分词和分词纠错
- 自定义扩展困难
    - 比如： 每个分词器实例采用不同的自定义词典
    - 扩展自定义分词规则插件
    - 扩展原子分词模式
    - 替换新的NER分词算法
    - 从数据库加载自定义词典
- 处理规则分词和基础分词算法直接冲突

本着开闭原则，Mynlp采用面向接口、Pipeline + WordNet、Path数据结构，把各个功能串联起来，
按需使用Builder模式构建个性化分词器实例。
比如hanlp里面的急速分词，在这里你可以构建基于核心词典、关闭其他特性、替换最优路径选择算法，获得
自定义Lexer实例，而无需创建一个具体的Class。

换句话说，每个模块都关注自己的算法实现，具体用户可以自由创建、组合、自定义分词器实例。也可以
升级、替换、扩展其中某个部分的接口实现，你可以实现一个全新的基础分词算法，就自动获得自定义扩展、纠错、NER等功能。



## Lexer

`Lexer`是词法分析器接口，对一段文字进行词法分析计算。

类`PipelineLexer`是Lexer的默认实现，`FluentLexerBuilder`是快捷方便的构建器。


```java
Lexer lexer = Lexers.builder()
        .basic().core()
        //.basic().cws()
        .withPos()
        .withNer()
        .withPersonName()
        .with(plugin);
```


### PipelineLexer

PipelineLexer结构和计算过程非常简单，它统一了分词计算过程和步骤。

步骤：
1. 对输入的String，构建Wordnet
2. 调用WordSplitAlgorithm填充Wordnet
3. 调用BestPathAlgorithm选择一个WordPath
4. 按顺序调用WordpathProcessor处理WordPath
5. WordTermCollector读取WordPath和Wordnet信息，输出分析结果

![Pipeline](images/mynlp-pipeline.png)

组件接口 | 描述
--- | ---
WordSplitAlgorithm | 基础分词算法
WordpathProcessor | 对已经确定的一条分词路径进行处理的逻辑
BestPathAlgorithm | 从Wordnet中选择一条最优路径算法
WordTermCollector | 结果收集器，从Wordnet和WordPath中提取分词结果。

*[Wordnet和WordPath数据结构](wordnet)*



## LexerReader

Lexer是面向String的接口，处理长度有限的输入。在对长篇文章进行词法分析时，会有内存和性能问题。

LexerReader是面向Reader的处理接口，同时也提供了停用词过滤器、标点过滤等功能。

```java

Lexer lexer = Lexers.core();

LexerReader reader = lexer.reader();
// LexerReader reader = lexer.filterReader(true,true);  // 开启过滤

WordTermSequence seq = reader.scan(new StringReader("小明送给张贺年老师一张贺年卡"));

seq.iterator();
seq.stream();
seq.toSentence();
seq.toWordSequence();
```

LexerReader把reader一段一段读进来，逐步使用Lexer进行分词计算。`WordTermSequence`是一个
流式计算接口，一边迭代一边计算，这样就没有内存溢出问题。

---
title: "起步"
draft: false
weight: 1
---
# Quick Tutorial


### Installation

mynlp由多模块构成，每个模块都发行JAR到maven仓库中（`groupId=com.mayabot.mynlp`），您可以直接在项目中引用。

模块（artifactId） | 功能描述
------------ | -------------
mynlp-core | 基础功能 Guice、logger、资源、基础数据结构和算法
mynlp-segment | 中文分词
mynlp-pinyin | 文字转拼音
mynlp-transform | 繁简体互转
mynlp-classification | 文本分类
mynlp-summary | 文本摘要
mynlp-perceptron | 感知机

如使用分词功能，只需要引入:
```
    <dependency>
      <groupId>com.mayabot.mynlp</groupId>
      <artifactId>mynlp-segment</artifactId>
      <version>2.0.0</version>
    </dependency>
```
> version请使用Maven仓库中最新版本

> 如果你比较懒，引入`mynlp-all`一个JAR包，即可包括分词、拼音、感知机、简繁体这些功能。

## 分词

### Core分词

```java
MynlpTokenizer tokenizer = Tokenizers.coreTokenizer();
Sentence sentence = tokenizer.parse("早上好");
System.out.println(sentence.asWordList());
```

coreTokenizer返回的是CORE分词器，它以二元语言模型+Viterbi为基础的分词算法，特点是高性能、排除歧义、高度自定义，
适应大部分场景。

`MynlpTokenizer`是分词算法的通用接口，它是多线程安全的，相同功能的MynlpTokenizer只需要一个实例即可。
MynlpTokenizer接口关注的是如何把长度有限的一小段文本进行分词。

MynlpTokenizer关注对有限长度的字符串进行分词计算，比如一句话分词。

那么如果需要对打断文本进行分词，那么MynlpTokenizer就不能胜任了，因为它会消耗不确定大的内存。
`MynlpAnalyzer`是面向Reader的分词接口，它关注无限长度的文本进行分词，同时提供过过滤器功能（停用词和标点符号）,是对MynlpTokenizer接口的上层包装。

```java
MynlpAnalyzer analyzer = new DefaultMynlpAnalyzer(Tokenizers.coreTokenizer());
Reader reader = new StringReader("假装这是一个大文本");
Iterable<WordTerm> result = analyzer.parse(reader);
```

>MynlpAnalyzer也是线程安全的，相同逻辑单例即可。MynlpAnalyzer返回的Iterable或Stream是延迟计算的。

### CWS感知机分词
```java
MynlpTokenizer tokenizer = Tokenizers.cwsTokenizer();
Sentence sentence = tokenizer.parse("委内瑞拉总统马杜罗表示他支持议会选举提前到今年举行");
System.out.println(sentence.asWordList());
```
感知机分词器是面向字符序列标注的分词器算法。cwsTokenizer方法返回一个新的感知机分词器实例。

如你所见和Core分词示例相比，只是Tokenizers构建方法调用不一样，其他的API都保持不变。

### 自定义选项
Tokenizers.coreTokenizer和cwsTokenizer只是返回一个预定义的分词算法实例。你还可以通过Builder创建自定义选项的分词器。
```java
MynlpTokenizer tokenizer = Tokenizers.coreTokenizerBuilder().setEnablePOS(false).build();
```
阅读【中文分词】章节的文档，了解更多高级定制的方法和技巧。


## 文本分类

文本分类使用了Facebook的FastText算法，java版本fasttext实现已经剥离到另一个项目[fastText4j](https://github.com/mayabot/fastText4j)。
这里提供一个开箱即用的分类API，你只需要给出无需预先分词语料即可训练分类模型。

语料的格式可以是：
>__label__pos 一般设施不错其他一般早餐也不错<br/>
__label__neg 服务极差价格高地点环境偏房间条件能有一星 

其中__label__是分类标签的前缀。

```java
FastText model = FasttextClassification.train(trainFile, 100, 0.05, 20);
model.saveModel("example.data/hotel.model");
```
第一行是训练模型，第二行是保存模型文件，就是这么简单。这里给出的三个参数是向量维度、学习率、epoch。
你可以通过TrainArgs传递更为复杂的参数给训练器，这些参数和Facebook原版的Fasttext算法一致。

有了训练模型，我们就可以进行预测了。
```java
FastText model = FastText.loadModel("example.data/hotel.model", false);
String result = FasttextClassification.predict(model, "两间房只给两条毛巾加一条毛巾要收费5元太过份吧");
```
第一行加载模型，第二行就可以进行预测了。

Fasttext除了进行文本分类外，另外很大的作用是进行词向量训练。


## 拼音

如果你么有自定需求，那么只需如下调用：
```java
PinyinResult result = Pinyins.convert("招商银行推出朝朝盈理财产品");
```

PinyinResult是转换结果，你可以通过这对象，返回完整拼写列表、缩写列表、是否归一模糊拼音等结果。
pinyin模块还支持配置扩展多音词词典文件、动态添加多音词等功能，详见[文字转拼音](Pinyin)。


## 简繁转换

```java
Simplified2Traditional s2t = TransformService.simplified2Traditional();
s2t.transform("软件和体育的艺术");
```

繁体转简体
```java
Traditional2Simplified t2s = TransformService.traditional2Simplified();
t2s.transform("軟件和體育的藝術");
```

## 文本摘要
文本摘要包含了两个简单TextRank的实现。

关键字摘要
```java
KeywordSummary keywordSummary = new KeywordSummary();
keywordSummary.keyword("text",10);
```

句子摘要
```java
SentenceSummary sentenceSummary = new SentenceSummary();
List<String> result = sentenceSummary.summarySentences(document, 10);
```

KeywordSummary和SentenceSummary内置了默认的分词实现，你可以配置自定义的MynlpAnalyzer对象。

---
title: 文本摘要
draft: false
weight: 30
TableOfContents: true
---

## 安装

Maven:
```xml
<dependency>
  <groupId>com.mayabot.mynlp</groupId>
  <artifactId>mynlp-summary</artifactId>
  <version>{{<version>}}</version>
</dependency>
```

Gradle:

```gradle
compile 'com.mayabot.mynlp:mynlp-summary:{{<version>}}'
```


## API

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

KeywordSummary和SentenceSummary内置了默认的分词实现，你可以配置自定义的Lexer对象。
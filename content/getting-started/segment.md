---
title: "API概览"
draft: false
weight: 2
---


Kotlin:
```kotlin
println("mynlp是mayabot开源的中文NLP工具包。".lexer().toList())
```

Java:
```java
Lexer lexer = Lexers.coreBuilder()      //core分词构建器
                     .withPos()         //开启词性
                     .withPersonName()  //开启人名
                     .build();          // 创建定制词法分析对象
                     
Sentence sentence = lexer.scan("mynlp是mayabot开源的中文NLP工具包。");

System.out.println(sentence.toList());
```

输出：
```text
[mynlp/x, 是/v, mayabot/x, 开源/v, 的/u, 中文/nz, nlp/x, 工具包/n, 。/w]
```
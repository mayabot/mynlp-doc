# 索引分词
mynlp中没有专门的索引分词器，是在WordTermCollector中实现这个功能的。

```java
 Lexer mynlpTokenizer = Lexers.
                 coreBuilder()
                 .collector().indexPickup().done()
                 .build();
```

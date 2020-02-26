# MynlpAnalyzer
MynlpAnalyzer面向Reader的处理器，同时也提供了过滤器等功能。

MynlpAnalyzers提供了常用的构建方法：

方法 | 描述
--- | ---
standard(MynlpTokenizer tokenizer) | 提供停用词、标点符号过滤
base(MynlpTokenizer tokenizer) | 什么都不过滤
noPunctuation(MynlpTokenizer tokenizer) | 只是排除标点符号

MynlpAnalyzer中内置一个分段器，从Reader读取字符序列，然后切分为一段一段的文本。
再把这些文本丢给MynlpTokenizer去做实际的分词处理。

MynlpAnalyzer可以返回Iterable<WordTerm>或者Stream<WordTerm>对象，你可以进一步处理，注意的是，
这里Iterable或者Stream是延迟计算的，并没有一次性把Reader中所有的文字全部分词。


## 创建自定义MynlpAnalyzer实现
只需继承BaseMynlpAnalyzer，实现warp方法即可。
```java
public class YourCustomAnalyzer extends BaseMynlpAnalyzer {
    public StandardMynlpAnalyzer(MynlpTokenizer tokenizer) {
        super(tokenizer);
    }
    public StandardMynlpAnalyzer() {
        this(MynlpTokenizers.coreTokenizer());
    }

    @Override
    protected WordTermGenerator warp(WordTermGenerator base) {
        base = new PunctuationFilter(base);
        base = new StopwordFilter(base);
        return base;
    }
}
```

比如StopwordFilter中默认使用了内置的停用词词典，你可能希望使用自己的，
```java
StopwordFilter(WordTermGenerator base, Set<String> stopwords)
```
给StopwordFilter传递一个Set<String>，包含你自定义的停用词设置。
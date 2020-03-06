---
title: "安装"
draft: false
weight: 1
---

最新版本 `{{<version>}}`

#### GRADLE
```groovy
compile 'com.mayabot.mynlp:mynlp:{{<version>}}'
```

#### MAVEN
```xml
<dependency>
  <groupId>com.mayabot.mynlp</groupId>
  <artifactId>mynlp</artifactId>
  <version>{{<version>}}</version>
</dependency>
```

> 'com.mayabot.mynlp:mynlp:{{<version>}}' 中包含主要功能和部分词典资源的依赖。
> 其他功能如拼音、fasttext等被独立打包、按需依赖。

## 功能模块、词典&模型资源

### Jar
模块 | 功能描述 
------ | ------------
`com.mayabot.mynlp:mynlp:{{<version>}}` | mynlp主要功能和基础算法包
`com.mayabot.mynlp:fastText4j:{{<version>}}` | fastText算法包
`com.mayabot.mynlp:mynlp-classification:{{<version>}}` | 文本分类
`com.mayabot.mynlp:mynlp-lucene:{{<version>}}` | lucene 分析器接口实现
`com.mayabot.mynlp:mynlp-pinyin:{{<version>}}` | 文字转拼音
`com.mayabot.mynlp:mynlp-summary:{{<version>}}` | 文本摘要
`com.mayabot.mynlp:mynlp-transform:{{<version>}}` | 繁简体互转

### 词典和模型资源

mynlp中模型和词典文件被封装在jar中，发布到maven中央仓库。你可以使用`unzip`命令解压查看里面的内容。
> 由于感知机分词模型太大，所以没有被默认依赖。

Gradle地址 |依赖| 大小 |功能描述 
------ | --- | -- | ----
`com.mayabot.mynlp.resource:mynlp-resource-coredict:1.0.0`| Y | 18.2M | 核心词典（词典和二元语言统计|格式：明文）
`com.mayabot.mynlp.resource:mynlp-resource-pos:1.0.0`| Y | 17.5M | 词性标注模型（感知机模型）
`com.mayabot.mynlp.resource:mynlp-resource-ner:1.0.0`| Y | 13.4M | 命名实体识别（感知机模型）
`com.mayabot.mynlp.resource:mynlp-resource-cws:1.0.0`| *N* | 62.4M | 感知机分词模型
`com.mayabot.mynlp.resource:mynlp-resource-custom:1.0.0`| *N* | 2.2M | 补充自定义词库

{{< panel status="notice" title="依赖说明" >}}
依赖为Y: 表示这些jar资源被`com.mayabot.mynlp:mynlp:{{<version>}}`自动依赖。<br>
依赖为N: 如果需要，你可以在maven或者Gradle中引入。 
{{< /panel >}}
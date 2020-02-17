---
title: 拼音转换
description: 中文文字转拼音API
draft: false
weight: 10
TableOfContents: true
---

## 安装

Maven:
```xml
<dependency>
  <groupId>com.mayabot.mynlp</groupId>
  <artifactId>mynlp-pinyin</artifactId>
  <version>{{<version>}}</version>
</dependency>
```

Gradle:

```gradle
compile 'com.mayabot.mynlp:mynlp-pinyin:{{<version>}}'
```

文字转拼音本质上是根据词典进行转换的，底层采用AhoCorasickDoubleArrayTrie进行高性能识别和转换的。
AhoCorasickDoubleArrayTrie和拼音转换的基本逻辑是来自Hanlp，这里我们优化了API、词典加载、动态加载等实现。

## 简单使用
```java
PinyinResult result = Pinyins.convert("招商银行推出朝朝盈理财产品");

System.out.println(result.asList());
System.out.println(result.asHeadList());
System.out.println(result.asHeadString());
System.out.println(result.asHeadString(""));
System.out.println(result.asString());
System.out.println(result.asString("|"));
```
返回如下：
```text
[zhao, shang, yin, hang, tui, chu, chao, chao, ying, li, cai, chan, pin]
[z, s, y, h, t, c, c, c, y, l, c, c, p]
z s y h t c c c y l c c p
zsyhtcccylccp
zhao shang yin hang tui chu chao chao ying li cai chan pin
zhao|shang|yin|hang|tui|chu|chao|chao|ying|li|cai|chan|pin
```

你还可以进行通过这些方法进行控制：

Method | 描述
--- | ---
skipNull | 默认false。 忽略null值
ignorePunctuation | 默认true。忽略标点
fuzzy | 模糊拼音归一化

## 指定拼音词典扩展
默认的拼音词典总归是不全面的或存在错误，你可以指定自己的拼音扩展。
```java
        Mynlps.install(builder -> {
            builder.set(PinyiService.pinyinExtDicSetting, "pinyin.txt");
        });
```
资源文件pinyin.txt的存储位置，参见[资源文件加载](resources)。一般你可以放在${dataDir}下面的
pinyin.txt。
关于数据目录dataDir请参照[系统架构与设置](system)

扩展文件的格式是：
```text
还款=huan2,kuan3
朝朝盈=zhao1,zhao1,yin2
```

>系统默认的拼音词典的在开源项目[mynlp-resources](https://github.com/mayabot/mynlp-resources)中提供，
>如果大家发现错误或者需要新增，欢迎大家通过ISSUE或者代码PULL Request提交更多的拼音扩展，大家一起把这个库补充完善。

## 动态拼音词典扩展
有一些系统比较实时，拼音修正的词典可能存储在数据库里面，需要修改后及时生效。

```java
PinyinDictionary pinyinService = Mynlps.getInstance(PinyinDictionary.class);

CustomPinyin customPinyin = pinyinService.getCustomPinyin();
customPinyin.put("朝朝盈", "zhao1,zhao1,yin2");

//生效
pinyinService.rebuild();
```

CustomPinyin可以put和remove字典信息。最后调用rebuild方法进行生效。

其实Pinyins只是对`pinyinService.text2Pinyin`方法的调用代理。


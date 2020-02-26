---
title: "感知机分词器"
draft: false
---
## 感知机分词器

感知机分词是基于字符序列标注的，mynlp提供了一个基于语料库字数7000万训练得到的模型。压缩后模型大小65M。
运行时占内存185M。


```java
Lexers.perceptronBuilder()
       .withPersonName()
       .withNer()
       .withPos()
       //.withCustomDictionary()
        .build();
```
构建一个感知机为基础算法的Lexer实例对象。

### 资源包
默认mynlp-segment包没有默认导入资源JAR。

```text
compile 'com.mayabot.mynlp.resource:mynlp-resource-cws:1.0.0'
```
MAVEN
```xml
<dependency>
  <groupId>com.mayabot.mynlp.resource</groupId>
  <artifactId>mynlp-resource-cws</artifactId>
  <version>1.0.0</version>
</dependency>
```

JAR文件稍大，最后参照[词典和模型资源](resource)中maven仓库国内镜像，加快下载。

模型基于语料库字数7000万训练得到的模型。压缩后模型大小65M。运行时占内存185M。

### CwsService
核心底层服务是由CwsService直接提供的。

```java
CwsService cwsService = Mynlps.instanceOf(CwsService.class);
List<String> strings = cwsService.splitWord("非受限的调度器是一种高级机制");
```

### 在线学习

感知机一个优点在于支持在线学习。
```java
        System.out.println(cwsService.splitWord("下雨天地面积水"));

        cwsService.getPerceptron().learn("下雨天 地面 积水");

        System.out.println(cwsService.splitWord("下雨天地面积水"));
```

### hanlp对比
感知机算法实现参考自hanlp，这里独立提取了感知机逻辑，并优化了性能，也提供了易用的自定义框架，参考[感知机](perceptron)

在分词模型训练部分，吸取hanlp感知机不能很好处理空格的问题，在训练语料上做了hack，能更好地处理文本中空格对分词结果的
影响。同时在训练模型的语料中，尽量统一时间、数量词的一致性。

>一般分词语料中使用空格作为词之间的分割符号，造成训练语料中根本不会遇见空格字符，所以在实际分词时就不认识空格。
>mynlp中的做法是，对分词语料进行预处理使用﹍代替空格，按概率随机转换一定数量的逗号、句号为空格。

### 训练模型

语料格式如下：
```text
我们﹍已经﹍知道﹍,﹍在﹍地壳﹍里﹍,﹍硅﹍的﹍含量﹍在﹍所有﹍元素﹍中﹍居﹍第二﹍位﹍ ﹍仅次于﹍氧﹍。
我们﹍知道﹍,﹍在﹍18﹍世纪﹍以前﹍,﹍在﹍科学﹍的﹍视野﹍里﹍ ﹍宇宙﹍实际上﹍只是﹍太阳系﹍。
```

训练代码（kotlin）

```kotlin
 val trainer = CWSPerceptronTrainer()

val trainFile = File("data.work/cws/pku") //语料文件所在目录
val evaluateFile = File("data.work/cws/pku/199801.txt") //评估语料

val cws = trainer.train(
        trainFile,
        evaluateFile,
        150, 2)

println("compress")
cws.compress(0.2, 1e-3) //压缩

println("After compress ...")
CWSEvaluate.evaluate(evaluateFile.readLines(), cws)

cws.save(File("cws-model"))

```

### 性能
在2.6 GHz Intel Core i7的Mac pro上，预热一遍后，对红楼梦文本2.6M文件进行测试。<br>

>耗时 777 ms , 速度 1153450字/秒

速度为115万字/秒,是Core算法的1/2左右。


和其他分词器是对比是需要注意的是，上面的代码逻辑包含了对数字、英文、中文数字、量词、email、日期等固定模式的处理，
mynlp在固定模式规则上特有的处理方式给性能提供了绝对的保证。

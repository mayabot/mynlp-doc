## 地名和组织机构命名实体

同样也是使用感知机实现。

```java
PerceptronNerService nameService = Mynlps.instanceOf(PerceptronNerService.class)
```

PerceptronNerService的输入是分词结果和词性分析结构。

### 安装

`builder.withNer()`

### 模型训练

在PKU 98 + cncorpus 两个数据集训练，包含了NT NS的训练。
把数据进行随机混合，选择%5的数据作为测试集。

Sample Size 37242

单线程训练。迭代130轮

```
#ITER 130/200
train use 1458 ms

 NER	     P	     R	    F1
avg.	 91.22	 90.45	 90.83
  ns	 85.09	 81.31	 83.16
  nt	 91.92	 91.53	 91.73
```
在NS地名识别 F1 83.16
在NT组织结构识别 F1 91.73
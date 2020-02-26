---
title: "词性标注"
draft: false
weight: 2
---

{{< panel status="warning" title="提醒" >}} 词性标注API包含在mynlp-segment.jar中。{{< /panel >}}

mynlp词性分词使用的是感知机标注。

这样设计的优点在于：
- 与核心词典解耦
- 未登录词的词性
- 功能相对独立

底层调用了`PerceptronPosService`进行计算。
```java
 PerceptronPosService service = Mynlps.instanceOf(PerceptronPosService.class);

System.out.println(service.pos(Lists.newArrayList("中国 交响乐团 谭利华 在 布达拉宫 广场 演出".split(" "))));
        
```
输出

```text
[ns, n, nr, p, ns, n, v]
```

### 安装使用

字Builder中调用`withPOS()`即可安装词性分析插件。

### 和Hanlp的算法差别
mynlp的感知机设定了快速解码的模式，也就是忽略词性前后的关联关系，词性只是和当前词，前后词的内容相关，
而和前后词的词性无关。在这个假设的下，词性解码性能提高数十倍。

感知机解码复杂度和label的数量有关，如果只是BMES4标签，使用viterbi解码，性能尚可。但是label标签
数量超过50个时，计算复杂度陡然上升。

测试下来，相同的语料下，采用快速解码，准确度性能几乎没有损失。


### 内存需求
运行时内存200M

### 模型训练
词性模型也是mynlp在语料库上训练所得，模型大小18M。

资源文件mynlp-resource-pos.jar默认被mynlp-segment依赖，如果你的项目中不需要词性分析，可以排除此依赖。

数据源是PKU和cncorpus库。
准确率从PKU96%降低到93%是因为cncorpus里面人工标注的准确率有下降，也不是同一个时代的标注。

```
val model = POSPerceptronTrainer().train(File("data"), 100, 4)
```

```text
#ITER 100/100
P = 0.930
use 538902 ms

--------------------
P = 0.930
```

## 词性表

mynlp采用的词性标注集，是在北大词性标注集（也是PKU语料库）做了一些少许扩展。

代码 | 名称 | 例
--- | --- |---
a | 形容词
Ag| 形语素
ad| 副形词
an| 名行词
b| 区别词 
Bg|区别语素
c| 连词
d| 副词
Dg| 副语素
e| 叹词
f| 方位词
h| 前接成分
i| 成语
j| 简称略语
k| 后接成分
l| 习用语
m| 数词
Mg|数词语素|甲/Mg
n| 名词
nr| 人名
ns| 地名
nt| 机构团体
nx| 外文字符串|ＶＣＤ/nx
nz| 其他专名
Ng| 名语素
o| 拟声词
p| 介词
q| 量词
r|代词
Rg|代词语素|诸/Rg 学者/n
s|处所词
t|时间词|去年/t
Tg|时间语素
u|助词
v|动词
vd|副动词
vn|动名词
Vg|动语素
w|标点符号
y|语气词
Yg|语气语素|唯/d 大力/d 者/k 能/v 致/v 之/u 耳/Yg
z|状态词
--下面是--|--扩展--
xx|非语素字。 ict
mq|数量词
x|字符串。同nx
begin|句子开头
end|句子结尾
newWord| 新词

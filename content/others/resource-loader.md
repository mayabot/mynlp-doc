---
title: "资源加载器"
draft: false
---

MynlpEnv对象负责从系统中加载文件资源，包含三个默认加载器：
- File
- JARFile
- classpath

当你通过maven依赖导入资源文件jar后，classpath加载器会加载到这些资源。

由于一些其他的情况，不希望从classpath中依赖，比如FAT Jar打包过大。

### mynlp.data

资源加载器File和JARFile是从${mynlp.data}指定的目录去加载资源文件，
默认 ${mynlp.data} = ~/mynlp.data

假设加载资源路径`core-dict/CoreDict.txt`,
```
mynlpEnv.loadResource("core-dict/CoreDict.txt")
```

- FileLoader 尝试访问 ~/mynlp.data/core-dict/CoreDict.txt
- JARFile 尝试访问 ~/mynlp.data/*.jar/core-dict/CoreDict.txt

如果你打算从mynlp.data中加载资源文件，记得从segment依赖中exclude这些资源包
```
    compile group: 'com.mayabot.mynlp', name: 'mynlp-segment', version: '3.0.0-beta.1'
    {
        exclude group: "com.mayabot.mynlp.resource"
    }
```

### 自定义NlpResourceFactory
用户自定义NlpResourceFactory，以实现自定义策略，如从数据库中加载词典内容。

mynlp资源加载默认策略，是从各个地方加载文件类型的资源。如果你需要从数据库、url或其他环境中加载资源，你可以通过实现
NlpResourceFactory来自定义文件资源加载策略，比如从数据库、HDFS、redis等来源。

自定义的NlpResourceFactory安装方法如下：
```java
Mynlps.install(mynlpBuilder -> {
            mynlpBuilder.addResourceFactory(yourNlpResourceFactory);
        });
```

比如当你需要从mynlp数据库中加载自定义分词词典，只需要实现一个接口就可以完成对mynlp的扩充了。
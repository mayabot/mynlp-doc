---
title: 繁简体转换
draft: false
weight: 40
TableOfContents: true
---
## 安装

Maven:
```xml
<dependency>
  <groupId>com.mayabot.mynlp</groupId>
  <artifactId>mynlp-transform</artifactId>
  <version>{{<version>}}</version>
</dependency>
```

Gradle:

```gradle
compile 'com.mayabot.mynlp:mynlp-transform:{{<version>}}'
```

## API

```java
Simplified2Traditional s2t = TransformService.simplified2Traditional();
s2t.transform("软件和体育的艺术");
```

繁体转简体
```java
Traditional2Simplified t2s = TransformService.traditional2Simplified();
t2s.transform("軟件和體育的藝術");
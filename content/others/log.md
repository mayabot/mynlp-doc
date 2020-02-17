---
title: "日志服务"
draft: false
---

Mynlp中日志输出，不是简单打印到控制台，或者指定使用某个日志系统。
我们借鉴了Elasticsearch中适配各个日志组件的方法，这样设计的目的是兼容你项目的环境的日志配置文件
，避免多个不同日志系统的冲突。


支持如下日志系统API：

日志 | 备注
--- | ---
JdkLogger    | java.util.logging.Logger
Log4JLogger  | log4j 1.x
Log4J2Logger | log4j 2.x，logback
Slf4JLogger  | slf4j


Mynlp使用了一个Logger抽象层，系统运行时去Classpath查找各种Logger的实现类，如果找到了就会使用对应的实现。
所以你可以自由地选择Logger实现。无需配置，自动检测适配。

比如在你的工程中使用logback依赖：
```
compile group: 'ch.qos.logback', name: 'logback-classic', version: '1.2.3'
```

mynlp会自动使用logback输出日志。
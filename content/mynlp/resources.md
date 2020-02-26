---
title: "资源加载"
draft: false
weight: 2
---
## 资源加载


资源文件被独立发布到group=com.mayabot.mynlp.resource下。

Name(artifactId) | Version | 尺寸 |desc | 默认导入
------ | ----- | --- | --- | ---
mynlp-resource-coredict | 1.0.0 | 19.1M | 核心词典 | 是
mynlp-resource-pos | 1.0.0 | 18.4M | 词性标注模型 | 是
mynlp-resource-ner | 1.0.0 | 14.1M |命名实体识别、人名识别模型 | 是
mynlp-resource-cws | 1.0.0 |65.5M |感知机分词模型 | 否
mynlp-resource-custom | 1.0.0 | 2.3M | 常见自定义扩展词库 | 否

coredict \ pos \ ner 默认被segment模块依赖。因为cws文件过大，如果使用了感知机分词，自行导入依赖。

### maven仓库加速
由于国内访问maven过慢，容易导致资源文件无法下载。请更具你的mavne或者gradle环境，自行设置阿里云或者华为云镜像加速。

- 华为云 'https://mirrors.huaweicloud.com/repository/maven/'
- 阿里云 'http://maven.aliyun.com/nexus/content/groups/public/'

gradle加速配置实例：
修改~/init.gradle文件
```groovy
allprojects {

    repositories {
        //mavenLocal()

        boolean added = false
        whenObjectRemoved {
            if(!added){
                addLast maven { 
                          url 'https://mirrors.huaweicloud.com/repository/maven/'
			//url 'http://maven.aliyun.com/nexus/content/groups/public/'
                }
            }
            added = true
        }

        all { ArtifactRepository repo ->
            if (repo instanceof MavenArtifactRepository) {
                def url = repo.url.toString()

                if (url.contains('org/maven2') || url.startsWith('https://jcenter.bintray.com/')) {
                    //project.logger.lifecycle "Repository ${repo.url} replaced by $REPOSITORY_URL."
                    remove repo
                }
            }
        }

    }

    buildscript {

        repositories {
      
            maven { url 'https://repo.huaweicloud.com/repository/maven/' }

            all { ArtifactRepository repo ->
                if (repo instanceof MavenArtifactRepository) {
                    def url = repo.url.toString()
                    if (url.contains('org/maven2') || url.startsWith('https://jcenter.bintray.com/')) {
                    	//project.logger.lifecycle "Repository ${repo.url} replaced by $REPOSITORY_URL."
                    	remove repo
                    }
                }
            }
        }
    }

}

```

---
title: "酒店评论分类示例"
draft: false
---

从网上找了一份酒店评论正负面的语料库来测试，演示和验证fasttext文本分类。

代码如下:
```java

import com.google.common.base.Charsets;
import com.google.common.io.Files;
import com.mayabot.mynlp.fasttext.FastText;
import com.mayabot.nlp.classification.FasttextClassification;
import com.mayabot.nlp.utils.DownloadUtils;

import java.io.File;
import java.util.List;

/**
 * 酒店评论的分类测试
 */
public class HotelCommentExampleTrain {

    public static void main(String[] args) throws Exception {


        File trainFile = new File("example.data/hotel-train.txt");


        if (!trainFile.exists()) {
            File trainZipFile = new File("example.data/hotel-train.txt.zip");
            File testZipFile = new File("example.data/hotel-test.txt.zip");

            DownloadUtils.download("http://cdn.mayabot.com/nlp/hotel-train.txt.zip",
                    trainZipFile);
            DownloadUtils.download("http://cdn.mayabot.com/nlp/hotel-test.txt.zip",
                    testZipFile);

            DownloadUtils.unzip(trainZipFile);
            DownloadUtils.unzip(testZipFile);
            trainZipFile.delete();
            testZipFile.delete();
        }

        FastText modelTrain = FasttextClassification.train(trainFile, 100, 0.05, 20);

        modelTrain.saveModel("example.data/hotel.model");


        //test

        {
            FastText model = FastText.loadModel("example.data/hotel.model", false);


            File testFile = new File("example.data/hotel-test.txt");

            List<String> examples = Files.readLines(testFile, Charsets.UTF_8);

            int total = 0;
            int success = 0;

            for (int i = 0; i < examples.size(); i++) {
                String line = examples.get(i);

                String[] parts = line.split(" ");

                String label = null;

                for (String part : parts) {
                    if (part.startsWith("__label__")) {
                        label = part;
                    }
                }
                String result = FasttextClassification.predict(model, line);

                if (label.equals(result)) {
                    success++;
                } else {
                }

                total++;
            }

            System.out.println("Total " + total);
            System.out.println("Success " + success);

            System.out.println("正确率 " + String.format("%.2f", success * 100.0 / total) + "%");
        }
    }

}
```

上述程序会自动下载这个酒店评论训练集和测试集，然后训练和验证测试集。
最终运行结果如下：
```text
Read file build dictionary ...
Read 0M words
Number of words:  17291
Number of labels: 2
Progress: 100.00% words/sec/thread:   848860 lr: 0.00000 loss: 0.43875 ETA: 0h 0m 0s
Train use time 2093 ms
Total Test Data 400
Success 362
正确率 90.50%
```
如果你有其他测试集或者各使用场景的测试数据，那么请ISSUE告知我们。另外我们也计划修改Fasttext程序，
在训练过程中加入词的权重来影响向量空间，是否能提高准确率。
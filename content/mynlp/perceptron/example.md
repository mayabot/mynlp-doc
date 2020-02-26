---
title: "拼音流切分示例"
draft: false
weight: 2
---
             
定义感知机
```kotlin
class PinyinSplitDefinition : PerceptronDefinition<Char, String, CharArray>() {

 override val labels = listOf("B", "M", "E", "S")

 override fun labelIndex(label: String): Int {
     return when (label) {
         "B" -> 0
         "M" -> 1
         "E" -> 2
         "S" -> 3
         else -> 0
     }
 }

 override fun buffer() = FastStringBuilder(4)

 override fun featureFunction(sentence: CharArray, size: Int, position: Int, buffer: FastStringBuilder, emit: () -> Unit) {

     val CHAR_NULL = '\u0000'

     val lastIndex = size - position - 1

     val pre2Char = if (position > 1) sentence[position - 2] else CHAR_NULL
     val preChar = if (position > 0) sentence[position - 1] else CHAR_NULL
     val curChar = sentence[position]
     val nextChar = if (lastIndex > 0) sentence[position + 1] else CHAR_NULL
     val next2Char = if (lastIndex > 1) sentence[position + 2] else CHAR_NULL

     buffer.clear()
     buffer.set2(curChar, '2')
     emit()

     if (position > 0) {
         buffer.clear()
         buffer.set2(preChar, '1')
         emit()

         buffer.clear()
         buffer.set4(preChar, '/', curChar, '5')
         emit()

         if (position > 1) {
             buffer.clear()
             buffer.set4(pre2Char, '/', preChar, '4')
             emit()
         }
     }

     if (lastIndex > 0) {
         buffer.clear()
         buffer.set2(nextChar, '3')
         emit()

         buffer.clear()
         buffer.set4(curChar, '/', nextChar, '6')
         emit()

         if (lastIndex > 1) {
             buffer.clear()
             buffer.set4(nextChar, '/', next2Char, '7')
             emit()
         }
     }
 }

 override fun inputList2InputSeq(list: List<Char>): CharArray {
     return list.toCharArray()
 }

 /**
  * "世界 你好" => 世/B 界/E 你/B 好/E
  * B M S E
  */
 override fun annotateText(text: String): List<Pair<Char, String>> {
     return text.splitToSequence('﹍')
             .flatMap { word ->
                 when (word.length) {
                     0 -> emptyList()
                     1 -> listOf(word[0] to "S")
                     2 -> listOf(word[0] to "B", word[1] to "E")
                     3 -> listOf(word[0] to "B", word[1] to "M", word[2] to "E")
                     4 -> listOf(word[0] to "B", word[1] to "M", word[2] to "M", word[3] to "E")
                     5 -> listOf(word[0] to "B", word[1] to "M", word[2] to "M", word[3] to "M", word[4] to "E")
                     else -> {
                         val list = ArrayList<Pair<Char, String>>(word.length)
                         list += word[0] to "B"
                         for (i in 1 until word.length - 1) {
                             list += word[i] to "M"
                         }
                         list += word[0] to "E"
                         list.toList()
                     }
                 }.asSequence()
             }.toList()
 }
}

fun pinyinSplitEvaluateFun(id:Int,model:Perceptron,sampleList:List<String>):EvaluateResult{
 var count = 0
 var goldTotal = 0
 var predTotal = 0

 var correct = 0

 val segmenter = PinyinSplitApp(model)

 for (line in sampleList) {
     val wordArray = line.split("﹍")
     goldTotal += wordArray.size

     val text = wordArray.joinToString(separator = "")
     val predArray = segmenter.decodeToWordList(text)
     predTotal += predArray.size

     correct += wordCorrect(wordArray,predArray)

     count++
 }

 return EvaluateResult(goldTotal, predTotal, correct)
}
```

训练模型
```kotlin
 val definition = PinyinSplitDefinition()
 val model = definition.train(
     File("data/pinyin/yuliao_a.txt"),
     File("data/pinyin/yuliao_b.txt"),80,4,false,::pinyinSplitEvaluateFun)

 model.save(File("data/pinyin/model"))
```
语料格式为
```
yin﹍shi﹍zhu﹍da﹍jia﹍cang﹍cai﹍mei﹍you﹍jiu﹍sui
```

模型解码:

```

class PinyinSplitApp(val model: Perceptron) {

 private val logic = PinyinSplitDefinition()

 fun decodeToWordList(sentence: String): List<String> {
     val result = ArrayList<String>()
     val input = sentence.toCharArray()
    

     val output = logic.decodeModel(model, input)

     var p = 0
     for (i in 0 until output.size) {
         val f = output[i]
         if (f == "S" || f == "E") {
             result += sentence.substring(p, i + 1)
             p = i + 1
         }
     }
     if (p < sentence.length) {
         result += sentence.substring(p, sentence.length)
     }

     return result
 }

 companion object {

     const val modelPrefix = "pinyin-split-model"

     fun load(file: File):PinyinSplitApp {
         return PinyinSplitApp(PerceptronModel.load(file))
     }

     fun loadDefault():PinyinSplitApp{
         return PinyinSplitApp(PerceptronModel.loadFromNlpResouce(modelPrefix))
     }
 }
}
```

测试
```kotlin
val app = PinyinSplitApp.load(File("data/pinyin/model")
val result = app.decodeToWordList("shujubianhuaqushidengxiangguanxinxi")
```

输出
```
shu, ju, bian, hua, qu, shi, deng, xiang, guan, xin, xi
```
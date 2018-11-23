#### Hanlp

简介：

​	HanLP是由一系列模型与算法组成的Java工具包，目标是普及自然语言处理在生产环境中的应用。 HanLP具备功能完善、性能高效、架构清晰、语料时新、可自定义的特点。功能：中文分词、词性标注、命名实体识别、依存句法分析、关键词提取、新词发现、短语提取、自动摘要、文本分类、拼音简繁 。

#### **Hanlp环境安装**

**【此处安装配置较麻烦可以忽略，直接使用pip install pyhanlp即可(不过hanlp方便迁移)】**

• 1、安装Java和Visual C++：我装的是Java 1.8和Visual C++ 2015。

Java 1.8：https://blog.csdn.net/zhangyong01245/article/details/75142870

Visual C++ 2015：https://www.microsoft.com/en-us/download/details.aspx?id=53587

• 2、安裝Jpype：conda install -c conda-forge jpype1
• 3、测试是否按照成功：

```python
from jpype import *
startJVM(getDefaultJVMPath(), "-ea")
java.lang.System.out.println("Hello World")
shutdownJVM() 
```

#### **Hanlp安装**

• 1、下载hanlp.jar包： https://github.com/hankcs/HanLP
• 2、下载data.zip：https://github.com/hankcs/HanLP/releases中
http://hanlp.linrunsoft.com/release/data-for-1.7.0.zip后解压数据包。

**备注**：所需要的模型结构如图片所示 data、jar、hanlp.properties

HanLP中的数据分为*词典*和*模型*，其中*词典*是词法分析必需的，*模型*是句法分析必需的。

可以自行增删替换，如果不需要句法分析等功能的话，随时可以删除model文件夹。



![54174622838](D:\Diary\temp\1541746228385.png)

• 3、配置文件
	• 示例配置文件:hanlp.properties

​	• 配置文件的作用是告诉HanLP数据包的位置，只需修改第一行：root=usr/home/HanLP/

​	• 比如data目录是/Users/hankcs/Documents/data，那么root=/Users/hankcs/Documents/ 

​	• 支持相对路径：root=../hanlp ，即只要找到hanlp目录，但是运行.py文件配置hanlp包含相对路径时均报错，故hanlp.properties**需要配置绝对路径**。

​	• hanlp.properties自定义字典：注意添加新词典时，在分号后面追加需要空一格，运行前需要删除.bin文件，类如：(CustomDictionary.txt.bin) 添加字典位置：data/dictionary/custom

```bash
#所有词典统一使用UTF-8编码，每一行代表一个单词，格式遵从[单词] [词性A] [A的频次] [词性B] [B的频次] ... 如果不填词性则表示采用词典的默认词性。
CustomDictionaryPath=data/dictionary/custom/CustomDictionary.txt; 现代汉语补充词库.txt; 全国地名大全.txt ns; 人名词典.txt; 机构名词典.txt; 上海地名.txt ns;data/dictionary/person/nrf.txt nrf;
```



#### 词典说明

​	详细介绍HanLP中的词典格式，满足自定义的需要。HanLP中有许多词典，它们的格式都是相似的，形式都是文本文档，随时可以修改。【不可以的字典可以从配置中删除】

**基本格式**

​	词典分为词频词性词典和词频词典。

- 词频词性词典（如 CoreNatureDictionary.txt）
  - 每一行代表一个单词，格式遵从`[单词] [词性A] [A的频次] [词性B] [B的频次] ...`。
  - 支持省略词性和频次，直接一行一个单词。
  - `.txt`词典文件的分隔符为空格或制表符，所以不支持含有空格的词语。如果需要支持空格，请使用英文逗号`,`分割的**纯文本**`.csv`文件。在使用Excel等富文本编辑器时，则请注意保存为**纯文本**形式。
- 词频词典（如CoreNatureDictionary.ngram.txt）
  - 每一行代表一个单词或条目，格式遵从`[单词] [单词的频次]`。
  - 每一行的分隔符为空格或制表符。

**储存形式**

​	词典有两个形态：文本文件(filename.txt)和缓存文件(filename.txt.bin或filename.txt.trie.dat和filename.txt.trie.value)。

- 文本文件

  - 采用明文储存，UTF-8编码，CRLF换行符。

- 缓存文件

  - 就是一些二进制文件，通常在文本文件的文件名后面加上.bin表示。有时候是.trie.dat和.trie.value。后者是历史遗留产物，分别代表trie树的数组和值。
  - 如果你修改了任何词典，只有删除缓存才能生效。

  ​

**修改方法**

​	HanLP的核心词典训练自人民日报2014语料，语料不是完美的，总会存在一些错误。这些错误可能会导致分词出现奇怪的结果，这时请打开调试模式排查问题：

```
HanLP.Config.enableDebug(); # 我暂时没有试过
```

- 核心词性词频词典
  - 比如在`data/dictionary/CoreNatureDictionary.txt`中发现了一个不是词的词，或者词性标注得明显不对，那么你可以修改它，然后删除缓存文件使其生效。
  - 目前`CoreNatureDictionary.ngram.txt`的缓存依赖于`CoreNatureDictionary.txt`的缓存，修改了后者之后必须同步删除前者的缓存，否则可能出错
- 核心二元文法词典
  - 二元文法词典`data/dictionary/CoreNatureDictionary.ngram.txt`储存的是两个词的接续，如果你发现不可能存在这种接续时，删掉即可。
  - 你也可以添加你认为合理的接续，但是这两个词必须同时在核心词典中才会生效。
- 命名实体识别词典
  - 基于角色标注的命名实体识别比较依赖词典，所以词典的质量大幅影响识别质量。
  - 这些词典的格式与原理都是类似的，请阅读[相应的文章](http://www.hankcs.com/category/nlp/ner/)或代码修改它。




方法①：另新增自定义词典（可增加分词+词性），切记先删除缓存所有.bin文件

方法②：CustomDictionary.add("北京大学")（仅可增加分词）



```python
question="请问北京大学怎么走？"
CustomDictionary = JClass('com.hankcs.hanlp.dictionary.CustomDictionary')
CustomDictionary.add("北京大学")
print(HanLP.segment(question))
```

备注：hanlp可以自定义字典分词+词性+词频，测试分词效果较佳，自定义词典后需要耗费一定运行时间，再运行时速度较快，灵活性强，缺陷：命名实体没有较为明显如person,org等词性标注。



#### 词性说明

​	HanLP使用的HMM词性标注模型训练自2014年人民日报切分语料，随后增加了少量98年人民日报中独有的词语。所以，HanLP词性标注集兼容《ICTPOS3.0汉语词性标记集》，并且兼容《现代汉语语料库加工规范——词语切分与词性标注》。

[^详情参见]: hanlp词性列表.doc




#### Hanlp代码示例 

```python
#-*- coding:utf-8 -*-
from jpype import *
import os

path=os.getcwd()
# startJVM(getDefaultJVMPath(), r"-Djava.class.path=F:\EmotionAnaly\emotionAnaly_preresearch\models\hanlp\hanlp-1.6.8.jar;F:\EmotionAnaly\emotionAnaly_preresearch\models\hanlp",
startJVM(getDefaultJVMPath(), r"-Djava.class.path="+path+os.sep+"hanlp-1.6.8.jar;"+path,
         "-Xms1g",
         "-Xmx1g") # 启动JVM，Linux需替换分号;为冒号:

print("=" * 30 + "HanLP分词" + "=" * 30)
HanLP = JClass('com.hankcs.hanlp.HanLP')
print(HanLP)
# 中文分词
print(HanLP.segment('你好，欢迎在Python中调用HanLP的API'))
print("-" * 70)

print("=" * 30 + "标准分词" + "=" * 30)
StandardTokenizer = JClass('com.hankcs.hanlp.tokenizer.StandardTokenizer')
print(StandardTokenizer.segment('你好，欢迎在Python中调用HanLP的API'))
print("-" * 70)

# NLP分词NLPTokenizer会执行全部命名实体识别和词性标注
print("=" * 30 + "NLP分词" + "=" * 30)
NLPTokenizer = JClass('com.hankcs.hanlp.tokenizer.NLPTokenizer')
print(NLPTokenizer.segment('中国科学院计算技术研究所的宗成庆教授正在教授自然语言处理课程'))
print("-" * 70)

print("=" * 30 + "索引分词" + "=" * 30)
IndexTokenizer = JClass('com.hankcs.hanlp.tokenizer.IndexTokenizer')
termList = IndexTokenizer.segment("主副食品")
for term in termList:
    print(str(term) + " [" + str(term.offset) + ":" + str(term.offset + len(term.word)) + "]")
print("-" * 70)

print("=" * 30 + " 极速词典分词" + "=" * 30)
SpeedTokenizer = JClass('com.hankcs.hanlp.tokenizer.SpeedTokenizer')
print(NLPTokenizer.segment('江西鄱阳湖干枯，中国最大淡水湖变成大草原'))
print("-" * 70)

print("=" * 30 + " 自定义分词" + "=" * 30)
CustomDictionary = JClass('com.hankcs.hanlp.dictionary.CustomDictionary')
CustomDictionary.add('攻城狮') # 动态增加
CustomDictionary.add("单身汪汪", "nz 1024 n 1") # 也可以加入词性
CustomDictionary.insert("白富美", "nz 1024")  # 强行插入
#CustomDictionary.remove("攻城狮"); # 删除词语（注释掉试试）
HanLP = JClass('com.hankcs.hanlp.HanLP')
print(HanLP.segment('攻城狮逆袭单身汪汪，迎娶白富美，走上人生巅峰'))
print("-" * 70)

print("=" * 30 + " 自定义分词 强制插入" + "=" * 30)
# 还可以直接插入到用户词典
CustomDictionary = JClass("com.hankcs.hanlp.dictionary.CustomDictionary")
CustomDictionary.insert("阿尔法狗", "科技名词 1024")
StandardTokenizer.SEGMENT.enablePartOfSpeechTagging(True)  # 依然支持隐马词性标注 #强行插入强制生成
term_list = HanLP.segment("苹果电脑可以运行开源阿尔法狗代码吗")
print(term_list) #[苹果电脑/电脑品牌, 可以/v, 运行/vn, 开源/v, 阿尔法狗/科技名词, 代码/n, 吗/y]

print("=" * 30 + " CRF分词" + "=" * 30)
# 增加用户词典,对其他分词器同样有效
# 注意此处,CRF分词器将单身狗分为了n 即使单身狗:"nz 1024 n 1"
CRFnewSegment = HanLP.newSegment("crf")
print(CRFnewSegment.seg('攻城狮逆袭单身汪汪，迎娶白富美，走上人生巅峰'))
print("-" * 70)

print("=" * 30 + " 词频统计" + "=" * 30)
print(CustomDictionary.get("单身汪汪")) # 展示该单词 在词典中的词频统计 展示分词
print(HanLP.segment('攻城狮逆袭单身汪汪，迎娶白富美，走上人生巅峰'))

print("=" * 20 + "命名实体识别与词性标注" + "=" * 30)
NLPTokenizer = JClass('com.hankcs.hanlp.tokenizer.NLPTokenizer')
print(NLPTokenizer.segment('中国科学院计算技术研究所的宗成庆教授正在教授自然语言处理课程'))
print("-" * 70)

document = "水利部水资源司司长陈明忠9月29日在国务院新闻办举行的新闻发布会上透露，" \
    "根据刚刚完成了水资源管理制度的考核，有部分省接近了红线的指标，" \
           "有部分省超过红线的指标。对一些超过红线的地方，陈明忠表示，对一些取用水项目进行区域的限批，" \
           "严格地进行水资源论证和取水许可的批准。"
print("=" * 30 + "关键词提取" + "=" * 30)
print(HanLP.extractKeyword(document, 8))
print("-" * 70)

print("=" * 30 + "自动摘要" + "=" * 30)
print(HanLP.extractSummary(document, 3))
print("-" * 70)

text = r"算法工程师\n 算法（Algorithm）是一系列解决问题的清晰指令，也就是说，能够对一定规范的输入，在有限时间内获得所要求的输出。如果一个算法有缺陷，或不适合于某个问题，执行这个算法将不会解决这个问题。不同的算法可能用不同的时间、空间或效率来完成同样的任务。一个算法的优劣可以用空间复杂度与时间复杂度来衡量。算法工程师就是利用算法处理事物的人。\n \n 1职位简介\n 算法工程师是一个非常高端的职位；\n 专业要求：计算机、电子、通信、数学等相关专业；\n 学历要求：本科及其以上的学历，大多数是硕士学历及其以上；\n 语言要求：英语要求是熟练，基本上能阅读国外专业书刊；\n 必须掌握计算机相关知识，熟练使用仿真工具MATLAB等，必须会一门编程语言。\n\n2研究方向\n 视频算法工程师、图像处理算法工程师、音频算法工程师 通信基带算法工程师\n \n 3目前国内外状况\n 目前国内从事算法研究的工程师不少，但是高级算法工程师却很少，是一个非常紧缺的专业工程师。算法工程师根据研究领域来分主要有音频/视频算法处理、图像技术方面的二维信息算法处理和通信物理层、雷达信号处理、生物医学信号处理等领域的一维信息算法处理。\n 在计算机音视频和图形图像技术等二维信息算法处理方面目前比较先进的视频处理算法：机器视觉成为此类算法研究的核心；另外还有2D转3D算法(2D-to-3D conversion)，去隔行算法(de-interlacing)，运动估计运动补偿算法(Motion estimation/Motion Compensation)，去噪算法(Noise Reduction)，缩放算法(scaling)，锐化处理算法(Sharpness)，超分辨率算法(Super Resolution),手势识别(gesture recognition),人脸识别(face recognition)。\n 在通信物理层等一维信息领域目前常用的算法：无线领域的RRM、RTT，传送领域的调制解调、信道均衡、信号检测、网络优化、信号分解等。\n 另外数据挖掘、互联网搜索算法也成为当今的热门方向。\n"
print("=" * 30 + "短语提取" + "=" * 30)
print(HanLP.extractPhrase(text, 10))
print("-" * 70)

print(HanLP.parseDependency("李白的诗有哪些？"))
print("-" * 70)


print("=" * 30 + "使用停用词的简单例子" + "=" * 30) # 目前认为不建议使用这里的停用词
text = "小区居民有的反对喂养流浪猫"
CRFnewSegment = HanLP.newSegment("crf")
term_list = CRFnewSegment.seg(text)
# BasicTokenizer = SafeJClass("com.hankcs.hanlp.tokenizer.BasicTokenizer")
# term_list = BasicTokenizer.segment(text)

CoreStopWordDictionary = JClass("com.hankcs.hanlp.dictionary.stopword.CoreStopWordDictionary")
CoreStopWordDictionary.apply(term_list)
HanLP.Config.ShowTermNature = False

print(term_list)
print([i.word for i in term_list])


shutdownJVM()
```

备注：首次运行需要加载模型文件，需要时间较长，加载后再运行几次速度均较快。





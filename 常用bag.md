#### NumPy

简介：

​	NumPy系统是Python的一种开源的数值计算包。包括：1、一个强大的N维数组对象Array；2、比较成熟的（广播）函数库；3、用于整合C/C++和Fortran代码的工具包；4、实用的线性代数、傅里叶变换和随机数生成函数。 numpy和稀疏矩阵运算包scipy配合使用更加方便。

#### NLTK

简介：

​	Natural Language Toolkit，自然语言处理工具包，在NLP领域中，最常使用的一个Python库。

- pip install nltk 

#### Gensim

简介：

​	Gensim是一个占内存低，接口简单，免费的Python库，它可以用来从文档中自劢提取语义主题。它包含了很多非监督学习算法如：TF/IDF，潜在语义分析（Latent Semantic Analysis，LSA）、隐含狄利克雷分配（Latent Dirichlet Allocation，LDA），层次狄利克雷过程（ Hierarchical Dirichlet Processes ，HDP ）等。Gensim支持Word2Vec,Doc2Vec等模型。 

- pip install gensim 

#### Tensorflow

简介：

​	TensorFlow是谷歌基于DistBelief进行研发的第二代人工智能学习系统。TensorFlow可被用于语音识别戒图像识别等多顷机器学习和深度学习领域。TensorFlow是一个采用数据流图（data flow graphs），用于数值计算的开源软件库。节点（Nodes）在图中表示数学操作，图中的线（edges）则表示在节点间相互联系的多维数据数组，即张量（tensor）。它灵活的架构让你可以在多种平台上展开计算，例如台式计算机中的一个戒多个CPU（戒GPU），服务器，移劢设备等等。TensorFlow 最初由Google大脑小组（隶属于Google机器智能研究机构）的研究员和工程师们开发出来，用于机器学习和深度神经网络方面的研究，但这个系统的通用性使其也可广泛用于其他计算领域。

- Pip install tensorflow
- Pip install tf-nightly-gpu/cpu 



#### jieba

简介：

​	“结巴”中文分词：是广泛使用的中文分词工具，具有以下特点：
1）三种分词模式：精确模式，全模式和搜索引擎模式
2）词性标注和返回词语在原文的起止位置（ Tokenize）
3）可加入自定义字典
4）代码对 Python 2/3 均兼容
5）支持多种语言，支持简体繁体
项目地址：https://github.com/fxsjy/jieba 

- pip install jieba

```python
#encoding=utf8
import jieba.posseg as pseg 
strings="是广泛使用的中文分词工具，具有以下特点："
words = pseg.cut(strings)

for word, flag in words:
    print('%s %s' % (word, flag))
#------------------------------------------------------------------------
# encoding=utf-8
import jieba

seg_list = jieba.cut("我来到北京清华大学", cut_all=True)
print("Full Mode: " + "/ ".join(seg_list))  # 全模式

seg_list = jieba.cut("我来到北京清华大学", cut_all=False)
print("Default Mode: " + "/ ".join(seg_list))  # 精确模式

seg_list = jieba.cut("他来到了网易杭研大厦")  # 默认是精确模式
print(", ".join(seg_list))

seg_list = jieba.cut_for_search("小明硕士毕业于中国科学院计算所，后在日本京都大学深造")  # 搜索引擎模式
print(", ".join(seg_list))

```



#### Stanford NLP

简介：

​	Stanford NLP提供了一系列自然语言分析工具。它能够给出基本的词形，词性，不管是公司名还是人名等，格式化的日期，时间，量词，并且能够标记句子的结构，语法形式和字词依赖，指明那些名字指向同样的实体，指明情绪，提取发言中的开放关系等。

- 1.一个集成的语言分析工具集；
- 2.进行快速，可靠的任意文本分析；
- 3.整体的高质量的文本分析;
- 4.支持多种主流语言;
- 5.多种编程语言的易用接口;
- 6.方便的简单的部署web服务。 

**Python 版本stanford nlp 安装**

• 1）安装stanford nlp自然语言处理包： pip install stanfordcorenlp
• 2）下载Stanford CoreNLP文件：https://stanfordnlp.github.io/CoreNLP/download.html （同个下载链接）
• 3）下载中文模型jar包： http://nlp.stanford.edu/software/stanford-chinesecorenlp-2018-10-05-models.jar（不是下载链接，仅做举栗子）
• 4）把加压后的Stanford CoreNLP文件夹和下载的stanford-chinese-corenlp-
2018-02-27-models.jar放在同一目录下
• 5）在Python中引用模型：
	- from stanfordcorenlp import StanfordCoreNLP

​	- nlp = StanfordCoreNLP(r‘path', lang='zh') 

Python 版本stanford nlp 用法展示 ：

```python
#-*-encoding=utf8-*-
from stanfordcorenlp import StanfordCoreNLP
nlp = StanfordCoreNLP(r'stanfordnlp', lang='zh')

fin=open('news.txt','r',encoding='utf8')
fner=open('ner.txt','w',encoding='utf8')
ftag=open('pos_tag.txt','w',encoding='utf8')
for line in fin:
    line=line.strip()
    if len(line)<1:
        continue
 
    fner.write(" ".join([each[0]+"/"+each[1] for  each in nlp.ner(line) if len(each)==2 ])+"\n")
    ftag.write(" ".join([each[0]+"/"+each[1] for each in nlp.pos_tag(line) if len(each)==2 ])+"\n")
fner.close()   
ftag.close()
nlp.close()
```



#### Hanlp

简介：

​	HanLP是由一系列模型与算法组成的Java工具包，目标是普及自然语言处理在生产环境中的应用。 HanLP具备功能完善、性能高效、架构清晰、语料时新、可自定义的特点。功能：中文分词、词性标注、命名实体识别、依存句法分析、关键词提取、新词发现、短语提取、自动摘要、文本分类、拼音简繁 。

**Hanlp环境安装**

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

**Hanlp安装【此处安装配置较麻烦可以忽略，直接使用pip install pyhanlp即可(不过hanlp方便迁移)】**

• 1）下载hanlp.jar包： https://github.com/hankcs/HanLP
• 2、下载data.zip：https://github.com/hankcs/HanLP/releases中
http://hanlp.linrunsoft.com/release/data-for-1.7.0.zip后解压数据包。
• 3、配置文件
	• 示例配置文件:hanlp.properties
	• 配置文件的作用是告诉HanLP数据包的位置，只需修改第一行：root=usr/home/HanLP/
	• 比如data目录是/Users/hankcs/Documents/data，那么root=/Users/hankcs/Documents/ 

​	• 支持相对路径：root=../hanlp ，即只要找到hanlp目录，但是运行.py文件配置hanlp包含相对路径时均报错，故hanlp.properties**需要配置绝对路径**。

Hanlp代码 ：

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






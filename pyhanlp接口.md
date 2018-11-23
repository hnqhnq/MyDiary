## pyhanlp

​	分词是HanLP最基础的功能，HanLP实现了许多种分词算法，每个分词器都支持特定的配置。标准分词是最常用的分词器，基于HMM-Viterbi实现，开启了中国人名识别和音译人名识别。	

​	HanLP具备高度可自定义的特点，所有模型和词典都可以自由替换。如果希望与别的项目共享同一套data，只需将该项目的配置文件`hanlp.properties`拷贝到pyhanlp的安装目录下即可。

注意：

1. 使用pip初次安装 pyhanlp 后，不设置变量，程序会自动下载所需依赖到默认位置。如果是设置了上述变量，则不进行下载。因为文件比较大，网络下载稳定性等原因。
2. 保证 hanlp.properties 中的 root 是指向正确的data路径。



>参考：
>
><http://hanlp.linrunsoft.com/> 
>
><https://github.com/hankcs/HanLP>
>
>备注：因为hanlp资料的代码由Java编写，本人进行了部分Python转换，可能存在出入。



#### **安装**

- conda install -c conda-forge jpype1
- pip install pyhanlp

测试是否安装成功：

```python
from jpype import *
startJVM(getDefaultJVMPath(), "-ea")
java.lang.System.out.println("Hello World")
shutdownJVM() 
```



使用代码测试：

- 如果没有对应的数据包，则系统会自动下载。
- hanlp-1.7.0-release.zip、data-for-1.7.0.zip

```python
from pyhanlp import *
print(HanLP.segment('你好，欢迎在Python中调用HanLP的API'))
```

#### 总代码示例

```python
# -*- coding: utf-8 -*-
# @Time    : 2018/11/14 14:42
# @File    : pyhanlp_demo.py
# @Author  : hnq

from jpype import *
# startJVM(getDefaultJVMPath(), "-ea")
# java.lang.System.out.println("Hello World")
# shutdownJVM()

from pyhanlp import *

print('-----hanlp分词-------')
print(HanLP.segment('你好，欢迎在Python中调用HanLP的API'))

print("=" * 30 + "HanLP_1分词" + "=" * 30)
HanLP_1 = JClass('com.hankcs.hanlp.HanLP') #与直接调用HanLP分词是一样的接口。
print(HanLP_1.segment('你好，欢迎在Python中调用HanLP的API'))
print("-" * 70)

testCases = [
"商品和服务",
"结婚的和尚未结婚的确实在干扰分词啊",
"买水果然后来世博园最后去世博会",
"中国的首都是北京",
"欢迎新老师生前来就餐",
"工信处女干事每月经过下属科室都要亲口交代24口交换机等技术性器件的安装工作",
"随着页游兴起到现在的页游繁盛，依赖于存档进行逻辑判断的设计减少了，但这块也不能完全忽略掉。"]
# for sentence in testCases: print(HanLP.segment(sentence))

document = "水利部水资源司司长陈明忠9月29日在国务院新闻办举行的新闻发布会上透露，" \
"根据刚刚完成了水资源管理制度的考核，有部分省接近了红线的指标，" \
"有部分省超过红线的指标。对一些超过红线的地方，陈明忠表示，对一些取用水项目进行区域的限批，" \
"严格地进行水资源论证和取水许可的批准。"
print('----关键词----')
print(HanLP.extractKeyword(document, 2))

print('----自动摘要-----')
print(HanLP.extractSummary(document, 3))

print('----依存句法分析-----')
# print(HanLP.parseDependency("徐先生还具体帮助他确定了把画雄鹰、松鼠和麻雀作为主攻目标。"))

print('-----感知机词法分析器--------')
PerceptronLexicalAnalyzer = JClass('com.hankcs.hanlp.model.perceptron.PerceptronLexicalAnalyzer')
analyzer = PerceptronLexicalAnalyzer()
print(analyzer.analyze("上海华安工业（集团）公司董事长谭旭光和秘书胡花蕊来到美国纽约现代艺术博物馆参观"))
print('-----感知机词法分析器--去除杂质------')
print(analyzer.analyze("空格 \t\n\r\f&nbsp;统统都不要".replace("\\s+", "") .replace("&nbsp;", "")))
# 去除所有空白符  # 如果一些文本中含有html控制符
print('-----感知机词法分析器---在线学习-----')
print(analyzer.analyze("我在浙江金华出生")) #学习前：我/r 在/p 浙江/ns 金华/nr 出生/v
print('-----训练后------')
# analyzer.learn("在/p 浙江/ns 金华/ns 出生/v")
print(analyzer.analyze("我在四川金华出生，我的名字叫金华")) #训练后
# analyzer.getPerceptronSegmenter().getModel().save(HanLP.Config.PerceptronCWSModelPath)
print(HanLP.segment('我在四川金华出生，我的名字叫金华'))

print("=" * 30 + "hanlp标准分词" + "=" * 30)
StandardTokenizer = JClass('com.hankcs.hanlp.tokenizer.StandardTokenizer')
print(StandardTokenizer.segment('你好，欢迎在Python中调用HanLP的API'))
print("-" * 70)

# NLP分词NLPTokenizer会执行全部命名实体识别和词性标注
print("=" * 30 + "NLP分词--开启人名识别(默认)--[命名实体识别和词性标注]" + "=" * 30)
NLPTokenizer = JClass('com.hankcs.hanlp.tokenizer.NLPTokenizer')
print(NLPTokenizer.segment('中国科学院计算技术研究所的宗成庆教授正在教授自然语言处理课程'))
segment_name=HanLP.newSegment().enableNameRecognize(True) #默认开启的 无需手动
print(segment_name.seg('中国科学院计算技术研究所的宗成庆教授正在教授自然语言处理课程'))

print("=" * 30 + "开启地名识别--[命名实体识别和词性标注]" + "=" * 30)
print(NLPTokenizer.segment('蓝翔给宁夏固原市彭阳县红河镇黑牛沟村捐赠了挖掘机'))#[蓝翔/d, 给/v, 宁夏/ns, 固原市/ns, 彭阳县/ns, 红河镇/ns, 黑牛沟村/ns, 捐赠/v, 了/u, 挖掘机/n]
segment_place=HanLP.newSegment().enablePlaceRecognize(True) # 默认关闭，省内存
print(segment_place.seg('蓝翔给宁夏固原市彭阳县红河镇黑牛沟村捐赠了挖掘机'))#[蓝翔/nt, 给/p, 宁夏/ns, 固原市/ns, 彭阳县/ns, 红河镇/ns, 黑牛沟村/ns, 捐赠/v, 了/ule, 挖掘机/n]

print("=" * 30 + "开启机构名识别--[命名实体识别和词性标注]" + "=" * 30)
print(NLPTokenizer.segment('济南杨铭宇餐饮管理有限公司是由杨先生创办的餐饮企业'))
segment_org = HanLP_1.newSegment().enableOrganizationRecognize(True) # 默认关闭，省内存
print(segment_org.seg('济南杨铭宇餐饮管理有限公司是由杨先生创办的餐饮企业'))

shutdownJVM()
```



## 命名实体识别

#### 中国人名识别

可以自动识别中国人名，标注为nr:

```bash
与人名相关词性配置——> NAME:{<nr|nr1|nr2|nrf|nrj>+}
```

```python
# NLP分词NLPTokenizer会执行全部命名实体识别和词性标注 PK “开启”命名识别分词器
print("=" * 30 + "NLP分词--开启人名识别(默认)--[命名实体识别和词性标注]" + "=" * 30)
NLPTokenizer = JClass('com.hankcs.hanlp.tokenizer.NLPTokenizer')
print(NLPTokenizer.segment('中国科学院计算技术研究所的宗成庆教授正在教授自然语言处理课程'))
segment_name=HanLP.newSegment().enableNameRecognize(True) #默认开启的
print(segment_name.seg('中国科学院计算技术研究所的宗成庆教授正在教授自然语言处理课程'))
#[中国科学院计算技术研究所/n, 的/u, 宗/n, 成庆/nr, 教授/n, 正在/d, 教授/n, 自然语言处理/v, 课程/n]
#[中国科学院计算技术研究所/nt, 的/ude1, 宗成庆/nr, 教授/nnt, 正在/d, 教授/nnt, 自然语言处理/nz, 课程/n]
```

- 目前分词器基本上都默认开启了中国人名识别，比如HanLP.segment()接口中使用的分词器等等，用户不必手动开启；上面的代码只是为了强调。
- 有一定的误命中率，比如误命中 `关键年` ，则可以通过在 `data/dictionary/person/nr.txt` 加入一条 `关键年 A 1` 来排除关键年作为人名的可能性，也可以将 `关键年` 作为新词登记到自定义词典中。
- 算法详解 [《实战HMM-Viterbi角色标注中国人名识别》](http://www.hankcs.com/nlp/chinese-name-recognition-in-actual-hmm-viterbi-role-labeling.html)



#### 地名识别

可以自动识别地名，标注为ns:

```bash
地名相关词性配置——>LOCATION:{<ns|nsf>+}
```

```python
print("=" * 30 + "开启地名识别--[命名实体识别和词性标注]" + "=" * 30)
print(NLPTokenizer.segment('蓝翔给宁夏固原市彭阳县红河镇黑牛沟村捐赠了挖掘机'))
segment_place=HanLP.newSegment().enablePlaceRecognize(True) # 默认关闭，省内存
print(segment_place.seg('蓝翔给宁夏固原市彭阳县红河镇黑牛沟村捐赠了挖掘机'))
#[蓝翔/d, 给/v, 宁夏/ns, 固原市/ns, 彭阳县/ns, 红河镇/ns, 黑牛沟村/ns, 捐赠/v, 了/u, 挖掘机/n]
#[蓝翔/nt, 给/p, 宁夏/ns, 固原市/ns, 彭阳县/ns, 红河镇/ns, 黑牛沟村/ns, 捐赠/v, 了/ule, 挖掘机/n]
```

- 目前标准分词器都默认关闭了地名识别，用户需要手动开启；这是因为消耗性能，其实多数地名都收录在核心词典和用户自定义词典中。
- 在生产环境中，能靠词典解决的问题就靠词典解决，这是最高效稳定的方法。
- 算法详解 [《实战HMM-Viterbi角色标注地名识别》](http://www.hankcs.com/nlp/ner/place-names-to-identify-actual-hmm-viterbi-role-labeling.html)



#### 机构名识别

可以自动识别机构名，标注为nt:

```bash
机构名相关词性配置——>ORGANIZATIONL:{<nt|ntc|ntcb|ntcf|ntch|nth|nto|nts|ntu>+}
```

```python
print("=" * 30 + "开启机构名识别--[命名实体识别和词性标注]" + "=" * 30)
print(NLPTokenizer.segment('济南杨铭宇餐饮管理有限公司是由杨先生创办的餐饮企业'))
segment_org = HanLP_1.newSegment().enableOrganizationRecognize(True) # 默认关闭，省内存
print(segment_org.seg('济南杨铭宇餐饮管理有限公司是由杨先生创办的餐饮企业'))
#[济南杨铭宇餐饮管理有限公司/nt, 是/v, 由/p, 杨/nr, 先生/n, 创办/v, 的/u, 餐饮企业/n]
#[济南杨铭宇餐饮管理有限公司/nt, 是/vshi, 由/p, 杨先生/nr, 创办/v, 的/ude1, 餐饮企业/nz]
```

- 目前分词器默认关闭了机构名识别，用户需要手动开启；这是因为消耗性能，其实常用机构名都收录在核心词典和用户自定义词典中。
- HanLP的目的不是演示动态识别，在生产环境中，能靠词典解决的问题就靠词典解决，这是最高效稳定的方法。
- 算法详解 [《层叠HMM-Viterbi角色标注模型下的机构名识别》](http://www.hankcs.com/nlp/ner/place-name-recognition-model-of-the-stacked-hmm-viterbi-role-labeling.html)


#### 时间识别

```bash
时间识别相关词性配置——>
TIME:{<m>+<qt>+<m|t>+<m>+<b>+}
{<m>+<qt|w>+<m>?<qt>}
{<t|qt>}
{<t>+<m>?<b>+}
```

#### 动名词短语识别

```
NP:
{<m|mg|Mg|mq|q|qg|qt|qv|s|>*<a|an|ag>*<s|g|gb|gbc|gc|gg|gm|gp|n|an|nr|ns|nt|nz|nb|nba|nbc|nbp|nf|ng|nh|nhd|o|nz|nx|ntu|nts|nto|nth|ntch|ntcf|ntcb|ntc|nt|nsf|ns|nrj|nrf|nr2|nr1|nr|nnt|nnd|nn|nmc|nm|nl|nit|nis|nic|ni|nhm|nhd>+<f>?<ude1>?<g|gb|gbc|gc|gg|gm|gp|n|an|nr|ns|nt|nz|nb|nba|nbc|nbp|nf|ng|nh|nhd|o|nz|nx|ntu|nts|nto|nth|ntch|ntcf|ntcb|ntc|nt|nsf|ns|nrj|nrf|nr2|nr1|nr|nnt|nnd|nn|nmc|nm|nl|nit|nis|nic|ni|nhm|nhd>+}
{<n|an|nr|ns|nt|nz|nb|nba|nbc|nbp|nf|ng|nh|nhd|nz|nx|ntu|nts|nto|nth|ntch|ntcf|ntcb|ntc|nt|nsf|ns|nrj|nrf|nr2|nr1|nr|nnt|nnd|nn|nmc|nm|nl|nit|nis|nic|ni|nhm|nhd>+<cc>+<n|an|nr|ns|nt|nz|nb|nba|nbc|nbp|nf|ng|nh|nhd|nz|nx|ntu|nts|nto|nth|ntch|ntcf|ntcb|ntc|nt|nsf|ns|nrj|nrf|nr2|nr1|nr|nnt|nnd|nn|nmc|nm|nl|nit|nis|nic|ni|nhm|nhd>+}
{<m|mg|Mg|mq|q|qg|qt|qv|s|>*<q|qg|qt|qv>*<f|b>*<vi|v|vn|vg|vd>+<ude1>+<n|an|nr|ns|nt|nz|nb|nba|nbc|nbp|nf|ng|nh|nhd|nz|nx|ntu|nts|nto|nth|ntch|ntcf|ntcb|ntc|nt|nsf|ns|nrj|nrf|nr2|nr1|nr|nnt|nnd|nn|nmc|nm|nl|nit|nis|nic|ni|nhm|nhd>+}
{<g|gb|gbc|gc|gg|gm|gp|n|an|nr|ns|nt|nz|nb|nba|nbc|nbp|nf|ng|nh|nhd|nz|nx|ntu|nts|nto|nth|ntch|ntcf|ntcb|ntc|nt|nsf|ns|nrj|nrf|nr2|nr1|nr|nnt|nnd|nn|nmc|nm|nl|nit|nis|nic|ni|nhm|nhd>+<vi>?}
VP:{<v|vd|vg|vf|vl|vshi|vyou|vx|vi|vn>+}
```



#### 同时识别人名、地名、机构名

- HanLP.newSegment().enableNameRecognize(True).enablePlaceRecognize(True).enableOrganizationRecognize(True)  # 开启人名识别（默认开启）、地名识别、机构名识别

```python
# 命名实体识别
def ner_hanlp_1(raw_sentence,return_list=True):
    segment_ner = HanLP.newSegment().enableNameRecognize(True).enablePlaceRecognize(True).enableOrganizationRecognize(True)  # 开启人名识别（默认开启）、地名识别、机构名识别
    if len(raw_sentence.strip()) > 0:
        return segment_ner.seg(raw_sentence) if return_list else iter(segment_ner.seg(raw_sentence))
```





## 篇章理解

​	想知道一篇文章中概况性的中心句是哪几句，或者提取关键字，HanLP提供了这些功能。

#### 关键词提取

一句话调用，第一个参数指定文本，第二个参数指定需要提取几个关键字:

```python
document = "水利部水资源司司长陈明忠9月29日在国务院新闻办举行的新闻发布会上透露，" \
"根据刚刚完成了水资源管理制度的考核，有部分省接近了红线的指标，" \
"有部分省超过红线的指标。对一些超过红线的地方，陈明忠表示，对一些取用水项目进行区域的限批，" \
"严格地进行水资源论证和取水许可的批准。"
print('----关键词----')
print(HanLP.extractKeyword(document, 2))
```

- 内部采用 `TextRankKeyword` 实现，用户可以直接调用 `TextRankKeyword.getKeywordList(document, size)`
- 算法详解 [《TextRank算法提取关键词的Java实现》](http://www.hankcs.com/nlp/textrank-algorithm-to-extract-the-keywords-java-implementation.html)



#### 自动摘要

同样是一句话调用，第一个参数指定文本，第二个参数指定需要提取几个句子:

```python
print('----自动摘要-----')
print(HanLP.extractSummary(document, 3))
```

- 内部采用 `TextRankSentence` 实现，用户可以直接调用 `TextRankSentence.getTopSentenceList(document, size)`。
- 算法详解 [《TextRank算法自动摘要的Java实现》](http://www.hankcs.com/nlp/textrank-algorithm-java-implementation-of-automatic-abstract.html)



#### 短语提取

同样是一句话调用，第一个参数指定文本，第二个参数指定需要提取几个短语:

```python
txt='2000年下半年，我组织成立友联，通过友联来管理各金融机构，当时有新疆金融租赁、新疆金新信托、新世纪金融租赁和德恒证券4家公司。'
print('----短语提取-----')
print(HanLP.extractPhrase(txt, 3))
```

- 内部采用 `MutualInformationEntropyPhraseExtractor` 实现，用户可以直接调用 `MutualInformationEntropyPhraseExtractor.extractPhrase(text, size)`。
- 算法详解 [《基于互信息和左右信息熵的短语提取识别》](http://www.hankcs.com/nlp/extraction-and-identification-of-mutual-information-about-the-phrase-based-on-information-entropy.html)





## 依存句法解析

**最大熵依存句法分析器**

​	这是一个判别式汉语句法分析器的Java实现，基于最大熵模型和最大生成树模型，实现了中文依存句法的自动分析，在封闭测试集（取自训练集）上取得了99.20%的准确率（UA），分析速度达到 570.7句/秒。 调用方法:

```python
print('----依存句法分析-----')
print(HanLP.parseDependency("徐先生还具体帮助他确定了把画雄鹰、松鼠和麻雀作为主攻目标。"))
# print(CRFDependencyParser.compute("把市场经济奉行的等价交换原则引入党的生活和国家机关政务活动中"))
'''
----依存句法分析-----
1	徐先生	徐先生	nh	nr	_	4	主谓关系	_	_
2	还	还	d	d	_	4	状中结构	_	_
3	具体	具体	a	ad	_	4	状中结构	_	_
4	帮助	帮助	v	v	_	0	核心关系	_	_
5	他	他	r	r	_	4	兼语	_	_
6	确定	确定	v	v	_	4	动宾关系	_	_
7	了	了	u	u	_	6	右附加关系	_	_
8	把	把	p	p	_	15	状中结构	_	_
9	画	画	v	v	_	8	介宾关系	_	_
10	雄鹰	雄鹰	n	n	_	9	动宾关系	_	_
11	、	、	wp	w	_	12	标点符号	_	_
12	松鼠	松鼠	n	n	_	10	并列关系	_	_
13	和	和	c	c	_	14	左附加关系	_	_
14	麻雀	麻雀	n	n	_	10	并列关系	_	_
15	作为	作为	v	v	_	6	动宾关系	_	_
16	主攻	主攻	v	vn	_	17	定中关系	_	_
17	目标	目标	n	n	_	15	动宾关系	_	_
18	。	。	wp	w	_	4	标点符号	_	_
'''
```

- 在封闭测试集上准确率有90%以上，但在开放测试集上则不理想。
- 算法详解： [《最大熵依存句法分析器的实现》](http://www.hankcs.com/nlp/parsing/to-achieve-the-maximum-entropy-of-the-dependency-parser.html)





## 智能推荐

>​	语义距离该部分是其实还是word2vec的应用，简单的来说，我们可以获取两个词的语义距离。因此我们也就获取了，同义改写的功能（语义距离最近的是哦同义词）。而基于此，我们当然还可以获得更多功能。不过本文只简介CoreSynonymDictionary该类的用法，下面的两个段代码一个是原作者给的，第二段代码是我查看源码之后给出了可以使用的方法。但是想要获得完整的功能还是直接使用word2vec好些，对于python而言或许自己编写或者使用gensim可能是更好的选择。
>
>
>
>word2vec
>
>- 词向量训练、加载、词语相似度计算、语义运算、查询、KMeans聚类
>- 文档语义相似度计算

[^参考]: https://blog.csdn.net/fontthrone/article/details/82830917



#### 语义距离

可以方便地计算两个词汇之间的距离，意义越相近，距离越小:

```python
from pyhanlp import *

print('----------------语义距离-------------------')
CoreSynonymDictionary = JClass("com.hankcs.hanlp.dictionary.CoreSynonymDictionary")

word_array = [
    "香蕉",
    "苹果",
    "白菜",
    "水果",
    "蔬菜",
    "自行车",
    "公交车",
    "飞机",
    "买",
    "卖",
    "购入",
    "新年",
    "春节",
    "丢失",
    "补办",
    "办理",
    "送给",
    "寻找",
    "孩子",
    "教室",
    "教师",
    "会计",
]
print("%-5s\t%-5s\t%-10s\t%-5s\n" % ("词A", "词B", "语义距离", "语义相似度"))
for a in word_array:
    for b in word_array:
        print("%-5s\t%-5s\t%-15d\t%-5.10f" % (a, b, CoreSynonymDictionary.distance(a, b),
            CoreSynonymDictionary.similarity(a, b)))
```

- 设想的应用场景是搜索引擎对词义的理解，词与词并不只存在“同义词”与“非同义词”的关系，就算是同义词，它们之间的意义也是有微妙的差别的。
- 算法上，为每个词分配一个语义ID，词与词的距离通过语义ID的差得到。语义ID通过《同义词词林扩展版》计算而来。

#### 智能推荐

> ​	该部分的内容之所以在一起是因其底层原理相同。文本推荐，语义距离，同义改写等都是基于word2vec的。因为作者HanLP源代码和博客都没有写该部分的原理。所以笔者大概查看了源码，发现功能实现原理非常之简单，就是根据文档与给定单词的距离进行打分，而这个语义距离就是从word2vec来的；这里的文本推荐属于 句子级别，从一系列句子中挑出与输入句子最相似的那一个。在搜索中是有用的。
>
> 有关内容你还可以参考这里，说明
>
> - [word2vec文档](https://github.com/hankcs/HanLP/wiki/word2vec)
> - [《word2vec原理推导与代码分析》](http://www.hankcs.com/nlp/word2vec.html)



也就是文本推荐(句子级别，从一系列句子中挑出与输入句子最相似的那一个):

```python
from pyhanlp import *

#智能推荐
print('----------------智能推荐-------------------')
Suggester = JClass("com.hankcs.hanlp.suggest.Suggester")
suggester = Suggester()
title_array = [
    "威廉王子发表演说 呼吁保护野生动物",
    "魅惑天后许佳慧不爱“预谋” 独唱《许某某》",
    "《时代》年度人物最终入围名单出炉 普京马云入选",
    "“黑格比”横扫菲：菲吸取“海燕”经验及早疏散",
    "日本保密法将正式生效 日媒指其损害国民知情权",
    "英报告说空气污染带来“公共健康危机”"
]

for title in title_array:
    suggester.addSentence(title)

print(suggester.suggest("陈述", 2))      # 语义
print(suggester.suggest("危机公关", 1))  # 字符
print(suggester.suggest("mayun", 1))   # 拼音
print(suggester.suggest("徐家汇", 1)) # 拼音

```



## 自定义词典



```python
from pyhanlp import *

print(HanLP.segment('世界之窗开幕式！'))
#[世界之窗/gi, 开幕式/n, ！/w]

# 添加mydict.txt：世界之窗 nz 100，更新 D:\myvenvs\nlpbagsvenv\Lib\site-packages\pyhanlp\static\hanlp.properties 配置root，删除.bin缓存文件
# 再次运行时间较长，也会报错，但不影响。【添加新词是发现在核心字典将“世界之窗”划分“gi”，可直接注释掉核心字典，或者修改核心字典的词性】
# 你会发现注释掉核心字典并删除所有bin文件，并没有效果。^_^【靠！原来是mydict.txt编码格式不是utf-8导致错误！】
# 如果你想修改的词在核心字典出现，修改核心字典，并删除所有bin文件，才生效。【并不一定是这样。】

#举栗子【修改核心字典】-----------------
# “新兴”、“产业链” 想要连接起来的分词效果，新增字典无效，因为核心字典有这2个词，必须在核心字典添加：“新兴产业链 nz 1000”(不一定，也可以在新增字典添加同样生效)

#举栗子【修改机构名字典】-----------------
# 新增机构名：济南扬帆贸易有限公司，在“机构名词典.txt”中新增，并删除CustomDictionary.txt.bin 后再次运行产生效果。

#举栗子【新增自定义字典】-----------------
# 新增 “李白 b 100”，李白出现在核心字典中，词性为nr(人名)，只能修改核心字典才能生效。

```

















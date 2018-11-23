# jieba

- 基于前缀词典实现高效的词图扫描，生成句子中汉字所有可能成词情况所构成的有向无环图 (DAG)
- 采用了动态规划查找最大概率路径, 找出基于词频的最大切分组合
- 对于未登录词，采用了基于汉字成词能力的 HMM 模型，使用了 Viterbi 算法
- **分词，词性标注，关键词抽取**



① 分词

```python
import jieba

seg_list = jieba.cut("欢迎科研人员、技术工程师、企事业单位与个人参与NLPIR平台的建设工作。", cut_all=False)
print("Default Mode: " + " ".join(seg_list))  # 精确模式
#Default Mode: 欢迎 科研人员 、 技术 工程师 、 企事业 单位 与 个人 参与 NLPIR 平台 的 建设 工作 。
```

② 词性标注

```python
import jieba.posseg as pseg

words = pseg.cut("欢迎科研人员、技术工程师、企事业单位与个人参与NLPIR平台的建设工作。")
result = []
for word, flag in words:
    result.append(word + '＿' +flag)
print(' '.join(result))
#欢迎＿v 科研人员＿n 、＿x 技术＿n 工程师＿n 、＿x 企事业＿n 单位＿n 与＿p 个人＿n 参与＿v NLPIR＿eng 平台＿n 的＿uj 建设＿vn 工作＿vn 。＿x
```

③ 关键词提取

```python
import jieba.analyse

#'基于 TF-IDF 算法的关键词抽取'
content = '重庆晚报6月11日报道  昨日，市政府公众信息网发布消息称，经2010年5月13日市政府第70次常务会议通过，给予文强、陈洪刚二人行政开除处分。\n今年4月14日，市第五中级人民法院以受贿罪，包庇、纵容黑社会性质组织罪，巨额财产来源不明罪，强奸罪数罪并罚判处文强死刑，剥夺政治权利终身，并处没收个人全部财产。5月21日，市高级人民法院对文强案二审宣判，依法驳回文强上诉，维持一审的死刑判决。\n2月25日，市公安局交警总队原总队长陈洪刚受贿案在市第五中级人民法院一审宣判。陈洪刚因犯受贿，包庇、纵容黑社会性质组织，巨额财产来源不明，伪造居民身份证罪，数罪并罚，被判处有期徒刑20年，没收个人财产40万元人民币，追缴赃款326万余元及不明来源财产584万余元。记者 李伟\n'
topK = 10
tags_tfidf = jieba.analyse.extract_tags(content, topK=topK, withWeight = False)
tags_textrank = jieba.analyse.textrank(content, topK=topK, withWeight = False)

print('\033[93m' + "Key Phrase Extraction Using TFIDF: " + '\033[0m' + ",".join(tags_tfidf))
print('\033[93m' + "Key Phrase Extraction Using TextRank: " + '\033[0m' + ",".join(tags_textrank))
# Key Phrase Extraction Using TFIDF: 文强,陈洪刚,巨额财产,来源不明,数罪并罚,包庇,一审,万余元,宣判,黑社会
# Key Phrase Extraction Using TextRank: 财产,性质,来源不明,死刑,纵容,没收,市政府,巨额财产,数罪并罚,包庇
```



# FoolNLTK

- 可能不是最快的开源中文分词，但很可能是最准的开源中文分词
- 基于BiLSTM模型训练而成
- 包含分词，词性标注，实体识别,　都有比较高的准确率
- 用户自定义词典
- 安装 pip install foolnltk



> 中文处理FoolNLTK，这是一个中文处理工具，提供BiLSTM来分词。
>
> 特性：基于BiLSTM模型来训练、准确度高、可用户自定义字典、支持自训练、允许batch处理、速度不是最快的，但接近市场上的大部分开源工具。

① 分词

```python
# segmentation
import fool

text = "一个傻子在北京"
print(fool.cut(text))
#['一个', '傻子', '在', '北京']
```

② 词性标注

```python
# pos tagging
import fool

text = "一个傻子在北京"
print(fool.pos_cut(text))
#[('一个', 'm'), ('傻子', 'n'), ('在', 'p'), ('北京', 'ns')]
```

③ 命名实体NER

```python
# NER
import fool 

text = "一个傻子在北京"
words, ners = fool.analysis(text)
print(ners)
#[(5, 8, 'location', '北京')]
```



# THULAC

THULAC（THU Lexical Analyzer for Chinese）由清华大学自然语言处理与社会人文计算实验室研制推出的一套中文词法分析工具包，具有**中文分词和词性标注功能**

<https://github.com/thunlp/THULAC-Python>

支持 python, c++, java



```python
import thulac 
thu1 = thulac.thulac()  #默认模式
# thu1 = thulac.thulac(seg_only=True)  #只进行分词，不进行词性标注
text = thu1.cut("欢迎科研人员、技术工程师、企事业单位与个人参与NLPIR平台的建设工作。", text=True)  #进行一句话分词
# text_list = thu1.cut("我爱北京天安门", text=False)  #进行一句话分词
print(text)
# Model loaded succeed
# 欢迎_v 科研_n 人员_n 、_w 技术_n 工程师_n 、_w 企事业_n 单位_n 与_p 个人_n 参与_v NLPIR_x 平台_n 的_u 建设_v 工作_v 。_w
```



# NLPIR-ICTCLAS

- use PyNLPIR A Python wrapper around the NLPIR/ICTCLASA
- **分词，词性标注，关键词抽取**

词性标注

```python
import pynlpir
pynlpir.open()

s = '欢迎科研人员、技术工程师、企事业单位与个人参与NLPIR平台的建设工作。'
pynlpir.segment(s)
#print(pynlpir.segment(s, pos_tagging=False))
termlist = pynlpir.segment(s, pos_tagging=True)
termlist2 = []
for word,nature in termlist:
    termlist2.append('_'.join([word,nature]))
print(' '.join(termlist2))
pynlpir.close()
# 欢迎_verb 科研_noun 人员_noun 、_punctuation mark 技术_noun 工程师_noun 、_punctuation mark 企事业_noun 单位_noun 与_conjunction 个人_noun 参与_verb NLPIR_noun 平台_noun 的_particle 建设_verb 工作_verb 。_punctuation mark
```

关键词抽取

```python
import pynlpir
pynlpir.open()

topK = 10
s = '重庆晚报6月11日报道  昨日，市政府公众信息网发布消息称，经2010年5月13日市政府第70次常务会议通过，给予文强、陈洪刚二人行政开除处分。\n今年4月14日，市第五中级人民法院以受贿罪，包庇、纵容黑社会性质组织罪，巨额财产来源不明罪，强奸罪数罪并罚判处文强死刑，剥夺政治权利终身，并处没收个人全部财产。5月21日，市高级人民法院对文强案二审宣判，依法驳回文强上诉，维持一审的死刑判决。\n2月25日，市公安局交警总队原总队长陈洪刚受贿案在市第五中级人民法院一审宣判。陈洪刚因犯受贿，包庇、纵容黑社会性质组织，巨额财产来源不明，伪造居民身份证罪，数罪并罚，被判处有期徒刑20年，没收个人财产40万元人民币，追缴赃款326万余元及不明来源财产584万余元。记者 李伟\n'
keywordList = pynlpir.get_key_words(s, weighted=False)
pynlpir.close()

print(','.join(keywordList[:topK]))
# 文强,陈洪刚,财产,市政府,判处,死刑,没收,人民法院,宣判,来源
```



# 哈工大LTP

- originally written in C++. provide interface for Python and Java
- Segmentation with customed lexicon

① 分词

```python
# Segmentation 分词
from pyltp import Segmentor
segmentor = Segmentor()
segmentor.load("/home/user/Python_notebooks/chinese_nlp/ltp-cws/ltp_data_v3.4.0/cws.model")
words = segmentor.segment("元芳你怎么看")
print(" ".join(words))
segmentor.release()

#<pyltp.VectorOfString object at 0x7ff89c21da50>
# 元芳 你 怎么 看
```

② 词性

```python
# POS Tagging 词性标注
# -*- coding: utf-8 -*-
from pyltp import Postagger
postagger = Postagger() # 初始化实例
postagger.load("/home/user/Python_notebooks/chinese_nlp/ltp-cws/ltp_data_v3.4.0/pos.model")  # 加载模型

words = ['元芳','你','怎么','看']
postags = postagger.postag(words)  # 词性标注

result = []
for word,pos in zip(words,postags):
    result.append(word + '_' + pos)
print(' '.join(result))
postagger.release()  # 释放模型
# 元芳_nh 你_r 怎么_r 看_v
```

③ 命名实体识别

```python
# Named Entity Recognition 命名实体识别
# 人名，机构名，地名
# -*- coding: utf-8 -*-
from pyltp import NamedEntityRecognizer
recognizer = NamedEntityRecognizer() # 初始化实例
recognizer.load("/home/user/Python_notebooks/chinese_nlp/ltp-cws/ltp_data_v3.4.0/ner.model")  # 加载模型

words = ['元芳', '你', '怎么', '看']
postags = ['nh', 'r', 'r', 'v']
netags = recognizer.recognize(words, postags)  # 命名实体识别

result = []
for word, netag in zip(words,netags):
    result.append(word+"_"+netag)
print(' '.join(result))
recognizer.release()  # 释放模型
# 元芳_S-Nh 你_O 怎么_O 看_O
```

备注：需要加载模型，目前没有看到可以自定义字典以及训练模型的入口，灵活性不可观。



# pyhanlp

资料查阅：http://hanlp.linrunsoft.com/

​	分词是HanLP最基础的功能，HanLP实现了许多种分词算法，每个分词器都支持特定的配置。标准分词是最常用的分词器，基于HMM-Viterbi实现，开启了中国人名识别和音译人名识别。
















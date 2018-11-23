#### Stanford NLP

简介：

​	Stanford NLP提供了一系列自然语言分析工具。它能够给出基本的词形，词性，不管是公司名还是人名等，格式化的日期，时间，量词，并且能够标记句子的结构，语法形式和字词依赖，指明那些名字指向同样的实体，指明情绪，提取发言中的开放关系等。

- 集成的NLP工具包，提供广泛的语法分析工具；

- 一种快速，强大的任意文本注释器，广泛用于生产；

- 一个现代的，定期更新的包，具有整体最高质量的文本分析；

- 支持一些主要（人类）语言，即支持多种语言；

- 适用于大多数主流现代编程语言的API；

- 能够作为简单的Web服务运行 。

  ​

#### **Python版本stanford nlp 安装**

• 1）安装stanford nlp自然语言处理包： pip install stanfordcorenlp
• 2）下载Stanford CoreNLP文件：https://stanfordnlp.github.io/CoreNLP/download.html （同个下载链接）
• 3）下载中文模型jar包： http://nlp.stanford.edu/software/stanford-chinesecorenlp-2018-10-05-models.jar（不是下载链接，仅做举栗子）【依赖java环境，java1.8】
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



#### 实现功能

```python
# 中文分词
nlp.word_tokenize(sentence)

# 词性标签
nlp.pos_tag(sentence)

# 语法解析
nlp.parse(sentence)

# 语法解析关系
nlp.dependency_parse(sentence)

# 命名实体识别 
nlp.ner(sentence)
```

备注：python接口暂无配置自定义词典入口，优化分词存在缺陷，java可配置：https://blog.csdn.net/dushenzhi/article/details/8734173



#### 词性标注

```bash
动词，形容词（4种）：VA，VC，VE，VV
1、谓词性形容词：VA
2、系动词：VC
3 、“有”作为主要动词：VE
4、其他动词：VV

名词（3种）：NR，NT，NN
1、专有名词：NR
2、时间名词：NT
3、其他名词：NN

定位（1种）：LC
方位词：LC

代词（1种）：PN

限定词和数词（3种）：DT，CD，OD
1、限定词：DT
2、基数词：CD
3、序列词：OD

度量词（1）：M
如“个”

副词（1）：AD 
如：仍然、很、最、大大、又、约

介词（1）：P
如：从、对

连词（2）：CC，CS
1、并列连接词：CC
如：与、和、或、或者、还是
2、从属连词：CS
如：如果/CS，……就/AD……

助词（8）：DEC，DEG，DER，DEV，SP，AS，ETC，SP，MSP
1、“的”作为补语标记/名词化标记：DEC（的，之）
2、“的”作为关联标记或所有格标记：DEG
3、 补语短语 得：DER
4、方式“地”：DEV
5、动态助词：AS
6、句末助词：SP
7、 ETC
8、其他助词：MSP

其他（8）：IJ，ON，PU，JJ，FW，LB，SB，BA
1、感叹词：IJ
2、拟声词：ON
3、长“被”结构：LB
4、短“被”结构：SB（仅包括口语中的“被，给”）
5、把字结构：BA
6、其他名词修饰语：JJ
7、外来词：FW
8、标点：PU
```

[^参考]: http://www.cnblogs.com/tonglin0325/p/6850901.html



#### 命名实体-八类实体

- ​	时间：Time
- ​	地点：Location
- ​	机构：Organization
- ​	人物：Person
- ​	金额：Money
- ​	百分比：Percent
- ​	日期：Date
- ​	其他：O

**备注**：由于是jar包封装好的模型，其框架存在局限性，缺点：目前局限于这八大类实体；优点：调用现成的训练模型，识别效果较好，降低开发时间成本，无需自行训练（训练需要大量的标注文本，收集难度大，需要耗费较大的大力资源。）





#### 耗时性能测试

测试各数量级的实体抽取耗费时长：

| 条数    | 大小 | 时间(s)                        |
| ------- | ---- | ------------------------------ |
| 11740   | 1.5M | 293.91655468940735 （4.89min） |
| 61140   | 3.1M | 767.0257866382599（12.78min）  |
| 215032  | 17M  | 3341.131045103073（55.68min）  |
| 1009437 | 90M  | 16996.65203809738（4.72h）     |

备注：万级数据量运行时间成本属可接受范围，百万级数据量运行时间不可用。局限：大级别数据量不适用该模型。










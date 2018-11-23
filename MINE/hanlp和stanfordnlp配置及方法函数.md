#### hanlp和stanfordnlp配置

nlp_conf.py

```python
# -*- coding: utf-8 -*-
# @Time    : 2018/11/5 16:58
# @File    : nlp_conf.py
# @Author  : hnq
''' stanford_nlp '''

import os
from stanfordcorenlp import StanfordCoreNLP
from jpype import *

''' stanford_nlp '''
root_path=os.path.abspath(os.path.join(os.getcwd(), "../.."))
# stanford_nlp = StanfordCoreNLP(root_path+os.sep+"models"+os.sep+'stanfordnlp', lang='zh')
stanford_nlp = StanfordCoreNLP(root_path+os.sep+"models"+os.sep+'stanfordnlp', lang='zh')

'''HanLP Tokenizer NLPTokenizer'''
hanlp_path=root_path+os.sep+"models"+os.sep+'hanlp'
djclass_path = "-Djava.class.path=" + hanlp_path + os.sep + "hanlp-1.6.8.jar;" + hanlp_path # Linux需把;改成:
startJVM(getDefaultJVMPath(),djclass_path,"-Xms1g","-Xmx1g")

Tokenizer = JClass('com.hankcs.hanlp.tokenizer.StandardTokenizer') # 分词
HanLP = JClass('com.hankcs.hanlp.HanLP') # hanlp分词
NLPTokenizer = JClass('com.hankcs.hanlp.tokenizer.NLPTokenizer') # 词性标注

# 要过滤的词性
# drop_pos_set=set(['xu','xx','y','yg','wh','wky','wkz','wp','ws','wyy','wyz','wb','u','ud','ude1','ude2','ude3','udeng','udh'])
drop_pos_set=set(['xu','xx','y','yg','wh','wky','wkz','wp','ws','wyy','wyz','wb','u','ud','ude1','ude2','ude3','udeng','udh','p','rr'])

huanhang=set(['。','？','！','?'])
keep_pos="q,qg,qt,qv,s,t,tg,g,gb,gbc,gc,gg,gm,gp,mg,Mg,n,an,ude1,nr,ns,nt,nz,nb,nba,nbc,nbp,nf,ng,nh,nhd,o,nz,nx,ntu,nts,nto,nth,ntch,ntcf,ntcb,ntc,nt,nsf,ns,nrj,nrf,nr2,nr1,nr,nnt,nnd,nn,nmc,nm,nl,nit,nis,nic,ni,nhm,nhd"
keep_pos_nouns=set(keep_pos.split(","))
keep_pos_v="v,vd,vg,vf,vl,vshi,vyou,vx,vi,vn"
keep_pos_v=set(keep_pos_v.split(","))
keep_pos_p=set(['p','pbei','pba'])
merge_pos=keep_pos_p|keep_pos_v
keep_flag=set(['：','，','？','。','！','；','、','-','.','!',',',':',';','?','(',')','（','）','<','>','《','》'])
drop_pos_set=set(['xu','xx','y','yg','wh','wky','wkz','wp','ws','wyy','wyz','wb','u','ud','ude1','ude2','ude3','udeng','udh'])

```



#### hanlp和stanfordnlp处理分词函数

tools.py

```python
#encoding=utf8
from busi_analy.config.nlp_conf import Tokenizer, HanLP, drop_pos_set, stanford_nlp, NLPTokenizer

# hanlp标准分词+词性标注
def to_string(sentence,return_generator=False):
    if return_generator:
        return (word_pos_item.toString().split('/') for word_pos_item in Tokenizer.segment(sentence)) # generator格式输出
    else:
        return [(word_pos_item.toString().split('/')[0],word_pos_item.toString().split('/')[1]) for word_pos_item in Tokenizer.segment(sentence)]
        # like[('新疆', 'ns'),……]

# hanlp分词+词性标注
def to_string_hanlp(sentence,return_generator=False):
    if return_generator:
        return (word_pos_item.toString().split('/') for word_pos_item in HanLP.segment(sentence)) # like[ ('上市公司', 'nis'),……]
    else:
        return [(word_pos_item.toString().split('/')[0],word_pos_item.toString().split('/')[1]) for word_pos_item in Tokenizer.segment(sentence)]

# 过滤空字符串以及不需要的词性字段
def seg_sentences(sentence,with_filter=True,return_generator=True):
    segs=to_string(sentence,return_generator=return_generator)
    # 过滤无用词性
    if with_filter:
        g = [word_pos_pair[0] for word_pos_pair in segs if len(word_pos_pair)==2 and word_pos_pair[0]!=' ' and word_pos_pair[1] not in drop_pos_set]
    else:
        g = [word_pos_pair[0] for word_pos_pair in segs if len(word_pos_pair)==2 and word_pos_pair[0]!=' ']
    return iter(g) if return_generator else g # 仅返回词，不带词性

# stanford_nlp命名实体识别--------------------------
def ner_stanford(raw_sentence,return_list=True):
    if len(raw_sentence.strip())>0:
        return stanford_nlp.ner(raw_sentence) if return_list else iter(stanford_nlp.ner(raw_sentence))

# hanlp_NLPTokenizer词性标注------------------------------
def ner_hanlp(raw_sentence,return_list=True):
    if len(raw_sentence.strip())>0:
        return NLPTokenizer.segment(raw_sentence) if return_list else iter(NLPTokenizer.segment(raw_sentence))

# stanford_nlp词性标注------------------------------
def cut_stanford(raw_sentence,return_list=True):
    if len(raw_sentence.strip())>0:
        return stanford_nlp.pos_tag(raw_sentence) if return_list else iter(stanford_nlp.pos_tag(raw_sentence))

#------------------------------------------------
def to_string_1(sentence, return_generator=False):
    if return_generator:
        return (word_pos_item.toString().split('/') for word_pos_item in Tokenizer.segment(sentence)) # 带词性的分词，返回一个迭代器
    else:
        return " ".join([word_pos_item.toString().split('/')[0] for word_pos_item in Tokenizer.segment(sentence)]) # 只取分词，like：今天 股市 上涨
# 去除空行，只取分词不含词性
def cut_hanlp(raw_sentence,return_list=True):
    if len(raw_sentence.strip())>0: # 去除空行
        return to_string_1(raw_sentence) if return_list else iter(to_string_1(raw_sentence)) # 只取分词，不含词性
# 去除空行，取分词+词性
def cut_hanlp_1(raw_sentence,return_list=True):
    if len(raw_sentence.strip())>0: # 去除空行
        return to_string(raw_sentence) if return_list else iter(to_string(raw_sentence))

if __name__ == '__main__':
    # str1='因为我都是保底收益的，银行(昆明、南昌、长沙、株洲、新疆、银川、陕西淮南商行等)的资金是以拆借的名义进来的，德隆产业中非上市公司部分的连手续都没办。我融的这些钱一共有50至60亿元，我不就成了融资员了吗?'
    # str1='今天股市上涨50%到80%，于2018年以来，还没有涨到90%，乐山金融公司还是可以继续保持业绩,北京大学怎么走。'
    str1='请问世界之窗怎么走？'
    # str1='请问北京大学怎么走？'
    print(ner_hanlp(str1))
    # print(ner_stanford(str1))
    #[('今天', 'DATE'), ('是', 'O'), ('个', 'O'), ('好', 'O'), ('日子', 'O'), (',', 'O'), ('广州', 'CITY'), ('是', 'O'), ('个', 'O'), ('好', 'O'), ('地方', 'O'), ('，', 'O'), ('张晓丽', 'PERSON'), ('明天', 'DATE'), ('过来', 'O'), ('长隆', 'FACILITY'), ('游乐园', 'FACILITY'), ('玩耍', 'O'), ('！', 'O')]
    print('-------------------')
    # print(ner_hanlp(str))#[今天/t, 是/v, 个/q, 好日子/n, ,/w, 广州/ns, 是/v, 个/q, 好/a, 地方/n, ，/w, 张晓丽/nr, 明天/t, 过来/v, 长/a, 隆/a, 游乐园/n, 玩耍/v, ！/w]
    print('-------------------')
    # print(cut_stanford(str))#[('今天', 'NT'), ('是', 'VC'), ('个', 'M'), ('好', 'JJ'), ('日子', 'NN'), (',', 'PU'), ('广州', 'NR'), ('是', 'VC'), ('个', 'M'), ('好', 'JJ'), ('地方', 'NN'), ('，', 'PU'), ('张晓丽', 'NR'), ('明天', 'NT'), ('过来', 'VV'), ('长隆', 'NR'), ('游乐园', 'NN'), ('玩耍', 'VV'), ('！', 'PU')]

    # print(to_string(str1)) #[('因为', 'c'), ('我', 'rr')……]
    print('----------------------')
    # print(to_string_hanlp(str1)) #[('因为', 'c'), ('我', 'rr')……]
    print('----------------------')
    # print(seg_sentences(str1,return_generator=False)) # ['因为', '我', '都', '是',……]
    # print(ner_hanlp(str1))
    # s1=to_string_1(str1)
    # s2=cut_hanlp(str1)
    # print(s1,s2)
```




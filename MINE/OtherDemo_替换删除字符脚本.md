#### 上代码

```python
#encoding=utf8

from nltk import Tree, ProbabilisticTree
import nltk,re

from busi_analy.config.nlp_conf import stanford_nlp

grammer = "NP: {<DT>?<JJ>*<NN>}"
cp = nltk.RegexpParser(grammer) #生成规则
pattern=re.compile(u'[^a-zA-Z\u4E00-\u9FA5]')
pattern_del=re.compile('(\a-zA-Z0-9+)')

def _replace_c(text):
    """
    输入需要被替换字符；替换成的字符；要删除的字符
    :param text: 文本
    :return: 替换或者删除特定字符后输出的文本
    """
    intab = ",?!()"
    outtab = "，？！（）"    
    deltab = " \n<li>< li>+_-.><li \U0010fc01 _"
    trantab=text.maketrans(intab, outtab,deltab)
    return text.translate(trantab)

def parse_sentence(text):
    text=_replace_c(text)
    try:
        if len(text.strip())>6:
            return Tree.fromstring(stanford_nlp.parse(text.strip()))
    except:
        pass
    '''
    return like:
    (ROOT
  (IP
    (IP (VP (ADVP (AD 又)) (VP (VV 到) (AS 了) (NP (NN 高峰期)))))
    (PU ，)
    (IP (NP (DNP (NP (NT 未来)) (DEG 的)) (NP (NN 股市))) (VP (VA 怎么样)))
    (PU ？)
    ……
    '''

def pos(text):
    text=_replace_c(text)
    if len(text.strip())>6:
        return stanford_nlp.pos_tag(text)
    else:
        return False
'''
return like:[('又', 'AD'), ('到', 'VV'), ('了', 'AS'), ('高峰期', 'NN'), ('，', 'PU'), ('未来', 'NT'), ('的', 'DEG'), ('股市', 'NN'), ('怎么样', 'VA'), ('？', 'PU'), ('！', 'PU'), ('（', 'PU'), ('还', 'AD'), ('不错', 'VA'), ('）', 'PU'), ('https', 'URL'), (':', 'PU'), ('//wwwwbaducom', 'NR')]
'''

def denpency_parse(text):
    return stanford_nlp.dependency_parse(text)
'''
return like:[('ROOT', 0, 2), ('advmod', 2, 1), ('aux:asp', 2, 3), ('dobj', 2, 4), ('punct', 2, 5), ('nmod', 8, 6), ('case', 6, 7), ('nsubj', 9, 8), ('dep', 2, 9), ('punct', 2, 10), ('ROOT', 0, 1), ('ROOT', 0, 3), ('punct', 3, 1), ('advmod', 3, 2), ('punct', 3, 4), ('amod', 6, 5), ('dep', 13, 6), ('punct', 13, 7), ('dep', 13, 8), ('punct', 13, 9), ('dep', 13, 10), ('punct', 13, 11), ('punct', 13, 12), ('punct', 3, 13)]
'''
# from nltk.chunk.regexp import *

if __name__ == '__main__':
    print('-----开始测试------')
    text='又到了高峰期,未来的股市怎么样?!(还不错)<li>https://wwww.baidu.com< li>+_'
    # print(_replace_c(text))# 又到了高峰期，未来的股市怎么样？！（还不错）https://wwwwbaiducom
    # print(parse_sentence(text))
    # print(pos(text))
    print(denpency_parse(text))
    # print(stanford_nlp.pos_tag('我是祖国'))

```


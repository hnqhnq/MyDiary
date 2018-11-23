#### Gensim搭建主题模型



```python
from gensim import corpora, models
import pandas as pd

def lda_ana(inputfile):
    pos=pd.read_csv(inputfile, encoding='utf-8', header=None)  # 读入数据
    pos_dict = corpora.Dictionary([pos.iloc[:,0]]) #建立字典 # 没有[]会报错，需变成unicode编码
    # print(pos_dict)
    pos_corpus = [pos_dict.doc2bow(i) for i in [pos.iloc[:,0]]] # 建立语料库 # 没有[]会报错，需变成unicode编码
    pos_lda = models.LdaModel(pos_corpus, num_topics = 3, id2word = pos_dict) # 建立模型
    for i in range(3):
        print(pos_lda.print_topic(i)) # 输出每个主题

if __name__ == '__main__':
    inputfile = '../otherfiles/meidi_myPosRes_clean2.txt'  # 去除停用词的文档
    lda_ana(inputfile)
```


### 文本特征提取

---

**特征提取**

- 将文本数据转化成特征向量的过程
- 比较常用的文本特征表示法为词袋法



**词袋法**
	不考虑词语出现的顺序，每个出现过的词汇单独作为一列特征，这些不重复的特征词汇集合为词表，每一个文本都可以在很长的词表上统计出一个很多列的特征向量，如果每个文本都出现的词汇，一般被标记为“停用词”不计入特征向量，主要有两个api来实现 CountVectorizer 和 TfidfVectorizer。

​	这两个类都是特征数值计算的常见方法。对于每一个训练文本，CountVectorizer只考虑每种词汇在该训练文本中出现的频率，而TfidfVectorizer除了考量某一词汇在当前训练文本中出现的频率之外，同时关注包含这个词汇的其它训练文本数目的倒数。相比之下，训练文本的数量越多，TfidfVectorizer这种特征量化方式就更有优势。



#### CountVectorizer

​	只考虑词汇在文本中出现的频率



#### TfidfVectorizer

​	除了考量某词汇在文本出现的频率，还关注包含这个词汇的所有文本的数量，能够削减高频没有意义的词汇出现带来的影响, 挖掘更有意义的特征。

​	相比之下，文本条目越多，Tfid的效果会越显著。

https://blog.csdn.net/the_lastest/article/details/79093407

|    参数表     |           作用           |
| :-----------: | :----------------------: |
|  stop_words   | 停用词表；自定义停用词表 |
| token_pattern |        过滤规则；        |

|   属性表    |      作用      |
| :---------: | :------------: |
| vocabulary_ | 词汇表；字典型 |
|    idf_     |   返回idf值    |
| stop_words_ |  返回停用词表  |

|      方法表      |           作用           |
| :--------------: | :----------------------: |
| fit_transform(X) | 拟合模型，并返回文本矩阵 |



#### TfidfVectorizer举栗子[网上]

```python
# -*- coding: utf-8 -*-
from sklearn.feature_extraction.text import TfidfVectorizer
X_test = ['没有 你 的 地方 都是 他乡','没有 你 的 旅行 都是 流浪']
stopword = [u'都是'] #自定义一个停用词表，如果不指定停用词表，则默认将所有单个汉字视为停用词；
#但可以设token_pattern=r"(?u)\b\w+\b"，即不考虑停用词

tfidf=TfidfVectorizer(token_pattern=r"(?u)\b\w\w+\b",stop_words=stopword)
weight=tfidf.fit_transform(X_test).toarray()
word=tfidf.get_feature_names()

print 'vocabulary list:\n'
for key,value in tfidf.vocabulary_.items():
    print key,value

print 'IFIDF词频矩阵:\n'
print weight

for i in range(len(weight)):  
# 打印每类文本的tf-idf词语权重，第一个for遍历所有文本，
#第二个for便利某一类文本下的词语权重
    print u"-------这里输出第", i, u"类文本的词语tf-idf权重------"
    for j in range(len(word)):
        print word[j], weight[i][j]#第i个文本中，第j个次的tfidf值


>>
>>
vocabulary list:
没有 3
他乡 0
地方 1
旅行 2
流浪 4

IFIDF词频矩阵:
[[ 0.6316672   0.6316672   0.          0.44943642  0.        ]
 [ 0.          0.          0.6316672   0.44943642  0.6316672 ]]
-------第 0 类文本的词语tf-idf权重------
他乡 0.631667201738
地方 0.631667201738
旅行 0.0
没有 0.449436416524
流浪 0.0
-------第 1 类文本的词语tf-idf权重------
他乡 0.0
地方 0.0
旅行 0.631667201738
没有 0.449436416524
流浪 0.631667201738
--------------------- 
作者：亡城 
来源：CSDN 
原文：https://blog.csdn.net/the_lastest/article/details/79093407 
版权声明：本文为博主原创文章，转载请附上博文链接！
```



#### CountVectorizer举栗子[网上]

```python
# -*- coding: utf-8 -*-
from sklearn.feature_extraction.text import CountVectorizer

X_test = [u'没有 你 的 地方 都是 他乡',u'没有 你 的 旅行 都是 流浪']

count_vec=CountVectorizer(token_pattern=r"(?u)\b\w\w+\b")
print count_vec.fit_transform(X_test).toarray()
print count_vec.fit_transform(X_test)
print '\nvocabulary list:\n'
for key,value in count_vec.vocabulary_.items():
    print key,value

>>
>>
[[1 1 0 1 0 1]
 [0 0 1 1 1 1]]
  (0, 0)    1
  (0, 5)    1
  (0, 1)    1
  (0, 3)    1
  (1, 4)    1
  (1, 2)    1
  (1, 5)    1
  (1, 3)    1

vocabulary list:
他乡 0
地方 1
旅行 2
没有 3
都是 5
流浪 4
```



#### 代码展示

```python
from sklearn.feature_extraction.text import CountVectorizer
# input corpus likes ['今天 一个 美好 一天','天气 很好 可以 郊游'] 
def bow_extractor(corpus, ngram_range=(1, 1)): 
    vectorizer = CountVectorizer(min_df=1, ngram_range=ngram_range)
    features = vectorizer.fit_transform(corpus)
    return vectorizer, features


from sklearn.feature_extraction.text import TfidfTransformer

def tfidf_transformer(bow_matrix):
    transformer = TfidfTransformer(norm='l2',
                                   smooth_idf=True,
                                   use_idf=True)
    tfidf_matrix = transformer.fit_transform(bow_matrix)
    return transformer, tfidf_matrix


from sklearn.feature_extraction.text import TfidfVectorizer
# input corpus likes ['今天 一个 美好 一天','天气 很好 可以 郊游'] 
def tfidf_extractor(corpus, ngram_range=(1, 1)):
    vectorizer = TfidfVectorizer(min_df=1,
                                 norm='l2',
                                 smooth_idf=True,
                                 use_idf=True,
                                 ngram_range=ngram_range)
    features = vectorizer.fit_transform(corpus)
    return vectorizer, features
```


## NLTK提取实体、词性

#### **生成结构树**

- result_tree=nltk.RegexpParser(grammar).parse

- grammar格式：treename:{<pos1|pos2|pos3>+}{……}{……}

- grammar规律：

  ```python
  # 优先级：* or ? > + 
  grammar = "NP: {<DT>*<JJ>}" ——> (NP 我/DT 是/JJ)、(NP 薪资/JJ) 即取 DT+JJ、JJ
  grammar = "NP: {<DT>+<JJ>}" ——> (NP 我/DT 是/JJ) 即取 DT+JJ
  grammar = "NP: {<DT>?<JJ>}" ——> (NP 我/DT 是/JJ)、(NP 薪资/JJ) 即取 DT+JJ、JJ
  grammar = "NP: {<DT><JJ>}" ——> (NP 我/DT 是/JJ) 即取 DT+JJ
  #-------------------------------------------------------------
  grammar = "NP: {<DT>*<JJ>*<NN>}"
  ——> (NP 我/DT 是/JJ 一个/NN)、(NP 上线/DT 学习/NN)、(NP 薪资/JJ 新增/NN)

  grammar = "NP: {<DT>？<JJ>？<NN>}"
  ——> (NP 我/DT 是/JJ 一个/NN)、(NP 上线/DT 学习/NN)、(NP 薪资/JJ 新增/NN)

  grammar = "NP: {<DT>+<JJ>+<NN>}"
  ——> (NP 我/DT 是/JJ 一个/NN)

  grammar = "NP: {<DT><JJ><NN>}"
  ——> (NP 我/DT 是/JJ 一个/NN)
  #-------------------------------------------------------------
  grammar = "NP: {<DT>+<JJ>?<NN>}"
  ——> (NP 我/DT 是/JJ 一个/NN)、(NP 上线/DT 学习/NN) 即取 DT+JJ+NN、DT+NN

  grammar = "NP: {<DT>+<JJ>*<NN>}"
  ——> (NP 我/DT 是/JJ 一个/NN)、(NP 上线/DT 学习/NN)

  grammar = "NP: {<DT>*<JJ>?<NN>}"
  ——> (NP 我/DT 是/JJ 一个/NN)、(NP 上线/DT 学习/NN)、(NP 薪资/JJ 新增/NN)、(NP 是/NN)

  grammar = "NP: {<DT>*<JJ>+<NN>}"
  ——> (NP 我/DT 是/JJ 一个/NN)、(NP 薪资/JJ 新增/NN)

  grammar = "NP: {<DT><JJ>?<NN>}"
  ——> (NP 我/DT 是/JJ 一个/NN)、(NP 上线/DT 学习/NN)

  ```

**举栗子1**

```python
sentence = [("我", "DT"), ("是", "JJ"), ("一个", "NN"), ("好学生", "NN"), ("如果", "VBD"), ("没有", "IN"),
            ("上线", "DT"), ("学习", "NN"), ("薪资", "JJ"), ("新增", "NN"),]
# grammar = "NP: {<DT>*<JJ>?<NN>}"
grammar = "NP: {<DT>+<JJ>?<NN>}"
cp = nltk.RegexpParser(grammar)
result = cp.parse(sentence)
print(result)
```

**举栗子2**

```python
import nltk
grammar_dict ={'hanlp_keep':
     r"""
     NAME:{<nr|nr1|nr2|nrf|nrj>+}
     NP:
     {<m|mg|Mg|mq|q|qg|qt|qv|s|>*<q|qg|qt|qv>}
     VP:{<v|vd|vg|vf|vl|vshi|vyou|vx|vi|vn>+}
     TIME:{<m>+<qt>+<m|t>+<m>+<b>+}
     {<m>+<qt|w>+<m>?<qt>}
     {<t|qt>}
     {<t>+<m>?<b>+}
     """,
 }

cp = nltk.RegexpParser(grammar_dict['hanlp_keep'])
result = cp.parse(sentence)
# like：Tree('S', [Tree('DATE', [('2018年', 'DATE')]), ('，', 'O'), Tree('PERSON', [('金正恩', 'PERSON')]), ('来到', 'O'), Tree('LOCATION', [('美国', 'COUNTRY'), ('洛杉矶', 'CITY')]), ('，', 'O'), ('他', 'O'), ('有', 'O'), ('80％', 'NUMBER'), ('的', 'O'), ('概率', 'O'), ('能够', 'O'), ('进球', 'O'), ('，', 'O'), ('花', 'O'), ('了', 'O'), ('1000', 'MONEY'), ('元', 'MONEY'), ('，', 'O'), ('50', 'MONEY'), ('美元', 'MONEY'), ('，', 'O'), ('在', 'O'), Tree('ORGANIZATIONL', [('斯坦福', 'ORGANIZATION'), ('大学', 'ORGANIZATION')]), ('开展', 'O'), ('了', 'O'), ('旅游', 'O'), ('会', 'O'), ('，', 'O'), Tree('DATE', [('20:15', 'TIME')]), ('开', 'O'), ('完', 'O'), ('会议', 'O'), ('。', 'O')])

# result.draw()
```



#### **提取树节点**

- for node in result_tree,if sign grammar,type(node) is nltk.Tree,else you can sign it others.
- node like : Tree('DATE', [('2018年', 'DATE')])
- node.label() like:'DATE'
- node.leaves() like :  [('金新信托', 'nz')]

```python
text = ''
time = ''
name = ''
for node in result:
    if type(node) is nltk.Tree:
        if node.label() == 'TIME':
            time = time + " " + ''.join([i[0] for i in node])
        if node.label() == 'NAME':
            name = name + " " + ''.join([i[0] for i in node])
        if node.label() == 'NP':
            # node.leaves() like [('金新信托', 'nz')]
            text += ''.join(node_child[0].strip() for node_child in node.leaves()) + "/NP" + 3 * " "
            # print('node.leaves()==',node.leaves())
            
      else:  # node[0] is word ,node[1] is pos
            if node[1] in keep_pos_p:  # 保留介词
                text += node[0].strip() + "/PP" + 3 * " "
            if node[0] in punctuation:  # 标点符号
                text += node[0].strip() + "/O" + 3 * " "
            if node[1] not in merge_pos:  # 除了介词 、动词，其余全部打标签： O。
                text += node[0].strip() + "/O" + 3 * " "
```



#### **形成语法树**

—— (目测非常的复杂麻烦)

- sroot = stanford_nlp.parse(sentence) # 语法解析
- tree_in = nltk.Tree.fromstring(sroot) # nltk将树节点汇集 ，type(tree_in)=<class 'nltk.tree.Tree'>
- isinstance(tree_in, nltk.tree.Tree) # 判断类型是否一致，一致为True

```python
from nltk import Tree, ProbabilisticTree
stanford_nlp = StanfordCoreNLP(r'D:\Py_work\nlpBags\StandfordNLP\stanfordnlp_my', lang='zh')

sentence='今天庆大公司股市上涨50%到80。'
# 语法解析
sroot=stanford_nlp.parse(sentence)
'''sroot return like :
(ROOT
  (IP
    (VP
      (NP (NT 今天))
      (VP (VV 庆)
        (IP
          (NP
            (NP
              (ADJP (JJ 大))
              (NP (NN 公司)))
            (NP (NN 股市)))
          (VP (VV 上涨)
            (QP (CD 50%)
              (CC 到)
              (CD 80))))))
    (PU 。)))
'''
tree_in=Tree.fromstring(sroot) # 聚集 np vp  # type(tree_in)=<class 'nltk.tree.Tree'>
'''Tree.fromstring(sroot) return like :
(ROOT
  (IP
    (VP
      (NP (NT 今天))
      (VP
        (VV 庆)
        (IP
          (NP (NP (ADJP (JJ 大)) (NP (NN 公司))) (NP (NN 股市)))
          (VP (VV 上涨) (QP (CD 50%) (CC 到) (CD 80))))))
    (PU 。)))
'''

if isinstance(tree_in, nltk.tree.Tree): #nltk.tree.Tree=<class 'nltk.tree.Tree'>
	pass

# 循环遍历tree，剔除ROOT，IP，提取NP,VP--------------------------------------
def search(tree_in):
    if not isinstance(tree_in,nltk.tree.Tree): # nltk.tree.Tree= <class 'nltk.tree.Tree'>
        return False    
    vp_pair=[]  
    stack=[]
    stack.append(tree_in) # 把树装进列表里
    current_tree=''
    while stack:  # 若非空列表[]，则执行
        tree=stack.pop()  # 把树从列表里取出
        if isinstance(tree,nltk.tree.Tree) and tree.label()=="ROOT": # 循环剔除ROOT
            for i in range(len(tree)):
                stack.append(tree[i])
        if isinstance(tree,nltk.tree.Tree) and tree.label()=="IP": # 循环剔除IP
            for i in range(len(tree)):
                stack.append(tree[i])
        if isinstance(tree,nltk.tree.Tree) and tree.label()=="VP":
            duplicate=[]
            if len(tree)>=2:
                for i in range(1,len(tree)):
                    if tree[0].label()=='VV' and tree[i].label()=="NP":
                        #[Tree('NT', ['今天'])——》tree[0].label()：标签; trtree[0].leaves()：word
                        verb=''.join(tree[0].leaves())
                        noun=get_noun_chunk(tree[i])
                        if verb and noun:
                            vp_pair.append((verb,noun))
                            duplicate.append(noun)
                    elif tree[0].label()=='VV' and tree[i].label()!="NP":
                        noun=get_vv_loss_np(tree)
                        verb=''.join(tree[0].leaves())
                        if verb and noun and noun not in duplicate:
                            duplicate.append(noun)
                            vp_pair.append((verb,noun))
    if vp_pair:
        return vp_pair
    else:
        return 'Not NP or VV'


stanford_nlp.close()
```



#### CFG自定义语句树

1. grammar = nltk.CFG.fromstring("""
   	     S -> N VP 
                VP -> V NP | V NP | V N | V V N
                V -> "尊敬" | "爱护" | "保护" | "崇拜"
                N -> "我们" | "老师" 
                """)      			——> # 定制语法规则

2. rd_parser = nltk.RecursiveDescentParser(grammar)  # 递归下降解析器  

   <nltk.parse.recursivedescent.RecursiveDescentParser ……

3. sent=['我们','崇拜','尊敬','老师' ]

4. return result or tree

   ```python
   result = []
   for i, tree in enumerate(rd_parser.parse(sent)):
       result.append(tree)
   #-------------------------------------------------
   for tree in rd_parser.parse(sent):  
       print(tree)
   ```



```python
from os.path import curdir

def exec_cmd(cmd):
    p = os.subprocess.Popen(
        cmd,
        stdout=os.subprocess.PIPE,
        stderr=os.subprocess.PIPE,
        shell=True,
        env=ENVIRON)
    out, err = p.communicate()
    return out, err
import nltk,os,jieba
from nltk.tree import Tree
from nltk.draw import TreeWidget
from nltk.draw.tree import TreeView
from nltk.draw.util import CanvasFrame
from nltk.parse import RecursiveDescentParser
class Cfg():

    def setUp(self):
        pass

    def tearDown(self):
        pass

    def test_sample(self):
        print("test_sample")
        # This is a CFG grammar, where:
        # Start Symbol : S
        # Nonterminal : NP,VP,DT,NN,VB
        # Terminal : "I", "a" ,"saw" ,"dog"
        grammar = nltk.grammar.CFG.fromstring("""
            S -> NP VP
            NP -> DT NN | NN
            VP -> VB NP
            DT -> "a"
            NN -> "I" | "dog"
            VB -> "saw"
        """)
        sentence = "I saw a dog".split()
        parser = RecursiveDescentParser(grammar)
        final_tree = parser.parse(sentence)

        for i in final_tree:
            print(i)

    def test_nltk_cfg_qtype(self):
        print("test_nltk_cfg_qtype")
        gfile = os.path.join(
            curdir,
            os.path.pardir,
            "config",
            "grammar.question-type.cfg")
        question_grammar = nltk.data.load('file:%s' % gfile)

        def get_missing_words(grammar, tokens):
            """
            Find list of missing tokens not covered by grammar
            """
            missing = [tok for tok in tokens
                       if not grammar._lexical_index.get(tok)]
            return missing

        sentence = "what is your name"

        sent = sentence.split()
        missing = get_missing_words(question_grammar, sent)
        target = []
        for x in sent:
            if x in missing:
                continue
            target.append(x)

        rd_parser = RecursiveDescentParser(question_grammar)
        result = []
        print("target: ", target)
        for tree in rd_parser.parse(target):
            result.append(x)
            print("Question Type\n", tree)

        if len(result) == 0:
            print("Not Question Type")

    def cfg_en(self):
        print("test_nltk_cfg_en")
        grammar = nltk.CFG.fromstring("""
         S -> NP VP
         VP -> V NP | V NP PP
         V -> "saw" | "ate"
         NP -> "John" | "Mary" | "Bob" | Det N | Det N PP
         Det -> "a" | "an" | "the" | "my"
         N -> "dog" | "cat" | "cookie" | "park"
         PP -> P NP
         P -> "in" | "on" | "by" | "with"
         """)

        sent = "Mary saw Bob".split()

        rd_parser = RecursiveDescentParser(grammar)

        result = []

        for i, tree in enumerate(rd_parser.parse(sent)):
            result.append(tree)

        assert len(result) > 0, " CFG tree parse fail."

        print(result)

    def cfg_zh(self):
       
        grammar = nltk.CFG.fromstring("""
             S -> N VP 
             VP -> V NP | V NP | V N | V V N
             V -> "尊敬" | "爱护" | "保护" | "崇拜"
             N -> "我们" | "老师" 
             """)

        sent = "我们 崇拜 尊敬 老师".split()
        rd_parser = RecursiveDescentParser(grammar)

        result = []

        for i, tree in enumerate(rd_parser.parse(sent)):
            result.append(tree)
            print("Tree [%s]: %s" % (i + 1, tree))

        assert len(result) > 0, "Can not recognize CFG tree."
        if len(result) == 1 :
            print("Draw tree with Display ...")
            result[0].draw()
        else:
            print("WARN: Get more then one trees.")

        print(result)

if __name__ == '__main__':
    cfg=Cfg()
    # cfg.cfg_en()
    cfg.cfg_zh()
```






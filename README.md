# Text\_Matching（文本匹配算法）

文本匹配是自然语言理解中的一个核心问题，它可以应用于大量的自然语言处理任务中，例如信息检索、问答系统、复述问题、对话系统、机器翻译等等。这些自然语言处理任务在很大程度上都可以抽象成文本匹配问题，比如**信息检索可以归结为搜索词和文档资源的匹配，问答系统可以归结为问题和候选答案的匹配，复述问题可以归结为两个同义句的匹配，对话系统可以归结为前一句对话和回复的匹配，机器翻译则可以归结为两种语言的匹配**。

## 1. 词汇（Lexical）层面

解决词汇层面的匹配问题，或者说词汇层面的相似度问题。

但是，基于词汇重合度的匹配算法有很大的局限性

* 语言的多义同义问题。相同的词在不同语境下，可以表达不同的语义，例如苹果既表示水果，也表示一家科技公司。同理，相同的语义也可由不同的词表达，例如的士、taxi都表示出租车。
* 语言的组合结构问题。相同的词组成的短语或句子，不同的语序可表达不同的语义，例如深度学习和学习深度。更进一步，还存在句法结构问题，例如从北京到上海高铁和从上海到北京高铁虽然含有的词语完全相同，但其语义完全不同。而北京队打败了广东队和广东队被北京队打败了又语义完全相同。
* 匹配的非对称问题。**文本匹配类的任务，并不单单是文本相似度问题**。一方面**不一定要求语言上的相似**，例如网页搜索任务中query端的语言表述形式和网页端往往具有很大的差别，至少在长度上就差距很大。另一方面也**不一定要求语义上的相同**，例如问答任务中，待匹配的两段文本并不要求同义，而是看候选答案是否真正回答了问题。

这表明，对文本匹配任务，**不能只停留在字面匹配层面，更需要语义层面的匹配，不仅是相似度匹配，还包括更广泛意义上的匹配**。

### 1.1 向量空间模型（VSM）

向量化

可以利用cosine相似性，求查询向量和文档向量的夹角，越小越相似。

提取词，文本向量中词对应的值可以是**0/1值**，0代表词在文本中出现过，1代表词在文本中未出现过；可以是**TF值**（词频）；可以是**DF值**（文档频率，DF越高表示单词越普遍，因此其区分度越低，其权重也越低）；可以是**TF-IDF值**，可以是**N-Gram**，可以是**Embedding**（词级别Embedding、文档级别Embedding）。

基于语意的特征提取方法：基于语境框架的文本特征提取方法、基于本体论的文本提取方法、基于知网的概念特征提取方法等。

[一种基于N一Gram改进的文本特征提取算法](https://wenku.baidu.com/view/d4976f0bf78a6529647d5369.html)

[基于N-Gram文本特征提取的改进算法](http://xueshu.baidu.com/s?wd=paperuri%3A%28f2da6e23f512901233f8c0603df9fe5d%29&filter=sc_long_sign&tn=SE_xueshusource_2kduw22v&sc_vurl=http%3A%2F%2Fkns.cnki.net%2FKCMS%2Fdetail%2Fdetail.aspx%3Ffilename%3DXDJS201234000%26dbname%3DCJFD%26dbcode%3DCJFQ&ie=utf-8&sc_us=5068616763849130906)

影响词权值的因素：词频、词性、文档频次、标题（例如可以初步设定标题的位置权重为5，摘要和结论部分为3，正文为1）、位置、句法结构、专业词库、信息熵、文档/词语长度、词语间关联、单词区分能力、词语直径（比较粗糙的度量特征）、首次出现位置、词语分布偏差（例如词汇在文章出现的平均位置）。

TF（不宜根据词频大幅度删词）、DF（根据预先设置阈值去除文档频次特别低或者特别高的特征，他们分别代表了没有代表性和没有区分度两种极端的情况）、TF-IDF、信息增益、互信息、期望交叉熵、二次信息熵（QEMI）、卡方统计量可以用做词降维。

互信息：

互信息(Mutual Information)是信息论里一种有用的信息度量，它可以看成是**一个随机变量中包含的关于另一个随机变量的信息量**，或者说是一个随机变量由于已知另一个随机变量而减少的不肯定性。

互信息理论进行特征抽取是基于如下假设：在某个特定类别出现频率高,但在其他类别出现频率比较低的词条与该类的互信息比较大。通常用互信息作为特征词和类别之问的测度，**如果特征词属于该类的话，它们的互信息量最大**。由于该方法不需要对特征词和类别之问关系的性质作任何假设，因此非常适合于文本分类的特征和类别的配准工作。

期望交叉熵：

交叉嫡也称KL距离。它**反映了文本主题类的概率分布和在出现了某特定词汇的条件下文本主题类的概率分布之间的距离**，词汇w的交叉嫡越大，对文本主题类分布的影响也越大。

它与KL距离不同之处在于加上了一个词的概率分布，原因是减少很多低频词出现在最终的特征集中。

它与信息增益唯一的不同之处在于没有考虑单词未发生的情况，只计算出现在文本中的特征项。

信息增益：

衡量标准是看特征能够为分类系统带来多少信息，带来的信息越多，该特征越重要。信息增益最大的问题在于它**只能考察特征对整个系统的贡献**，而**不能具体到某个类别上**，这就使得它**只适合用来做所谓“全局”的特征选择**（指所有的类都使用相同的特征集合），而**无法做“本地”的特征选择**（每个类别有自己的特征集合，因为有的词，对这个类别很有区分度，对另一个类别则无足轻重）。

信息增益方法的不足之处在于它考虑了特征未发生的情况。特别是在类分布和特征值分布高度不平衡的情况下，绝大多数类都是负类，绝大多数特征都不出现。此时的函数值由不出现的特征决定，因此，信息增益的效果就会大大降低。信息增益表现出的分类性能偏低。因为信息增益考虑了文本特征未发生的情况，虽然特征不出现的情况有可能对文本类别具有贡献，但这种贡献往往小于考虑这种情况时对特征分值带来的干扰。

卡方统计量：

计算某个特征全局的Chi值，选择Chi值较大的特征。

### 1.2 BM25

计算查询和文档之间的相关性。对查询的分词进行语素分析，每个词看成语素Qi；D表示一个结果文档，Wi表示语素Qi的权重。计算语素Qi和与文档D的相关性得分。Wi的计算：较常用的是IDF。

要点在于语素分析、语素权重判定、语素与文档的相关性判定。

综合来看，BM25考虑了4个因素：IDF因子、文档长度因子、文档词频因子和查询词频因子。

## 2. 语义层面

语义如何表示？语义如何计算？

文本匹配在语义层面的技术演进：

矩阵分解 -> 主题模型 -> 深度学习

### 2.1 矩阵分解

潜在语义分析技术（Latent Semantic Analysis，LSA），开辟了一个新思路，将**词句映射到等长的低维连续空间**，可在此隐式的潜在语义空间上进行相似度计算。

### 2.2 主题模型

之后**PLSA**（Probabilistic Latent Semantic Analysis）、**LDA**（Latent Dirichlet Allocation）等更高级的概率模型被设计出来，逐渐形成非常火热的主题模型技术方向。

这些技术对文本的语义**表示形式简洁、运算方便**，较好的弥补了传统词汇匹配方法的不足。不过从效果上来看，这些技术都**无法替代**字面匹配技术，只能**作为字面匹配的有效补充**。

### 2.3 基于语义相似度的文本相似度算法

语义相关度计算获得相似度矩阵的方向有两个：基于知网HowNet或者基于WordNet。

[文本相似度算法](https://www.cnblogs.com/liangxiaxu/archive/2012/05/05/2484972.html)

### 2.4 Word Embedding

深度学习技术兴起后，基于神经网络训练出的**Word Embedding**来进行文本匹配计算引起了广泛的兴趣。Word Embedding的训练方式更加简洁，而且所得的词语向量表示的语义可计算性进一步加强。但是，只利用无标注数据训练得到的Word Embedding在匹配度计算的**实用效果上和主题模型技术相差不大**，它们**本质**上都是**基于共现信息的训练**。另外，Word Embedding本身**没有解决短语、句子的语义表示问题**，也**没有解决匹配的非对称性问题**。

### 2.5 SimNet

百度2013年设计研发。

SimNet 在语义表示上沿袭了隐式连续向量表示的方式，但对语义匹配问题在深度学习框架下进行了End-to-End的建模，将**词语的Embedding表示**与**句篇的语义表示**、**语义的向量表示**与**匹配度计算**、**文本对的匹配度计算**与**pair-wise的有监督学习**全部统一在一个整体框架内。

### 2.6 其他模型

学术界相关的研究也逐渐增多。像Microsoft Research提出的**DSSM**模型（Deep Structured Semantic Model）即和**SimNet**初版在模型框架上非常类似，只是在训练手段上有所区别。华为NOAH'S ARK LAB也提出了一些新的**神经网络匹配模型变体**，如**基于二维交互匹配的卷积匹配模型**。中科院等研究机构也提出了诸如**多视角循环神经网络匹配模型**（MV-LSTM）、**基于矩阵匹配的的层次化匹配模型**MatchPyramid等更加精致的神经网络文本匹配模型。

## 3. 本仓库相关

本仓库主要关注语意层面。

模型分类标准：

* 基于表示的模型(representation-based)。
  首先对两段文本分别利用编码器编码生成文本的表示，再利用相似度函数或者相关结构计算匹配度。基础模型为Siamese系列的模型，在基础模型上进阶：
  * 改进编码器，得到更好的文本表示。例如：InferSent、SSE模型。
  * 改进相似度计算的方法。例如：SiamCNN、SiamLSTM、Multi-view。
* 基于交互的模型(interaction-based)。
  直接计算匹配特征，第一是关键字的匹配，第二是相对位置，同时考虑匹配度和匹配的结构。
  简单来说首先通过attention为代表的结构来对两段文本进行不同粒度的交互（词级、短语级等），然后将各个粒度的匹配结果通过一种结构来聚合起来，作为一个超级特征向量进而得到最终的匹配关系。
* 基于表示和基于交互的模型的融合。

具体模型：

* **基于表示的模型(representation-based)** : 
  * 基本模型：
    * SiameseCNN
    * SiameseLSTM
    * Learning Text Similarity with Siamese Recurrent Networks, 2016 (**done**)
    * Siamese Recurrent Architectures for Learning Sentence Similarity
    * LSF-SCN
    * LSTM-RNN(Deep Sentence Embedding Using the Long Short Term MemoryNetwork:Analysis and Application toInformation Retrieval, TASLP 2016)
  * 改进编码器：
    * InferSent(Supervised Learning of Universal Sentence Representations from Natural Language Inference Data, EMNLP 2017)
    * SSE(Shortcut-Stacked Sentence Encoders for Multi-Domain Inference, ENLP 2017, 改进编码器)
  * 改进相似度计算的方法：
    * SiamCNN(Applying deep learning to answer selection: A study and an open task, ASRU 2015)
    * SiamLSTM(Siamese Recurrent Architectures for Learning Sentence Similarity, AAAI 2016)
    * MV-DSSM/Multi-view(Multi-view Response Selection for Human-Computer Conversation, EMNLP 2016)
  * 改进编码器 + 改进相似度计算结合：
    * MP-CNN(Multi-Perspective Sentence Similarity Modeling with Convolutional Neural Networks, EMNLP 2015) https://blog.csdn.net/liuchonge/article/details/62424805 https://blog.csdn.net/liuchonge/article/details/64440110 https://blog.csdn.net/liuchonge/article/details/64128870 (**done**)
  * DSSM系列：
    * DSSM(Deep Structured Semantic Models)(Learning deep structured semantic models for web search using clickthrough data, 2013)
    * CNN-DSSM(CLSM)(convolutional latent semantic model)(A latent semantic model with convolutional-pooling structure for information retrieval, 2014)
    * LSTM-DSSM(Semantic modelling with long-short-term memory for information retrieval, 2014)
    * MV-DNN(A Multi-View Deep Learning Approach for Cross Domain User Modeling in Recommendation Systems)
* **基于交互的模型(interaction-based)** :
  * DeepMatch(A Deep Architecture for Matching Short Texts, NIPS 2013) 
  * (Convolutional neural network architectures for matching natural language sentences, NIPS 2014)
    * ARC-I(representation-based)
    * **ARC-II(interaction-based)**
  * MatchPyramid(Text Matching as Image Recognition, AAAI 2016)
  * Match-SRNN(Modeling the Recursive Matching Structure with Spatial RNN, IJCAI 2016)
  * DRMM系列（相关性匹配）
    * DRMM(A deep relevance matching model for ad-hoc retrieval, CIKM 2016)
    * Enhanced DRMM(Deep Relevance Ranking Using Enhanced Document-Query Interactions, EMNLP 2018)
  * DecAtt(A Decomposable Attention Model for Natural Language Inference, EMNLP 2016)
  * KNRM(End-to-end neural ad-hoc ranking with kernel pooling, SIGIR 2017)
  * CompAgg(A COMPARE-AGGREGATE MODEL FOR MATCHING TEXT SEQUENCES, ICLR 2017)
  * **ESIM**(Enhanced LSTM for Natural Language Inference, ACL 2017)
  * Conv-KNRM(Convolutional Neural Networks for Soft-Matching N-Grams in Ad-hoc Search, WSDM 2018)
  * BiMPM(Bilateral multi-perspective matching for natural language sentences, IJCAI)
  * DAM(Multi-Turn Response Selection for Chatbots with Deep Attention Matching Network, ACL 2018)
  * Neural Network Models for Paraphrase Identification, Semantic Textual Similarity, Natural Language Inference, and Question Answering, COLING 2018, 综述文
  * HCAN(Bridging the Gap between Relevance Matching and Semantic Matching for Short Text Similarity Modeling, EMNLP 2019)
  * MV-LSTM
  * K-NRM
  * MM-DNN
* 其他未总结：
  * **ABCNN**, 2016 IBM
  * **Tree-LSTM**, 2015
  * **MIX**(MIX: Multi-Channel Information Crossing for Text Matching, KDD 2018, 腾讯)
  * CNM: An Interpretable Complex-valued Network for Matching, NAACL 2019, 最佳可解释NLP论文
  * Multi-Turn Response Selection for Chatbots with Deep Attention Matching Network ACL 2018 检索式多轮对话系统 https://blog.csdn.net/zhucuankuan2669/article/details/83002423
  * A Compare-Aggregate Model for Matching Text Sequences
  * Improved Representation Learning for Question Answer Matching
  * Learning to Rank Short Text Pairs with Convolutional Deep Neural Networks
  * Improved Representation Learning for Question Answer Matching
  * A Compare-Aggregate Model for Matching Text Sequences, ICLR 2017 

## 参考

[Text-Similarity Method Implemented by Pytorch ESIM/SiaGRU/ABCNN/BiMPM github](https://github.com/pengshuang/Text-Similarity)

[基于MP-CNN的中文句子相似度计算 github](https://github.com/ATEC2018/mpcnn-text-similarity)

[基于siamese-lstm的中文句子相似度计算 github](https://github.com/ATEC2018/deep-siamese-text-similarity)

[基于神经网络模型的释义识别、语义文本相似性、自然语言推理和问题回答](https://blog.csdn.net/u010859324/article/details/80746491)

[LSF-SCNN：一种基于 CNN 的短文本表达模型及相似度计算的全新优化模型](https://www.cnblogs.com/qcloud1001/p/7910255.html)

[论文笔记：BiMPM 阅读和实现 (PyTorch)](https://zhuanlan.zhihu.com/p/50184415)

[文本匹配相关方向打卡点总结](https://www.jiqizhixin.com/articles/2019-10-18-14)

[KDD2018，短文本匹配：MIX](https://www.jianshu.com/p/9c2e35f9ff53)

[搜索之BM25和BM25F模型（没看）](https://www.cnblogs.com/liguangsunls/p/6722444.html)

[维基百科语料中的词语相似度探索](http://www.52nlp.cn/%E7%BB%B4%E5%9F%BA%E7%99%BE%E7%A7%91%E8%AF%AD%E6%96%99%E4%B8%AD%E7%9A%84%E8%AF%8D%E8%AF%AD%E7%9B%B8%E4%BC%BC%E5%BA%A6%E6%8E%A2%E7%B4%A2)

[（simhash讲的不错）（比较全）NLP点滴——文本相似度](http://www.cnblogs.com/xlturing/p/6136690.html#simhash)

[深度学习在文本领域的应用](https://tech.meituan.com/deep_learning_doc.html)

[深度语义模型以及在淘宝搜索中的应用](https://yq.aliyun.com/articles/422338)

[（介绍SimNet）百度NLP 神经网络语义匹配技术机器之心](https://baijiahao.baidu.com/s?id=1570247207721372)

[如何匹配两段文本的语义？](https://www.jiqizhixin.com/articles/2018-06-11-17)

[原创:史上对BM25模型最全面最深刻的解读以及lucene排序深入讲解](http://www.mamicode.com/info-detail-1701866.html)

[经典检索算法：BM25原理](https://www.jianshu.com/p/53e379483f3e)

[搜索中的权重度量利器: TF-IDF和BM25](https://my.oschina.net/stanleysun/blog/1617727)

[搜索引擎的文档相关性计算和检索模型（BM25/TF-IDF）（很好的一篇文章，介绍了很多特征提取方法）](https://blog.csdn.net/heiyeshuwu/article/details/43429447)

[特征选择之卡方检验](https://www.jianshu.com/p/b670b2a23187)

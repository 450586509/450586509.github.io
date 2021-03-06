---
title: learning semantic hierarchies via word embedding
date: 2019-12-10 21:04:34
categories: nlp
tags: [nlp, word-embedding]
---

### 问题描述
本文基于词向量技术，提出一种新的概念语义层（Semantic hierarchy）构建方法。概念语义层是一种树状结构，节点表示概念，边表示相连的两个概念存在**上下位关系**（is-a）的关系，下图是一个例子。概念间的上下位关系在知识图谱Schema构建时会用到。本文提出的方法F-score有73.74%，取得最佳的效果，将本方法和之前人工设计的一种方法结合，可以进一步将F-score提升到80.29%。
![](1.jpg)
<!-- more -->
### Motivation 
本文的灵感来源非常有意思，我们以前都见过这个例子：$v(king)-v(queue) \approx v(man) - v(woman)$，作者发现上下位词对也存在这种关系：
![](2.jpg)
假设$(x,y)$存在上下位关系，且x是y的下位词，那么y是x的上位词，例如：x=对虾，y=虾。如果把所有上下位词对中的上位词减去下位词，得到offset向量，再将offset向量做聚类，会得到如下效果：
![](3.jpg)
由上图可见offset向量存在明显的聚类关系。
作者最初希望找到一个矩阵$W$，使得$Wx=y$，既把x的词向量经过变换，变成y。这种技巧在很多地方都有用到，例如trans系列。估计发现只用一个W估计效果不佳，所以为每个聚类训练一个映射矩阵$W$，每一个聚类中上下位词对（x,y）用各自的映射矩阵，同一个聚类中的词对用同一个映射矩阵，该矩阵可以把下位词向量映射为上位词词向量。

### loss函数
本文的词向量可以使用已经训练好的词向量，需要训练的对象是映射矩阵$W$，损失为：
![](4.jpg)
$N_k$代表第k个聚类中上下位词对的数目，$C_k$代表第k个聚类中的上下位词对的集合，$\Phi$代表映射矩阵。

### 模型的作用
用于判断两个词是否存在上下位关系。如果存在某个映射矩阵$\Phi_{k}$使得(x,y)满足：
$$|\Phi_{k}x - y|<\sigma$$
$\sigma$是一个阈值。则表示(x,y)满足上下位关系。给定一个词典，如果知道任意两个词的是否存在上下位关系，那么可以构造一棵概念语义层次树。


### 创新点：
本文是刘知远教授推荐的一篇文章，他表示自己非常喜欢文中模型的创意，他评价说该文，思路简单，方法高效，论文图示赏心悦目。这应该是首个充分利用word2vec的king-queen=man-woman特性的研究工作。的确，本文从king-queen=man-woman这个现象触发，发现上下位词对也存在类似的性质，并通过可视化的方法，发现offset向量存在明显的聚类关系。所以，有时多角度看问题，能有意想不到的收获，比如文中通过可视化角度展示offset向量发现聚类关系。再利用一个映射矩阵，把下位词向量映射到上位词，方法简单直观。

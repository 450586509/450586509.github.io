---
title: Bi-LSTM CRF 模型
date: 2016-11-27 14:32:00
categories: deep learning
tags: [CRF,命名实体识别,mi-lstm]
---
### 主题
最近看了一篇关于[命名实体论文](https://arxiv.org/abs/1603.01360)，后来有人把这篇论文中的Bi-LSTM + CRF模型用来做分词，并且据说取得非常好的效果，但是今天又有人说效果没有说的那么好。这个模型成功引起了我的注意，下面是阅读的一些收获吧。
### Bi-LSTM+CRF模型
Bi-LSTM模型在深度学习中很常用，网上有很多介绍。我倒是第一次见到它与CRF结合的情形。模型如下图
<center>![Bi-LSTM+CRF][1]</center>

### CRF层在做什么？
这里有篇非常好的[CRF文章](http://blog.echen.me/2012/01/03/introduction-to-conditional-random-fields/)。本文模型中用的是linear-chain CRF

**linear-chain CRF的优点**：
> 假设CRF进行序列标记，输入序列为$(x\_1,x\_2,...,x\_n)$,CRF标记后的序列为$(y\_1,y\_2,...,y\_n)$,CRF不仅能够捕获$x\_i$对$y\_i$的影响，也能捕获$y\_{i-1}$对$y$的影响。
chain-CRF假设如下，这个公式在论文中被用作序列打分函数：
$P(y|x,\lambda) \propto \exp(\sum\_j(\lambda\_jt\_j(y\_{i-1}，y\_i,x,i))+\sum\_k(u\_ks\_k(y\_i,x,i)))$  
$\tag{1}$

论文中提到两个矩阵，分别为P和A。
**p的含义**：
>P的形状是$n\*k$,k是标签的数目，命名实体识别本质上是一个打标签的过程，$P_{i,j}$对应输入句子中第 $i$ 个词对第 $j$ 个标签的打分，（要点来了）**P矩阵就是Bi-LSTM的输出矩阵**。

**A的含义**：
>$A\_{i,j}$代表标签i到标签j到转移概率，这在HMM模型中叫做**状态转移矩阵**。

**打分函数S:**
>文中给出了一个打分函数，也就对于每一个预测序列$y$都有一个对应的分数。
$score(X,y) = \sum^n\_0A\_{y\_i,y\_{i+1}}+\sum^n\_1P\_{i,y\_i}$
**这个公式正好与(1)一样**。

**转为概率**
将打分函数的结果通过一个softmax操作，转为该序列是目标序列的概率：
<center>![image_1b2idmh4p11siie95go8kt1ks913.png-5.5kB][2]</center>

**参数训练**
在训练过程中，可以通过cross-entropy损失函数，最大化正确序列的对数概率
<center>![image_1b2idqtau13842hp8aa134s71g1g.png-12.7kB][3]</center>

### 总结一下CRF做了什么
CRF层把Bi-LSTM的输出当做一个词对每个标签打分，再利用打分函数对每一个预测序列进行打分，分数越高则对应预测的概率越大，再通过一个softmax过程将分数转为概率。


  [1]: http://static.zybuluo.com/BruceXie/te1pj4k0ntal3c7bv8oq27ec/image_1b2ibed3418711u6gf0gsqk15kum.png
  [2]: http://static.zybuluo.com/BruceXie/6udqkwwo398y2v3yrx8e8u8t/image_1b2idmh4p11siie95go8kt1ks913.png
  [3]: http://static.zybuluo.com/BruceXie/yw6tu8zm648t8241k17vab1i/image_1b2idqtau13842hp8aa134s71g1g.png
  [4]: http://static.zybuluo.com/BruceXie/46anx2zbxmoiyhqy9344l5df/image_1b2i9eh4n1kia1rujfniufnrj9.png
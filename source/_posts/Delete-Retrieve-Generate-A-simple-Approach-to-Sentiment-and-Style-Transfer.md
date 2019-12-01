---
title: 'Delete, Retrieve, Generate: A simple Approach to Sentiment and Style Transfer -- percy liang'
date: 2019-11-30 15:32:41
categories: nlp
tags: [nlp,text_generation]
---

### 问题描述
本文旨在解决句子风格变换的问题。句子风格种类已知，给定一个句子和目标风格，生成另一个符合目标风格的新句子。
新句子和原句子描述的类容一样，差别在于描述风格的用词不一样。下面的例子把一个积极的评论，转换为消极的评论，风格发生变化，但是描述的对象和原句子一致。
>原句子： great food but horrible staff and very rude workers！
>
>新句子：great food , awesome staff, very personable and efficient atmosphere。
<!-- more -->

### 解决方案
##### 本文解决方案
文本的属性通常由文本片段体现（distinctive phrase），例如体现风格的词“too small， rude，bad”。所以，本文期望找到原句描述文本风格的片段，并替换成
描述目标风格的片段，从而再保证描述内容一致的情况下，实现句子的风格转换。本文提出了四种无监督算法，这四种算法的思路大致一样，其中DelteAndRetrieve取得最佳的效果，其它三种算法用作对比和Baseline，DeleteAndRetrieve主要包括以下几步：
- 输入数据：$句子(S)，当前风格v^{s}，目标风格v^{t}$
- 从S化中抽取出描述风格$v^{s}$的片段Ps，句子剩下的内容为c(s)，那么句子可以表示为：$S=Ps \cup C(s)$。
- 从目标风格$v^{t}$的句子中，找一个与S相似的句子M。
- 抽取句子M中表示风格的片段Pm。
- 利用一个生成模型，以C(s)和Pm为输入，生成新的目标句子。

四种方案的流程图如下：
![](process.jpg)
RetrieveOnly: 使用相似性检索算法，从目标属性的集合中，检索出与原句子最相似的句子，作为目标句子。
TemplateBased: 利用被检索出句子的属性描述片段，代替原句子中的属性描述片段。
DeleteOnly: 不使用减速操作，使用删除属性片段的句子C(s)和目标属性，利用RNN生成模型生成目标句子。
DeleteAndRetrieve：利用C(s)和被检索出句子中的属性片段，利用RNN生成模型生成目标句子。

##### 方案细节
###### Delete
文中定义了s(u,v)值，用来表示片段u与属性v的相关性，s(u,v)的值与u和v的相关性成正比。计算公式为$$s(u,v)=\frac{count(u,D_v) + \lambda}{\sum_{v^{'}\in V,v^{'}\neq v}count(u,D_{v^{'}})+\lambda}$$
$D_v$表示包含属性v的计算$s(u,v)$的过程是一个统计的过程，先统计片段u在属性为v中的句子集合中出现的次数，再统计片段u在其它属性的句子集合中出现次数，$\lambda$是一个超参数，通过上述步骤可以求任何片段的s(u,v)值。
###### Retrieve
利用去掉属性片段的原句，即C(s,v)，去目标属性的句子集合中搜索，在计算相似性时，都利用去掉属性的句子计算。文中使用的相似性算法有基于tf-idf计算的词重叠算法（求杰卡德系数）和tf-idf加权的词向量句子表示的欧式距离。

###### Generate
以下分别描述四个模型的生成过程：

**RetrieveOnly**:直接使用检索的最相似结果当作生成的文本。
**TemplateBased**:使用检索出的句子对应的属性片段，代替原文本的属性片段。
**DeleteOnly**:利用RNN把C(s)转换成向量，即取最后的隐含状态$s_t$；再把目标属性也变成向量$a_{target}$，目标属性相当于一个word，可以通过Embedding层转换为向量；再把两个向量首位拼接，输入到另一个RNN中，最后得到生成的句子。这里说明可以通过使用属性向量，来控制生成什么属性的句子。
**DeleteAndRetrieve**: 与DeleteOnly的区别在于，这种方法不适用目标属性，而是利用目标属性的描述片段。首先，利用RNN将C(s)转换成向量；再用另一个RNN把目标属性的描述片段，转换成向量；接着把两个向量拼接起来，用来生成句子。从DeleteOnly和DeleteAndRetrieve两种方法来看，可以利用向量拼接的方法，融合不同的输入信息.
sdfada
### 实现细节
##### 生成模型的训练
在DeleteOnly和DeleteAndRetrieve两个模型中，都用到Encoder-Decoder架构的模型，那么在两个方案中，是如何训练的？

**DeleteOnly**: 为了训练Encoder-Decoder模型，需要平行语聊对。本方案使用了类似AutoEncoder的方法训练模型。可以用到数据格式是，句子和风格属性对。在训练DeleteOnly的模型时，输入是C(s)和句子S的属性$a_{s}$，输出原句S，即重构原句S。
**DeleteAndRetrieve**：与DeleteOnly类似，训练DeleteAndRetrieve模型时的输入是C(s)和句子S的属性描述片段（记为P），输出是原句S，也是重构S。所以都是用到AutoEncoder的思想。但是本模型会对S的属性片段以0.1的概率加入一些噪声，如果在存在相同属性的属性描述片段Q,且P与Q的编辑距离为1,则替换，否则继续使用P。目的是使模型具有更好的鲁棒性。

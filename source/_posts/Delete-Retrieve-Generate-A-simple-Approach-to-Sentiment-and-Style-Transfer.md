---

title: 'Delete, Retrieve, Generate: A simple Approach to Sentiment and Style Transfer -- percy liang'

date: 2019-11-30 15:32:41

categories: nlp

tags: [nlp,text_generation]

---



### 问题描述

本文旨在解决句子风格变换的问题。句子风格种类已知，给定一个句子和目标风格，生成一个符合目标风格的新句子。新句子和原句子描述的类容一样，差别在于描述风格的用词。下面是一个将评论语句由积极转为消极的例子，新句子和元句子的风格发生了变化，但是所评价的对象和原句一致，两句都是在评论food、staff和worker。

>原句子： great food but horrible staff and very rude workers！
>新句子：great food , awesome staff, very personable and efficient atmosphere。

<!-- more -->



### 解决方案

##### 动机

作者发现，句子的风格通常由表示风格的片段体现（distinctive phrase），例如“too small， rude，bad”。所以，本文期望从原句中找到描述句子风格的片段，并替换成描述目标风格的片段，便可以在保证描述对象不变的情况下，实现句子的风格转换。本文提出了四种无监督算法，这四种算法的思路大致一样，其中DelteAndRetrieve取得最佳的效果，其它三种算法用作对比和Baseline。DeleteAndRetrieve主要包括以下几步：

- 符号含义：输入句子$s$，当前风格$v^{s}$，目标风格$v^{t}$，$s$去掉风格描述片段后剩下的内容$c(s)$，风格描述片段$p$。

- 首先，从$s$中抽取出描述的片段$p$，并可以得到$c(s)$。

- 从风格为$v^{t}$的句子集合中，找一个与$s$相似的句子$s_r$。

- 从句子$s_r$中抽取出表示风格的片段$p_r$。

- 利用已经训练好的生成模型，以$c(s)$和$p_r$为输入，便可生成风格为$v^{t}$新句子。



四种方案的流程图如下：

![](process.jpg)

简单介绍其它三种方案：
RetrieveOnly: 使用相似性检索算法，从目标属性的集合中，检索出与原句子最相似的句子，作为目标句子。这种方案可以保证语法准确和语义完整，并且得到目标风格的句子，但是风格片段的描述对象可能发生变化。

TemplateBased: 利用被检索出句子的风格描述片段，代替原句子中的风格描述片段，得到目标句子。这种方案得到的结果，能够保证得到目标风格的句子，并且风格片段的描述对象保存一致，但是由于采用直接替换的方式，可能导致语法错误的问题。

DeleteOnly: 不使用检索操作，直接将$c(s)$和目标风格$v^{t}$输入RNN生成模型中，得到新的句子。


##### 方案细节

###### Delete

为了在句子$s$中找到与风格相关的片段，论文中定义了$S(u,v)$值，该值用来表示片句子段$u$与风格$v$的相关性，$S(u,v)$的值与$u$和$v$的相关性成正比。计算公式为$$S(u,v)=\frac{count(u,D_v) + \lambda}{\sum_{v^{'}\in V,v^{'}\neq v}count(u,D_{v^{'}})+\lambda}$$

$D_v$是具有风格$v$的句子集，计算$S(u,v)$是一个统计的过程，先统计片段$u$在$D_v$中的出现次数，再统计片段$u$在其它属性的句子集合中出现次数，$\lambda$是一个超参数。

###### Retrieve

利用$c(s)$,即去掉风格片段后的句子，在目标风格的句子集合中搜索与$c(s)$最相似的句子。在计算相似性时，都利用去掉风格的句子计算。论文中使用的相似性算法有基于tf-idf的词重叠算法（求杰卡德系数）和基于tf-idf加权的词向量句子表示的欧式距离。



###### Generate

以下分别描述四个模型的生成过程：



**RetrieveOnly**:直接使用检索出的最相似句子当作生成的句子。

**TemplateBased**:利用被检索出句子的风格描述片段，代替原句子中的风格描述片段，得到目标句子。

**DeleteOnly**:利用RNN把$c(s)$转换成向量，取最后的隐含状态$s_t$；再把目标风格$a_{target}$embedding成向量，目标风格相当于一个word；再把两个向量拼接，输入到另一个RNN中，生成目标句子。该方案说明可以通过属性向量，控制生成句子的风格。

**DeleteAndRetrieve**: 与DeleteOnly的区别在于，这种方法不使用目标风格，而使用目标风格的描述片段。首先，利用RNN将$c(s)$转换成向量；再用另一个RNN把目标风格片段，转换成向量；接着将两个向量拼接，输入到另一个RNN,得到新的句子。从DeleteOnly和DeleteAndRetrieve两种方法来看，可以利用向量拼接的方法，融合不同的输入信息.


### 实现细节

##### 生成模型的训练

在DeleteOnly和DeleteAndRetrieve两个模型中，都用到Encoder-Decoder架构的模型，那么两个方案的模型是如何训练的？



**DeleteOnly**: 为了训练Encoder-Decoder模型，需要平行语聊对。本方案使用了类似AutoEncoder的方法训练模型。模型的输入是句子$s$的$c(s)$和风格$a_{s}$，输出原句$s$，即重构原句$s$。

**DeleteAndRetrieve**：与DeleteOnly类似，训练DeleteAndRetrieve模型时的输入是$c(s)$和句子$s$的风格描述片段（记为$p_s$），输出原句$s$，也是重构$s$。所以都是用到AutoEncoder的思想。但是本模型会对$s$的风格描述片段以0.1的概率加入噪声，如果存在相同风格的风格描述片段$q_s$,且$p_s$与$q_s$的编辑距离为1,则替换，否则继续使用$p_s$。目的是使模型具有更好的鲁棒性。


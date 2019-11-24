---
title: pun generation with surprise -- percy liang
date: 2019-11-24 17:47:26
categories: nlp
tags: [nlp,text_generation]
---

### 问题描述
介绍一种无监督方法**生成音同或音近的双关语句子**（pun sentence）的方案。相比于有监督方法，该可以可复训练语料不足的问题，并且更有创新型。经人工评估后表示，该方案成功生成双关语句子的概率有33%，超过基于神经网络的基准方案的3倍概率。

### 解决方案
#### 什么造就一个好的双关语句子？
作者发现，当我们在阅读一个句子的某个词的时候，基于上文大家会这个词的可能性有个估计。对于一个双关词，从较短的上下文来看，大家可能较难想到（发生概率小）。但是，从更长的上下文来看，大家又觉得应该是这样（发生概率大）。基于此发现，作者提出了一个local-global surprisal 原则，双关词在local上下文更让人surprising(意外)，但是在global 上下文中却显得情理之中。
<!-- more -->
#### 双关语例子
文中举了一个例子：Yesterday I accidentally swallowed some food coloring. The doctor says I’m OK, but I feel like I’ve （） a little inside。大家认为括号中填died的可能性更大一些，此处对应的双关词是dyed。从近的上文来看，p(dyed| but I feel like I've a little )很小；但是从更长的山下问来看，p(dyed|Yesterday I ... food coloring)的概率较大。
#### A Local-Global Surprisal Measure。
本文提出了量化local-global surprisal原则的方法：
$S(c)=-log(\frac{p(w^p|c)}{p(w^a|c)})$
$S_{local}=S(x_{p-d:p-1},x_{p+1:p+d})$
$S_{global}=S(x_{1:p-1},x_{p+1:n})$
$S_{ratio} = \begin{cases} -1 & S_{local}<0 or S_{global}<0\\ S_{local}/S_{global} & 其它 \end{cases}$
符号含义:

 1. c: 上下文
 2. $w^p$: 双关词
 3. $w^a$: 替代此
 4. p: $w^p$的位置下标
 
该指标可以用来评估一个句子是否为双关句，一个双关语句子的$S_{ratio}$越大，代表效果越好。

#### 方案
方案的输入数据：

 1. 语料：要来生成双关句
 2. ($w^a,w^p$)：替代词和双关词。

##### local surprisal
利用$w^a$从语料中检索一批候选句子。保留只有一个$w^a$的句子，并$w^a$的位置排序，越靠后越好。再把$w^p$代替$w^a$，这样会得到局部surprisal的效果。
##### global surprisal
第一步用pun word (双关词)替代了相关词（alternative word）,这一步目是找到一个topic word，替换掉句子开头的一个词，起到预测pun word的作用。topic world 利用语言模型基于pun word生成。
##### Type consistent constraint
目的是确保被删除的词和topic word具有相同的类型。基于WordNet path 相似性，判断两个词是否为相同类型。 例如，person和passenger是相同的类型，person和ship不是。**被替换的词是句子中的第一个名词或者代词。**
##### Improve grammaticality
直接用topic word替换被替换的词，容易引起语法错误。本文应用一个seq2seq模型解决该问题。seq2seq是一个denoising autoencoders，把topic word周围的词删去，然后重构该句子。

### 创新亮点

 1. 提出了一种无监督生成双关句子的方案，并取得不错的效果。
 2. Local-Global Surprisal 原则的发现和量化方法。
 3. 提升句子语法性的方法。

### 实验结果
本文设计了两个实验：

 1. 验证local-grobal surprisal principle 假设的有效性。
 2. 比较双关句子生成模型和其它模型的效果。

### 总结展望
- 本文基于local-global surprisal 原则实现了一个双关句子生成模型。
- 验证了基于non-humorous文本训练的语言模型，可以得到一个检测双关句子的模型。
- 尽管我们的方法比其它模型要好，但是离人类生成的双关句子有很大差距。






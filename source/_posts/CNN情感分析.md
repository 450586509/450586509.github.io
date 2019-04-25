---
title: CNN情感分析
date: 2016-12-9 21:33:00
categories: deep learning
tags: [CNN]
---

### 前言
最近一直学习如何利用卷积神经网络（CNN）进行情感分析，一下是我用keras实现[Yoon Kim 论文](http://www.aclweb.org/anthology/D14-1181)的过程，趟过一个大坑，在此记录一下。
<!-- more -->
### 算法介绍
该论文是将CNN引入文本分类的开山之作，论文提出了4种模型，模型简单，效果喜人。CNN的输入时二维或以上的张量（tensor），张量不准确的表述为二维或者以上的矩阵。若利用CNN对文本数据建模，需要将输入文本转为张量的形式，利用Word Embedding 可以将单个句子转为一个矩阵,每一行对应该词的词向量。如下图：
<center>![image_1b3eq53cv7j410j51t1fc3v1ku0m.png-35.4kB][1]</center>
#### random模型
利用随机初始化的词向量构造句子矩阵，词向量的值作为模型的参数。[keras的实现代码](https://github.com/450586509/DLNLP/blob/master/src/notebooks/CNN/cnn_static_word.ipynb)
#### static 模型
利用训练好的词向量构造句子矩阵，词向量不作为模型的参数。论文中使用的是google基于新闻语料训练的词向量[keras的实现代码](https://github.com/450586509/DLNLP/blob/master/src/notebooks/CNN/cnn_static_word.ipynb)。
#### non-static模型
利用训练好的词向量构造句子，词向量作为模型的参数。[keras的实现代码](https://github.com/450586509/DLNLP/blob/master/src/notebooks/CNN/cnn_static_word.ipynb)。
#### multi channel 模型
CNN的输入有两个通道，前面三种模型的输入都是num\_words * embedding\_dim的矩阵,该模型的输入为2 * num\_words * embedding\_dim 的张量。类似于图片RGB三个通道。
第一个通道（第一个矩阵）由训练好的词向量初始化，且这些词向量作为模型参数。第二个通道（第二个矩阵）由训练好的词向量促使化，词向量保持不变，不作为模型的参数。[keras的实现代码](https://github.com/450586509/DLNLP/blob/master/src/notebooks/CNN/cnn_static_word.ipynb)（可能与原论文有出入）。

### 收获
1. 在利用keras的Embedding层做static模型时，我们会构建一个word到index的字典，记为word\_index\_dict,再通过训练好的词向量够着要给index到词向量的字典，记为index\_embedding。再构造一个矩阵weights,使得weights[i] = index_embedding[i]即可。通过Embedding层的trainable设置该层的参数是否参与训练（也就是词向量是否当作参数训练）
2. 在构建Embedding层权重的weights时，肯定会出现某些word没有出现在google的词向量中，这时合理的初始化方法，对收敛和最后的最优值有
决定性的作用。这是做这个实验所遇到最大坑，我试过[-1,1]的均匀分布、正态分布和全0，一直无法达到作者的最优值（48%）。一定要按如下初始化，使得与训练好的词向量具有相似的变化范围,作者在原码中说的是，the unknown vectors have (approximately) same variance as pre-trained ones
    np.random.uniform(-0.25,0.25,300)


  [1]: http://static.zybuluo.com/BruceXie/dcl45ptphztxp4hd9yljdmyn/image_1b3eq53cv7j410j51t1fc3v1ku0m.png
---
title: xgboost
date: 2019-05-02 17:10:53
tags: machine learning
categories: machine learning
---

### xgboost如何划分连续变量？
由于训练集中样本的数量是有限的，所以也可以当作离散。在选择分裂点事，先把连续特征的取值从小到大排列，得到$[v_1,v_2,v_3,...,v_k]$,则分裂点可以选取为$[\frac{v_1+v_2}{2},\frac{v_2+v_3}{2},... ]$。
### xgboost如何处理缺失值？
在训练阶段，把缺失样本分别放入左节点和右边节点，通过结构分数，进行最终选择。
在测试阶段，默认放入右边。
### xgboost为什么能够并行？
在选择要分裂的节点、选择特征和选择要分隔属性值时，都可以进行并行。
### 回归树的工作原理？
CART用作分类时，使用基尼系数选择特征和切分点；当CART用作拟合时，使用均方差选择特征和切分点。

### 如何选择分裂特征和特征值？
遍历每一个叶子节点中的属性和属性分裂点。

### 叶子结点的预测值如何选择？
分裂后的树决定后，利用该公式计算：$w_j=\frac{G_j}{H_j+\lambda}, G_j=\sum_{i\in{I_j}}g_i,H_j=\sum_{i\in{I_j}}h_i$。$I_j$表示落入第j个叶子节点的样本集合，$H_j$也就是指把该叶子节点的$h_i$累加起来。

### xgboost的求每棵树的复杂度公式时，为什么要上该叶子节点在的取值平方？

### xgboost数学推到？
- 数学推到的意义在于，得到分裂后形成树的**打分公式**和叶子节点的**预测分**。通过这个打分公式来判断每次分裂的好坏，类似ID3的熵增益，C4.5的熵增益率，cart的gini系数。
- 首先，定义损失函数：$Obj(\theta) = \sum_{i=1}^nl(y_i,\hat{y})+\sum_{k=1}^K\Omega(f_k)$；
- 再定义复杂度公式：$\Omega(f_k)={\gamma}T+\frac{1}{2}\sum_{j=1}^Kw_j^2$;
- 再把损失函数Obj作泰勒展开，$f_k(x)当做{\Delta}x$,并提取合并一些常量，得到：$Obj(\theta) = \sum_{i=1}^n[g_if_t(x)+\frac{1}{2}h_if_t(x)^2]+{\gamma}T+\frac{1}{2}{\lambda}\sum_j^Tw_j^2$;
- 再用叶子节点的值来表示Obj，得到$Obj(\theta) = \sum_{j=1}^T[G_jw_j+\frac{1}{2}(H_j+\lambda)w_j^2]+{\gamma}T$
- 打分公式：$Obj^{\ast}=-\frac{1}{2}\sum_{j=1}^{T}\frac{G_j^2}{H_j+\lambda}+{\gamma}T$
- 叶子节点预测分公式：$w_j=\frac{G_j}{H_j+\lambda}$


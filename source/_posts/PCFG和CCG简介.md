---
title: PCFG和CCG简介
date: 2019-05-03 18:05:42
tags: nlp
categories: nlp
---

### 1. 什么是形式语法？
&emsp;&emsp;Chomsky把**形式语法**理解为数目**有限的规则的集合**，这些规则可以**生成**语言中合格的句子，并**排除**不合格的句子。Chomsky把形式语法G定义为4元组。$G=(V_n,V_t,S,P)$，含义如下：
&emsp;&emsp;1. $V_n:$ 非终结符集合
&emsp;&emsp;2. $V_t:$ 终结符集合
&emsp;&emsp;3. $S:$ $V_n$的初始符号
&emsp;&emsp;4. $P:$ **重写规则**。通过这些规则生成句子。

### 2. 什么是Chomsky层级？
&emsp;&emsp;Chomsky根据**重写规则**的形式，把形式语法分为4类。含义如下，其中符号的含义是，${\phi}$、$\psi$和$w$都是**符号串**，$A$和$Q$为非终结符，$a$为终结符。
&emsp;&emsp;1. **0型语法**。满足重写规则：${\phi}{\rightarrow }\psi$。
&emsp;&emsp;2. **上下文有关语法**。满足重写规则：${\phi}_1A{\phi}_2{\rightarrow }{\phi}_1w{\phi}_2$。
&emsp;&emsp;3. **上下文无关语法**。满足重写规则：${A}{\rightarrow }w$，这种规则没有上下文限制。
&emsp;&emsp;4. **有限状态语法**。满足重写规则：$A{\rightarrow }aQ$或者$A{\rightarrow }a$。

### 3. 什么是PCFG？
&emsp;&emsp;PCFG全称叫概率上下文无关语法（Probabilistic Context-Free Grammar）。在上下文无关语法的基础上，为每一个重写规则增加了一个条件概率。

### 4. PCFG中重写规则的概率如何获得？
&emsp;&emsp;通过语料库统计得到，或者通过训练语料进行学习。

### 5. 什么是CCG？
&emsp;&emsp;CCG全称叫组合范畴语法（Combinatory Categorical Grammar），对范畴语法进行了扩展。扩展的实质在于组合，在范畴语法的基础上，增加了**函子范畴**的组合运算。
&emsp;&emsp;范畴语法把英语中的词用S和n两个最基本的范畴表示，n代表名词的**句法类型**，S表示句子。S和n代表原子范畴，其它词可以通过3个规则，用n和S表示其在句子中的句法类型。句法类型大致相当于传统语法中的词类。
&emsp;&emsp;CCG的基本思路：
&emsp;&emsp;1. 为每一个词或者短语赋一个**类别**。
&emsp;&emsp;2. 再对类别按照**组合规则**进行组合。


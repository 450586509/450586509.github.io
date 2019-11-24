---
title: Deep Q-Learning
date: 2019-04-29 10:20:08
tags: reinforcement learning
---

Q-learning存在的问题？
> 现实生活中经常遇到模型状态太多，以至于无法构建Q(s,a)表格，也叫Q-table。

如何解决上面的问题？
> 利用一个函数近似代替。例如，找到一个函数f(s,a)，是的Q(s,a) = f(s,a)。

如何构造函数的输入？
> 一种方法是把状态S和动作a拼接起来用作f的输入。另一种方法是，只把s当作f的输入，f的输出是一个向量，向量的每一个维度的值代表对应动作的Q(s,a)值。

如何构建函数f？
> 函数拟合是深度学习的强项，所以可以通过一个网络构建函数f，该网络叫做Q-Network(Q网络)。

Q-Learning算法原理？

    初始化矩阵Q，Q矩阵有S行A列，S为状态的数量，A为动作的数量。
    while Q 未收敛：
        开始一轮训练
        while 状态S != 结束：
            基于策略，获得动作a
            agent采用动作a,获得新的状态S_new,与奖励R(S,a)
            更新Q矩阵，Q[S,a] = (1-k)*Q[S,a] + k*(R(S,a) + t*max(Q[S_new,:]))。Q[S,a]是矩阵第S行第a列的元素，max(Q[S_new,:])表示第S_new行最大的值。
            S = S_new

如何训练Q-network？
> 每次和环境交互得到的奖励和新的状态都保存起来。用作训练数据。
> 损失函数$L(w)=E[(r +{\lambda}*max_{a^{'}}Q(s^{'},a^{'},w)-Q(s,a,w))^{2}]$

如何构建Q-network的训练数据？
>1. agent在状态S时，采取动作a后，得到新的状态S_new、奖励R(S,a)和是否结束的标志。构成一个5元组（S, a, R(s, a), S_new, is_end）
>2. 每一个5元组都可以转换一个训练样本（S, y）。当is_end为True的时候，y = R(s, a); 当is_end为False的时候，y=R(S,a)+k maxQ(S)。Q代表Q网络。


DQN的训练流程：
&emsp;&emsp;算法符号介绍：迭代轮数$T$，状态特征维度$n$, 动作集$A$, 步长$\alpha$，衰减因子$\gamma$, 探索率$\epsilon$, Q为网络结构, 批量梯度下降的样本数m。
&emsp;&emsp;算法输出：训练好的Q网络。
&emsp;&emsp;1. 初始化Q，清空经验回放集合D。
&emsp;&emsp;2. for i from 1 to T, 迭代训练：
&emsp;&emsp;&emsp;&emsp;a. 初始化启始状态S
&emsp;&emsp;&emsp;&emsp;b. 经过Q网络，采用$\epsilon$-贪婪策略，得到输出动作a
&emsp;&emsp;&emsp;&emsp;c. 执行动作a，得到新的状态$S_{new}$、奖励$R(S,a)$和$is\_end$的标志
&emsp;&emsp;&emsp;&emsp;d. 将S、a、R(S,a)、$S_{new}$、$is\_end$合并成5元组（S,a, R(S,a), $S_{new}$, is_end），再加入到经验回放集D。
&emsp;&emsp;&emsp;&emsp;e. 从经验回放集D中，随机挑选m个样本，每个样本对应一条训练数据$(S,y)$.
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;$y = \begin{cases}  
R(S,a) & is\_end = True \\
R(S,a) + maxQ(S) & is\_end = False
\end{cases}$
&emsp;&emsp;&emsp;&emsp;f. 利用m个样本，并基于反向传播，训练Q网络。
&emsp;&emsp;&emsp;&emsp;g. 如果is_end为真，则结束本轮循环。否则，令$S=S\_new$，并再从第b步开始执行。

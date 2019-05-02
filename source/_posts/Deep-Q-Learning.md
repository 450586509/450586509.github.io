---
title: Deep-Q-Learning
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

如何训练Q-network？
> 损失函数$L(w)=E[(r +{\lambda}*max_{a^{'}}Q(s^{'},a^{'},w)-Q(s,a,w))^{2}]$

如何构建Q-network的训练数据？

什么是Policy networ？Policy network如何更新
> 策略网络，由当前状态，推测动作分布。比DQN更加简洁。

什么是Actor Critic?如何结合DQN和Policy Network的？

什么是A3C？
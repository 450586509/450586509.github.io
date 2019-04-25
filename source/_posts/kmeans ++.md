---
title: kmeans++
date: 2016-12-16 17:17:30
tags: machine learning
categories: machine learning
---
[k-means++:the advantages of careful seeding](http://ilpubs.stanford.edu:8090/778/1/2006-13.pdf)
### kmeans++的优点：
相对于kmeans，既能提升准确率也能加快速度。
>By augmenting k-means with a simple, randomized seeding technique, we obtain an algorithm that is **O(log k)-competitive** with the optimal clustering. Experiments show our augmentation **improves both the speed and the accuracy of k-means**, often quite dramatically.

<!-- more -->
### kmeans和kmenas++介绍
$c:$某一个中心点
$D(x):$样本点x到已取中心点的最短距离。
k-means算法很容易理解：
![image_1b43eff851i40j09m87fvdnik1g.png-80.1kB][1]
k-means++算法：
![image_1b43dlo5013b1nnf14g5di34b4m.png-64.2kB][2]

### kmeans与kmeans++的区别
kmeans随机选取k个中心点，kmeans以一种更加合理的策略选择。


  [1]: http://static.zybuluo.com/BruceXie/yi8uo1ij4b348d7v92fv78sf/image_1b43eff851i40j09m87fvdnik1g.png
  [2]: http://static.zybuluo.com/BruceXie/kw2bcrrcuv8h1sp1ylsb7chm/image_1b43dlo5013b1nnf14g5di34b4m.png
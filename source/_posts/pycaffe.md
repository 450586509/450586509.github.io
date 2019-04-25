---
title: pycaffe
date: 2016-07-21 22:21:46
tags:
---

### 利用python加载已经训练好的caffe模型。非常简单。

```
// code
import caffe
protoPath = 'G:/code/caffeDemo/lenet_train_test.prototxt' 
weigths = 'G:/code/caffeDemo/_iter_10000.caffemodel'
net = caffe.Net(protoPath,weigths,caffe.TEST)

#通过net可以查看每层的weights和所有的blobs.
net.param
net.blobs
```


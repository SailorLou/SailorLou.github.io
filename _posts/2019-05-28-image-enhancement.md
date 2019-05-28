---
layout:     post
title:      "数字图像增强知识总结"
subtitle:   "基础知识总结"
date:       2019-05-25 16:00:00
author:     "Sailor"
categories:  image-processing
mathjax: true
tags:  image-processing
---

本文主要对图像增强一般方法的总结.

<!-- more -->

## 图像增强的概念
图像增强是指按照特定的需要突然感兴趣的信息，同事削弱或去除不感兴趣的信息.处理的结果并不能增加图像的信息，而只能增强对某些信息辨识能力，并且这种处理有可能损失一些其他信息。

## 图像增强技术分类
![](https://sailorlou.github.io/image/image_boundary/imageenhancement.png)

## 灰度增强
一般原理：
      $$ s= T(r) $$,
其中r是f(x,y)像素的灰度值，s为g（x，y）像素的灰度值,T是灰度变换函数（或变换算子）;其包含线性变换和非线性变换.
![](https://sailorlou.github.io/image/image_boundary/huidubianhuan.png)

### 直方图均衡化

#### 直方图
x轴表示 灰度级，y轴表示对应每一个灰度级的像素个数或者频数（n/N）.

#### 直方图均衡化
一般好的图像都是图像直方图比较均匀，这样对比度才比较好，一般采用直方图均衡化来提高图像的质量.

变量r 代表图像中像素灰度级。用r的积累分布函数作为灰度变换函数可产生一副灰度级分布具有均匀概率密度的图像.
对灰度级进行归一化处理, 则0≤r≤1, 按照
 $$ S_k = T（r_k）= \sum_{j=0}^k（n_j/N） $$ K=0,1,2,L-1
 变换满足两个条件：
- 对于0≤r≤1,则0≤s≤1,
- 在0≤r≤1,t（r）单调递增。

反变换后 $ T^{-1}(S) $ 同样满足上面两个条件.

##### 直方图均衡化步骤
![](https://sailorlou.github.io/image/image_boundary/zhifangtu.PNG)
##### 直方图均衡化例子

### 直方图规定化
直方图均衡化是一种通用的对比度提升方法，如果我们想把图像变换到制定的直方图就显得力不从心。所以就有了直方图规定化，就是对原始图像做变换，使得变换后的图像的直方图跟我们规定的一样。
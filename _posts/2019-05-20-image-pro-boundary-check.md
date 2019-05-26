---
layout:     post
title:      "图像处理之边缘检测"
subtitle:   "边缘检测"
date:       2019-05-20 16:00:00
author:     "Sailor"
categories:  image-processing
mathjax: false
tags: image-processing    
---

本章主要介绍各种边缘检测的算法，通常边缘常用图像一阶导数和二阶导数来检测.

<!-- more -->

## 梯度算子
### 原理介绍
梯度算子对应于图像的一阶导数，图像的一阶导数一般通过差分运算来近似的.
梯度的方向是图像最大变化率的方向，对图像F(j，k)在点(j，k)处的梯度定义为矢量：
![](https://sailorlou.github.io/image/image_boundary/tidu1.PNG)
对离散的图像，微分可以用差分来近似，如下图（1）式，便于编程和提高运算速度，在一些场合可以用绝对值表示，如下图二式，
![](https://sailorlou.github.io/image/image_boundary/tidu2.PNG)




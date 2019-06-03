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

<!-- TOC -->

- [基于微分算子的边缘检测方法](#基于微分算子的边缘检测方法)
    - [原理介绍](#原理介绍)
    - [Prewitt算子](#prewitt算子)
    - [Roberts 交叉算子](#roberts-交叉算子)
    - [Sobel 算子](#sobel-算子)
    - [Kirsch 算子](#kirsch-算子)
    - [laplace 算子](#laplace-算子)
    - [Marr-Hidreth算子](#marr-hidreth算子)
    - [Canny算子](#canny算子)
    - [Hough变换](#hough变换)

<!-- /TOC -->

## 基于微分算子的边缘检测方法
### 原理介绍
梯度算子对应于图像的一阶导数，图像的一阶导数一般通过差分运算来近似的.
梯度的方向是图像最大变化率的方向，对图像F(j，k)在点(j，k)处的梯度定义为矢量：
![](https://sailorlou.github.io/image/image_boundary/tidu1.PNG)

对离散的图像，微分可以用差分来近似，如下图（1）式，便于编程和提高运算速度，在一些场合可以用绝对值表示，如下图二式，
![](https://sailorlou.github.io/image/image_boundary/tidu2.PNG)

关于梯度的介绍可以参见 另一篇总结[《图像增强》](https://sailorlou.github.io/2019/05/25/image-enhancement/#%E5%9B%BE%E5%83%8F%E7%9A%84%E9%94%90%E5%8C%96)

### Prewitt算子
![](https://sailorlou.github.io/image/image_boundary/prewitt.PNG)
### Roberts 交叉算子
另一篇总结[《图像增强》](https://sailorlou.github.io/2019/05/25/image-enhancement/#%E5%9B%BE%E5%83%8F%E7%9A%84%E9%94%90%E5%8C%96)

### Sobel 算子
另一篇总结[《图像增强》](https://sailorlou.github.io/2019/05/25/image-enhancement/#%E5%9B%BE%E5%83%8F%E7%9A%84%E9%94%90%E5%8C%96)

### Kirsch 算子
### laplace 算子
另一篇总结[《图像增强》](https://sailorlou.github.io/2019/05/25/image-enhancement/#%E5%9B%BE%E5%83%8F%E7%9A%84%E9%94%90%E5%8C%96)
### Marr-Hidreth算子
laplace对噪声比较敏感，该算子的主要思路是首先对图像进行平滑处理然后再进行laplace算子.平滑函数常常选择Gauss函数.
### Canny算子
### Hough变换




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

本文主要对图像增强一般方法的总结，其包括灰度增强，空间域滤波和频率域滤波.

<!-- more -->
<!-- TOC -->

- [图像增强的概念](#图像增强的概念)
- [图像增强技术分类](#图像增强技术分类)
- [灰度增强](#灰度增强)
    - [直方图均衡化](#直方图均衡化)
        - [直方图](#直方图)
        - [直方图均衡化](#直方图均衡化-1)
            - [直方图均衡化步骤](#直方图均衡化步骤)
            - [直方图均衡化例子](#直方图均衡化例子)
    - [直方图规定化](#直方图规定化)
- [图像平滑](#图像平滑)
    - [领域平均法（均值滤波器）](#领域平均法均值滤波器)
    - [中值滤波器](#中值滤波器)
    - [加权平均](#加权平均)
    - [高斯滤波器](#高斯滤波器)
        - [还有很多其他平滑模板的设计](#还有很多其他平滑模板的设计)
- [图像的锐化](#图像的锐化)
    - [图像的一阶微分和二阶微分](#图像的一阶微分和二阶微分)
    - [梯度算子一阶微分](#梯度算子一阶微分)
    - [拉普拉斯算子 - 二阶微分](#拉普拉斯算子---二阶微分)
- [频率域上的图像增强](#频率域上的图像增强)
- [伪彩色处理](#伪彩色处理)
- [假彩色图像处理](#假彩色图像处理)
- [reference](#reference)

<!-- /TOC -->

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
![](https://sailorlou.github.io/image/image_boundary/zhifangtulizi.PNG)
### 直方图规定化
直方图均衡化是一种通用的对比度提升方法，如果我们想把图像变换到制定的直方图就显得力不从心。所以就有了直方图规定化，就是对原始图像做变换，使得变换后的图像的直方图跟我们规定的一样。

## 图像平滑
- 图像平滑的目的消除或尽量减少噪声的影响，改善图像的质量.
噪声是指在图像摄取或传输过程中所收到的随机干扰信号.图像平滑的本质上低通滤波，图像边缘也出高频部分.

- 首先介绍一个概念,平滑模板实现了一个领域运算，即某个像素点的结果灰度不仅和该像素灰度有关，而且和其领域点的值有关
模板运算的数学含义是一种卷积（或互相关）运算.其中大卷积模板可以加大滤波程度但也会导致图像细节的损失.
- 平滑的本质是积分

### 领域平均法（均值滤波器）
![](https://sailorlou.github.io/image/image_boundary/lingyupinghua.PNG)
### 中值滤波器
$$ y[m,n] = median{x[i,j],(i,j)∈w} $$

where $w$ represents a neighborhood defined by the user, centered around location $[m,n]$ in the image

- 中值滤波器是把已某一点为中心的小窗口的所有像素的灰度按照从小到大排列，将中间值作为该点的灰度值，其目的主要在保护边缘的时同时去除噪声.

- 中值滤波器容易出去孤立点，线的噪声同时保持图像的边缘
- 能很好的的去除二值噪声，但对高斯噪声无能为力
- 当窗口内噪声点的个数大于窗口宽度的一半时，中值滤波的效果不好.

### 加权平均
![](https://sailorlou.github.io/image/image_boundary/jiaquanpingjun.PNG)

### 高斯滤波器
通过以上的图可以看出box模板考虑领域的作用，没有考虑个点的位置的影响，对于所有的9个点都一视同仁，平滑效果并不理想.映入加权系数，离某点越近对该点的影响越大，就像高斯图一样，如下图;
其中高斯模板是通过二维高斯采样得到.
![](https://sailorlou.github.io/image/image_boundary/gaosilvbo.PNG)

#### 还有很多其他平滑模板的设计
![](https://sailorlou.github.io/image/image_boundary/qitamoban.PNG)


## 图像的锐化
- 与平滑相反，锐化是通过增强高频分量来减少图像中的模糊
- 增强图像的细节边缘和轮廓，灰度反差增强，便于后期对目标的识别和处理
- 锐化的本质是微分，高通滤波
- 边缘和轮廓都位于灰度突变的地方.

### 图像的一阶微分和二阶微分
![](https://sailorlou.github.io/image/image_boundary/weifen.PNG)

一阶微分的三个特征：
- 在恒定灰度区域的微分为零
- 在灰度台阶或斜坡处微分值为零
- 延着斜坡的微分值非零

二阶微分的三个特征是：
- 在恒定灰度区域的微分为零
- 在灰度台阶或斜坡的起点微分值非零
- 延着斜坡的微分值非零

数字图像中边缘在灰度上常常类似于斜坡过度，这样就导致图像的一阶微分产生较粗的边缘，因为延着斜坡的微分非零，二阶微分产生由零分开的一个像素宽的双边缘,
重要结论：二阶微分在增强细节要比一阶微分好的多.

### 梯度算子一阶微分
图像处理中的一阶微分用梯度幅值来实现的，f在坐标（x,y）处的梯度定为二维列向量：
![](https://sailorlou.github.io/image/image_boundary/tiduzengqiang.png)

其中向量表示在位置(x,y)的最大变化率方向.
幅值表示梯度向量方向变化率在(x,y)处的值，注意M(x,y)是与原图像大小相同，在实战中该图像被称为梯度图像。

对于求出梯度幅值后，确定锐化输出g(x,y)方法有：
- 直接用梯度幅值代替锐化后的图像的灰度值   
   该方法简单，但在图像均匀区域，梯度很少，甚至为0，结果图像较暗.
- 输出门限判断
    - 当梯度值大于门限时，取梯度值为输出图像的灰度值
    - 其他则保留原图像像素
- 为边缘规定一个特定的灰度级
    - 当梯度值大于某一个门限时，设置指定灰度
    - 其他则保留原来图像灰度值
- 为背景规定特定灰度级
    -  当梯度值大于某一个门限时，取梯度值为输出图像的灰度值
    - 其他则为指定灰度
- 二值化图像
    - 当梯度值大于某一个门限时，设置指定灰度
    - 其他则设置指定另一种灰度

假如一个图像的局部为：
![](https://sailorlou.github.io/image/image_boundary/pixgrid.PNG)
由于求梯度分量的方法不同而提出了不同的梯度算子：
- Roberts 算子
![](https://sailorlou.github.io/image/image_boundary/roberts.png)

- Sobel 算子
![](https://sailorlou.github.io/image/image_boundary/sobel.png)

在边缘检测检测中，G大于某一个阈值，则认为该点(x，y)为边缘点.
sobel对噪声具有平滑作用，提供较为精确的边缘方向信息，边缘定位精度不够高。当对精度要求不是很高时，是一种较为常用的边缘检测方法。

### 拉普拉斯算子 - 二阶微分
![](https://sailorlou.github.io/image/image_boundary/lapulasi.png)

特点是：
- 二阶导，对噪声非常敏感
- 拉普拉斯的肤质产生双边缘
- 不能检测边缘的方向-无向模板

## 频率域上的图像增强
频率域上的图像处理一般步骤如下：
![](https://sailorlou.github.io/image/image_boundary/frequencedomain.PNG)
有卷积定理可知，滤波函数的表达式可以表示为
$$ G(u,v)=H(u,v)F(u,v) $$
其中H为转换函数，其包含一下几种类型
- 理想圆形低通滤波器
![](https://sailorlou.github.io/image/image_boundary/idearlowpass.PNG)

- Butterworth lowpass filter
![](https://sailorlou.github.io/image/image_boundary/butterworth.PNG)

- Gaussian Low pass filters
![](https://sailorlou.github.io/image/image_boundary/gaosiditong.PNG)

- 高通滤波器
![](https://sailorlou.github.io/image/image_boundary/gaotonglvboqi.PNG).
其中lfe 代表高通，H（x,y）代表低通.
a等于1，b为-1.D(u,v)表示欧式u和v距离，D0表示被截断的频率门限值（cutoff frequency）.
## 伪彩色处理
把灰度图像处理成伪彩色图像，其宗旨是将灰度图像的不同灰度级按照线性或非线性映射成不同的彩色，以提高图像的内容的可辨识度.
## 假彩色图像处理
把真实的自然彩色图像处理成假彩色图像。
假彩色是通过不同波段进行合成得到的彩色影像，不是肉眼观察得到的实际颜色。


## reference
- http://fourier.eng.hmc.edu/e161/lectures/
- http://faculty.salina.k-state.edu/tim/mVision/freq-domain/freq_filters.html


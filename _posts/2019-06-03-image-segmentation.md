---
layout:     post
title:      "数字图像分割知识总结"
subtitle:   "基础知识总结"
date:       2019-05-25 16:00:00
author:     "Sailor"
categories:  image-processing
mathjax: true
tags:  image-processing
---

本文主要对图像分割一般方法的总结，其包括阈值分割，形态学分割及其区域增长.

<!-- more -->

## 图像分割概述
分割的目的是把图像空间分成一些有意义的区域，是实现图像自动识别与理解的必不可少的过程，是计算机视觉的中间层次.
![](https://sailorlou.github.io/image/image_boundary/seg-overview.PNG)

假如集合R代表整个图像区域，对R分割为n个子区域为r1，r2...rn，则图像分割需要满足以下五个条件：
- 所有子区域的并集是R，也就是说分割是针对每一个像素
- 任何被分割的两个子区域没有交集
- 属于同一个区域的像素应该具有相同的特性，区域边界是明确的.这个需要定义相应的评价标准.
-  属于不同区域中的像素应该具有不同的特性
- 要求被分割的子区域内的像素应该是连通的.   

那么要求分割准则
- 分割准则应适用于所有区域和像素
- 分割准则应能确定各区域像素有代表的特性

分割算法的基于灰度值的两个基本特性：
- 不连续性.不同区域之间没有共性的.
- 相似性.被分割的子区域是相似性的

## 图形分割的基本策略
- 从简到难，逐级分割
- 控制背景环境，降低分割难度
- 把焦点放在增强感兴趣对象，减少不相干部分的干扰上.   

## 阈值分割法
- 二值化的分割方法
![](https://sailorlou.github.io/image/image_boundary/yuzhi.PNG)
- 直方图的双峰性质来确定阈值
- 全局阈值处理
    - 1).为全局阈值T选择一个初始值
    - 2).以阈值T 对图像进行分割产生两组像素，G1 和 G2
    - 3).对两组像素分别计算平均灰度值（均值）：m1 和m2
    - 4).计算一个新的阈值 T = (m1+m2)/2
    - 5).重复步骤2和4直到迭代中的T值间的差小于一个预定义的误差系数为止.

- 最大类间方差阈值(大津阈值分割OTSU)
    - 该方法自动寻找阈值，对图像进行划分，将目标和背景区分开来.
    - 主要原理是 把直方图在某一个阈值分成两组,当被分成的两组间方差为最大时，决定最终阈值
    - 算法步骤如下：
        - 1).假设一幅图像有L个灰度级[1,2,…,L]。灰度级为i的像素点的个数为ni，像素总个数为N
        - 2).用灰度级k将图像分成两组像素 C0 和 C1，C0灰度级为[1,…,k]，C1灰度级为[k+1,…,L].
        - 3).分别计算两组产生的概率和灰度平均值
        ![](https://sailorlou.github.io/image/image_boundary/otsu.PNG)  
         并且全部采样的平均值可以表述为：
           ![](https://sailorlou.github.io/image/image_boundary/otsu1.PNG)  
        - 4).用下式计算两组总的方差
          ![](https://sailorlou.github.io/image/image_boundary/otsu2.PNG) 
        - 5).所以可以求出k就是阈值
        ![](https://sailorlou.github.io/image/image_boundary/otsu3.PNG) 


## Reference
- A threshold selection method from gray-level histogram
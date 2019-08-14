---
layout:     post
title:      "C++常用基础知识汇总"
subtitle:   "基础知识总结"
date:       2018-11-02 16:00:00
author:     "Sailor"
categories:  C++
mathjax: true
tags:  C++
---

##  C++ template
本文主要是对c++中模板知识的总结，主要包括函数模板，类模板和特化.
<!-- more -->

### 函数模板
#### 基础概念
#### 偏特化
#### 全特化
### 类模板
#### 基础概念

```c

#include <iostream>
template<class T>
class TestClass
{
public:
   void Print(T1 left)   {std::cout<<left << std::endl;}
    
}

void mian()
{
    TestClass<int> test;
    test.Print(1);
}

```

#### 偏特化
#### 全特化
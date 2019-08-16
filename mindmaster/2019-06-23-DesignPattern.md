---
layout:     post
title:      "设计模式"
subtitle:   "基础知识总结"
date:       2019-05-25 16:00:00
author:     "Sailor"
categories:  编程哲学
mathjax: true
tags:  编程哲学
---

本文我想探讨的主题应该算是软件编程的的哲学-设计模式和设计准则

<!-- more -->

## 前言
早想写一篇关于设计模式的总结，可一直不敢下笔，因为设计模式和设计准则太过深邃，不知如何动笔，也害怕自己认知有限，误导他人。
后来想明白，这篇总结是自己以往工作和学习的总结，便于自己复读用的，也无关他人，如果有缘人看到此文，发现错误处或不同意见，期待你的批评指教.
也欢迎联系我loufuyi@163.com进行讨论交流，认知是一个过程性的活动，我敢肯定的是这篇文章随着我的开发工作的进行一定会有修改.

## overview
本文我之所以说他是编程哲学，因为这些方法也常常出现在我们生活和其他学科中，计算机南北桥的设计，计算机网络的分层结构等
我本想试图给设计模式一个定义，又不想照本宣科，白话说就是写好程序，这个好又该如何评价呢？我想会有以下几个维度
- 代码复用
- 响应需求变更快
- 移植性
- 可扩展性

可能有人会说好代码还有其他特征比如说：好的代码规范，命名规则，代码执行效率，代码的可读性等等.

## 设计准则
设计准则（SOLID..)应该是内功心法，是设计模式的纲。我的第一任老板有个很好的工作习惯：他应该每做完设计后，都会拿这些设计准则进行校验
自己的设计，我也吸取了他的经验，也分享给有缘人.

### 抽象数据类型(Abstract Data Types ADTs)
很多讲解设计模式的书都没有阐述数据抽象的重要性，可能数据抽象是需求分析的组成部分但不是设计模式的范畴吧，更不算是设计准则，但我想把他放在设计准则中，以突出他的重要性。我个人觉得，数据抽象是把现实的业务或是场景抽象成虚拟世界的数据集合(或者类)，他是设计模式应用的基石，OOAD的基础，没有好的抽象，一切设计模式都显得太过脆弱.如何做抽象我觉得是对业务需求有足够的了解和认知，能够掌握需求的边界条件及其可扩展的点，我觉得在《代码大全》中对于抽象和类的定义的阐述是最好的，以下引用几个好的结论性的总结：
- 良好的类接口：通过接口来展现的合理抽象，并确保细节被影藏在抽象背后.
- 尽可能地限制类和成员的可访问性
- 不要公开暴露成员数据
- 避免把使用的实现细节放在类的接口中-比如接口类中定义了数据成员.
- ...

### 单一原则(SRP)
- 官方解释是：对一个类而言，应该仅有一个引起它变化的原因，
- 我的理解是：设计类或者模块时，职责要清晰（单一），不能太多，否则其他的类任何相关的类发生变化，就会影响到这个类，
类与类之间的关系也就不牢靠。就像人，具有独立人格的朋友很容易建立巩固的关系，你就会理解他什么会做，什么就不会做，也会取得你的信任。
### 开闭准则(OCP)
-  官方解释是：对扩展开放，对修改封闭;
- 换一句说面对需求的变更时，最大化的保持已有代码的稳定性，而添加新的类或代码.比如说模块与模块之间的信息交流可以抽象接口。
在大型系统或者平台中，比如应用模块很多的情况下，上层把模块的注册接口定掉，在具体的新增应用或模块中，按照架构师订立的接口实现，
就能把应用或者模块集成在现有的系统或平台中.

### 里氏替换原则(LSP)
- 官方解释是：用子类对象替换掉所有父类被应用的地方，程序依然可以正常的运行，并没发生任何变化.
- 具体体现：
    - 子类可以实现父类的抽象方法，但是不能覆盖父类的非抽象方法。
    - 子类当然是可以有自己的个性的（方法）
    - 子类重载父类的方法时，子类方法的前置条件(形参)要比父类方法的输入参数更宽松.
      也就是说子类的参数类型范围比父类大，那么子类的方法就不会执行
- 该原则不仅仅指导如何使用继承关系，而且可以用来指导接口与其实现方式的一种设计原则，

### 接口隔离原则(ISP)
- 一个模块或者类通过最小的粒度的接口与另一个模块或类建立依赖关系。
- 任何层次的软件设计如果依赖于不需要的东西，都会有害处的，从源代码的角度说，这样的依赖关系会导致不必要的重新编译和重新部署.
- 接口的设计要保证最小的粒度，不应该出现不需要的方法，或者适当分解大的接口为多个单一的接口，是职责单一化.
### 依赖倒置原则(DIP)
- 官方解释是：高层模块不应该依赖底层模块，两个都应该依赖抽象；抽象不应该依赖细节（具体的实现类），细节应该依赖抽象；
- 针对接口编程，而不是针对实现编程.模块与模块之间的依赖可以通过抽象（或叫接口）来降低耦合.
- 例如，我们在开发图形化界面时，用QT做界面，用VTK来显示三维物体，这时，QT界面就是高层模块，VTK 就是底层模块；那么我们愉快的在QT里
  直接调用VTK里的各种渲染。当有一天说VTK收费了，我们要换成OpenGL，那么界面与vtk之间的耦合又太过高，重构必将是一场灾难。如果我们能够抽象
  一层图形引擎接口，那么QT不直接用VTK，而是用这个抽象层的，那么当我们要opengl换掉vtk时，只需要重新实现这个抽象的图形引擎接口即可，无需动QT的业务逻辑.

  ### 迪米特法则（最少知道原则 LOD）
  - 类的设计中，应对其他类保持最小的了解.这样可以降低类与类之间的耦合关系.

  ### 优选组合再考虑继承
  组合比继承松耦合，因为继承相当于所有的设计细节都被子类知道。

  ## 设计模式

  ### 创建型模式
  #### 简单工厂模式
   ![](https://sailorlou.github.io/image/DesignPattern/SimpleFactory.png)
  #### 工厂模式
  ![](https://sailorlou.github.io/image/DesignPattern/Factory.png)
  #### 抽象工厂模式
  ![](https://sailorlou.github.io/image/DesignPattern/AbstractFactory.png)
  #### 构建者模式
   ![](https://sailorlou.github.io/image/DesignPattern/Builder.png)
  #### 原型模式
    ![](https://sailorlou.github.io/image/DesignPattern/Prototype.png)
  #### 单例模式
- 单线程中

```c

//Singleton.hxx
// 以下是一个单例模板类，但并不是线程安全的
#ifndef SINGLETON_HXX_INCLUDED
#define SINGLETON_HXX_INCLUDED


#define DECLARE_SINGLETON_CLASS( type ) friend class Singleton< type >;\
                                        friend std::unique_ptr<type> std::make_unique<type>();

template <class T>
class Singleton
{
public:
    static inline T* GetInstance();
private:
    Singleton(void) {}
    ~Singleton(void) {}
    Singleton(const Singleton&);
    Singleton & operator= (const Singleton &);
    static std::unique_ptr<T> S_instance;
};
template <class T>
std::unique_ptr<T> Singleton<T>::S_instance;
template <class T>
inline T* Singleton<T>::GetInstance()
{
    if (S_instance.get() == nullptr)
    {
        S_instance = UGS::Estd::make_unique<T>();
    }
    return S_instance.get();
}

#endif  //SINGLETON_HXX_INCLUDED

// 在类中用法
// 在单例类的私有语句块中添加如下
DECLARE_SINGLETON_CLASS(Class);

//用法：
 typedef Singleton<Object> ObjectSingleton;

auto segleton = ObjectSingleton::GetInstance();

```
- 静态初始化

```c

template <class T>
class Singleton
{
  public:
    static T* GetInstance();
protected:
    Singleton()=default;
private:
    Singleton(const Singleton&)=delete;
    Singleton& operator=(const Singleton&)=delete;
    static T* m_instance;
};


template <class T>
T* Singleton<T>::GetInstance()
{
    return m_instance;
}

template <class T>
T* Singleton<T>::m_instance = new T(); // todo:need use shared pointer

```
- 双重锁定

```c

template <class T>
class Singleton
{
  public:
    static T* GetInstance(); //todo:shared pointer
   
private:
    Singleton()=default;
    Singleton(const Singleton&)=delete;
    Singleton& operator=(const Singleton&)=delete;
    static T* m_instance;
    static pthread_mutex_t mutex;

};


template <class T>
T* Singleton<T>::GetInstance()
{
    if( m_instance == nullptr)
    {
        pthread_mutex_lock(&mutex);
        if( m_instance == nullptr)
        { 
             m_instance = new T();
        }
        pthread_mutex_unlock(&mutex);
    }
    return m_instance;
}


template <class T>
pthread_mutex_t Singleton<T>::mutex = PTHREAD_MUTEX_INITIALIZER;

template <class T>
T* Singleton<T>::m_instance = nullptr;

```

### 结构型模式
#### 适配器
#### 桥接
#### 组合
#### 装饰
#### 外观
#### 享元
#### 代理模式
### 行为型模式
#### 职责链
#### 命令
#### 解释器
#### 迭代器
#### 中介者
#### 备忘录
#### 观察者
#### 状态
#### 策略
#### 模板方法
#### 访问者
## 结束语

  






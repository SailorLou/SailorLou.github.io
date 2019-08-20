---
layout:     post
title:      "设计准则与设计模式"
subtitle:   "基础知识总结"
date:       2019-05-25 16:00:00
author:     "Sailor"
categories:  编程哲学
mathjax: true
tags:  编程哲学
---

本文我想探讨的主题应该算是软件编程的的哲学-设计模式和设计准则

<!-- more -->

<!-- TOC -->

- [1.前言](#1前言)
- [2. overview](#2-overview)
- [3.设计准则](#3设计准则)
    - [3.1 抽象数据类型(Abstract Data Types ADTs)](#31-抽象数据类型abstract-data-types-adts)
    - [3.2 单一原则(SRP)](#32-单一原则srp)
    - [3.3 开闭准则(OCP)](#33-开闭准则ocp)
    - [3.4里氏替换原则(LSP)](#34里氏替换原则lsp)
    - [3.5 接口隔离原则(ISP)](#35-接口隔离原则isp)
    - [3.6 依赖倒置原则(DIP)](#36-依赖倒置原则dip)
    - [3.7 迪米特法则（最少知道原则 LOD）](#37-迪米特法则最少知道原则-lod)
    - [3.8 优选组合再考虑继承](#38-优选组合再考虑继承)
- [4.设计模式](#4设计模式)
    - [4.1创建型模式](#41创建型模式)
        - [4.1.1简单工厂模式](#411简单工厂模式)
        - [4.1.2工厂模式](#412工厂模式)
        - [4.1.3抽象工厂模式](#413抽象工厂模式)
        - [4.1.4构建者模式](#414构建者模式)
        - [4.1.5原型模式](#415原型模式)
        - [4.1.6单例模式](#416单例模式)
    - [4.2 结构型模式](#42-结构型模式)
        - [4.2.1 适配器](#421-适配器)
        - [4.2.2桥接](#422桥接)
        - [4.2.3组合](#423组合)
        - [4.2.4装饰](#424装饰)
        - [4.2.5外观](#425外观)
        - [4.2.6享元](#426享元)
        - [4.2.7代理模式](#427代理模式)
    - [4.3行为型模式](#43行为型模式)
        - [4.3.1职责链](#431职责链)
        - [4.3.2命令](#432命令)
        - [4.3.3解释器](#433解释器)
        - [4.3.4迭代器](#434迭代器)
        - [4.3.5中介者](#435中介者)
        - [4.3.6备忘录](#436备忘录)
        - [4.3.7观察者](#437观察者)
        - [4.3.8状态](#438状态)
        - [4.3.9策略](#439策略)
        - [4.3.10模板方法](#4310模板方法)
        - [4.3.11访问者](#4311访问者)
- [5结束语](#5结束语)

<!-- /TOC -->

## 1.前言
早想写一篇关于设计模式的总结，可一直不敢下笔，因为设计模式和设计准则太过深邃，不知如何动笔，也害怕自己认知有限，误导他人。
后来想明白，这篇总结是自己以往工作和学习的总结，便于自己复读用的，也无关他人，如果有缘人看到此文，发现错误处或不同意见，期待你的批评指教.也欢迎联系我loufuyi@163.com进行讨论交流，认知是一个过程性的活动，我敢肯定的是这篇文章随着我的开发工作的进行一定会有修改.

## 2. overview
本文我之所以说他是编程哲学，因为这些方法也常常出现在我们生活和其他学科中，计算机南北桥的设计，计算机网络的分层结构等
我本想试图给设计模式一个定义，又不想照本宣科，白话说就是写好程序，这个好又该如何评价呢？我想会有以下几个维度来衡量软件的设计：
- 代码复用
- 响应需求变更快
- 移植性
- 可扩展性

可能有人会说好代码还有其他特征比如说：好的代码规范，命名规则，代码执行效率，代码的可读性等等.

## 3.设计准则
设计准则（SOLID..)应该是内功心法，是设计模式的纲。我的第一任老板有个很好的工作习惯：他应该每做完设计后，都会拿这些设计准则进行校验自己的设计，我也吸取了他的经验，也分享给有缘人.

### 3.1 抽象数据类型(Abstract Data Types ADTs)
很多讲解设计模式的书都没有阐述数据抽象的重要性，可能数据抽象是需求分析的组成部分但不是设计模式的范畴吧，更不算是设计准则，但我想把他放在设计准则中，以突出他的重要性。我个人觉得，数据抽象是把现实的业务或是场景抽象成虚拟世界的数据集合(或者类)，他是设计模式应用的基石，OOAD的基础，没有好的抽象，一切设计模式都显得太过脆弱.如何做抽象我觉得是对业务需求有足够的了解和认知，能够掌握需求的边界条件及其可扩展的点，我觉得在《代码大全》中对于抽象和类的定义的阐述是最好的，以下引用几个好的结论性的总结：
- 良好的类接口：通过接口来展现的合理抽象，并确保细节被影藏在抽象背后.
- 尽可能地限制类和成员的可访问性
- 不要公开暴露成员数据
- 避免把使用的实现细节放在类的接口中-比如接口类中定义了数据成员.
- ...

### 3.2 单一原则(SRP)
- 官方解释是：对一个类而言，应该仅有一个引起它变化的原因，
- 我的理解是：设计类或者模块时，职责要清晰（单一），不能太多，否则其他的类任何相关的类发生变化，就会影响到这个类，
类与类之间的关系也就不牢靠。就像人，具有独立人格的朋友很容易建立巩固的关系，你就会理解他什么会做，什么就不会做，也会取得你的信任。
### 3.3 开闭准则(OCP)
-  官方解释是：对扩展开放，对修改封闭;
- 换一句说面对需求的变更时，最大化的保持已有代码的稳定性，而添加新的类或代码.比如说模块与模块之间的信息交流可以抽象接口。
在大型系统或者平台中，比如应用模块很多的情况下，上层把模块的注册接口定掉，在具体的新增应用或模块中，按照架构师订立的接口实现，
就能把应用或者模块集成在现有的系统或平台中.

### 3.4里氏替换原则(LSP)
- 官方解释是：用子类对象替换掉所有父类被应用的地方，程序依然可以正常的运行，并没发生任何变化.
- 具体体现：
    - 子类可以实现父类的抽象方法，但是不能覆盖父类的非抽象方法。
    - 子类当然是可以有自己的个性的（方法）
    - 子类重载父类的方法时，子类方法的前置条件(形参)要比父类方法的输入参数更宽松.
      也就是说子类的参数类型范围比父类大，那么子类的方法就不会执行
- 该原则不仅仅指导如何使用继承关系，而且可以用来指导接口与其实现方式的一种设计原则，

### 3.5 接口隔离原则(ISP)
- 一个模块或者类通过最小的粒度的接口与另一个模块或类建立依赖关系。
- 任何层次的软件设计如果依赖于不需要的东西，都会有害处的，从源代码的角度说，这样的依赖关系会导致不必要的重新编译和重新部署.
- 接口的设计要保证最小的粒度，不应该出现不需要的方法，或者适当分解大的接口为多个单一的接口，是职责单一化.
### 3.6 依赖倒置原则(DIP)
- 官方解释是：高层模块不应该依赖底层模块，两个都应该依赖抽象；抽象不应该依赖细节（具体的实现类），细节应该依赖抽象；
- 针对接口编程，而不是针对实现编程.模块与模块之间的依赖可以通过抽象（或叫接口）来降低耦合.
- 例如，我们在开发图形化界面时，用QT做界面，用VTK来显示三维物体，这时，QT界面就是高层模块，VTK 就是底层模块；那么我们愉快的在QT里
  直接调用VTK里的各种渲染。当有一天说VTK收费了，我们要换成OpenGL，那么界面与vtk之间的耦合又太过高，重构必将是一场灾难。如果我们能够抽象
  一层图形引擎接口，那么QT不直接用VTK，而是用这个抽象层的，那么当我们要opengl换掉vtk时，只需要重新实现这个抽象的图形引擎接口即可，无需动QT的业务逻辑.

### 3.7 迪米特法则（最少知道原则 LOD）
  - 类的设计中，应对其他类保持最小的了解.这样可以降低类与类之间的耦合关系.

### 3.8 优选组合再考虑继承
  组合比继承松耦合，因为继承相当于所有的设计细节都被子类知道。

## 4.设计模式

### 4.1创建型模式
#### 4.1.1简单工厂模式
   ![](https://sailorlou.github.io/image/DesignPattern/SimpleFactory.png)
#### 4.1.2工厂模式
  ![](https://sailorlou.github.io/image/DesignPattern/Factory.png)
#### 4.1.3抽象工厂模式
  ![](https://sailorlou.github.io/image/DesignPattern/AbstractFactory.png)
#### 4.1.4构建者模式
   ![](https://sailorlou.github.io/image/DesignPattern/Builder.png)
#### 4.1.5原型模式
    ![](https://sailorlou.github.io/image/DesignPattern/Prototype.png)
#### 4.1.6单例模式
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

### 4.2 结构型模式
结构型：如何组合类和对象以获得更大的结构.

#### 4.2.1 适配器
该模式主要解决两个已有接口之间不匹配的问题：
   ![](https://sailorlou.github.io/image/DesignPattern/Adapter.png)
#### 4.2.2桥接
原理：将抽象和实现分离
![](https://sailorlou.github.io/image/DesignPattern/Bridge.png)
#### 4.2.3组合
![](https://sailorlou.github.io/image/DesignPattern/Composite.png)

- 可能add 和remove 在基类中声明，导致leaf子类就必须重新实现它，虽然是无用的实现，但这个好处是：composite类和leaf类没有区别，在client端访问的时候，只需要知道是Componet，而无需区别leaf 和composite，这样可能会更加具有抽象性（优选）.
- 也可以吧add和remove的声明只放在composite中，这样leaf就没有
无用的方法重写，但在client端就的添加区分leaf 和 composite的逻辑。

#### 4.2.4装饰
该模式旨在使你能够不需要创建子类即可对已给的对象添加新职责.
![](https://sailorlou.github.io/image/DesignPattern/Decorator.png)

- 注意点： 如果类很大的时候（比如有20个虚方法时），避免用该类，否则你会很痛苦
#### 4.2.5外观
原理：将一个系统划分成若干个子系统有利于降低系统的复杂性，一个常见的设计目标是引入外观Facede对象为子系统中提供一个单一而统一的界面或接口，从而使使子系统间的通信和相互依赖关系达到最小。
![](https://sailorlou.github.io/image/DesignPattern/Facede.png)

要点：
- 如果子系统中存在依赖或者通讯可以通过Facede对象来完成，这一点又很像中介者模式

- 利用Facede 使客户端与系统之间进行分离，把对系统中的相互细节封装起来，利于扩展
- 把大系统分解为子系统，有利于职责单一化，小系统也利于代码的复用
- 客户端仅仅面对的是Facede独立和统一的接口，而不用特过于了解系统的设计细节;

- 我觉得还有一层的理解：可以把大系统里的子系统理解为子组件（零部件），把Facede当做组装厂，不一样的产品可以用不一样的facede组装而成，并且至于两个零部件的链接方式（齿轮或链条）也可以由组装厂决定.

#### 4.2.6享元
- 利用内存共享技术有效的地支持大量细粒度的对象
- 在程序设计中，有时为了实例化类的数量，把参数分成两类：内部共享参数和外部独自的参数，把外部参数移到类的外部作为外部参数传递类的方法中，而是内部参数作为共享类固有属性，这样就能构造实例化的数量就会少很多.
- 应用场景有
    - 当使用大量的的对象，造成很大的存储开销
    - 对象的大多数状态都可以变为外部状态

![](https://sailorlou.github.io/image/DesignPattern/Flyweight.png)

#### 4.2.7代理模式
原理：为其他对象提供一种代理以控制对这个对象的访问
![](https://sailorlou.github.io/image/DesignPattern/Proxy.png)
- UML图的要点：
    - subject定义realsubject 和proxy的公用接口，这样就在任何使用RealSubject的地方都可以使用Proxy
    - proxy会保存一个引用使得代理可以访问实体，proxy会引用subject
- 代理模式的应用：
    - 远程代理：为一个对象在不同的地址空间提供局部代表，可以隐藏一个对象存在于不同地址空间的事实（没用过）
    - 虚代理：根据需要创建开销很大的对象（很像智能指针的用法）
    - 保护代理：用于控制真实对象的访问权限
    - 智能指引：取代了简单的指针，他在访问对象时执行一些附件操作.

### 4.3行为型模式
行为模式：涉及到算法和对象间职责的分配，不仅描述类和对象的模式还描述类或对象之间的通信模式

#### 4.3.1职责链
使多个对象都有机会处理请求，从而避免请求的发送者和接受者之间的耦合关系，将这个对象连在一起形成了一条链，并延着这条链一次传递请求并处理.
![](https://sailorlou.github.io/image/DesignPattern/ChainOfResponsibility.png)

注意：有时候也会和组合模式结合来用.

#### 4.3.2命令
原理：将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化，对请求排队或记录请求日志，以及支持可撤销的操作.
![](https://sailorlou.github.io/image/DesignPattern/Commander.png)
#### 4.3.3解释器

官方： 给定一个语言，定义它的文法的一种表示，并定义一个解释器，这个解释器使用该表示来解释语言中的句子.
![](https://sailorlou.github.io/image/DesignPattern/Expression.png)
应用：通常当一个语言需要解释执行，并且可以将该语言中的句子表示为一个抽象的语法树时，可以使用该模式
如：正则表达式：raining&(dogs|cats)*

![](https://sailorlou.github.io/image/DesignPattern/ExpressionTree.png)
![](https://sailorlou.github.io/image/DesignPattern/ExpressionDesign.png)
- 
#### 4.3.4迭代器
原理：提供一种方法顺序访问一个聚合对象中各个元素，而又不需要暴露该对象的内部表示
应用：当需要对聚合对象有多种方式遍历时，可以考虑用迭代器模式，如从前到后，从后到前，从大到小等.
![](https://sailorlou.github.io/image/DesignPattern/Iterator.png)

#### 4.3.5中介者
原理：用一个中介对象来封装一些列对象交互，中介者使各对象不需要显示地相互引用，从而使其松耦合松散，而且可以独立地改变他们之间的交互.

应用场景有：
- 一组对象以定义良好但是复杂的方式进行通信，产生的相互依赖关系结构混乱且难以理解
- 一个对象引用其他很多对象并且直接与这些对象通信，导致难以复用该对象
- 想定制一个分布在多个类中的行为，而又不想生成太多的子类.
![](https://sailorlou.github.io/image/DesignPattern/Mediator.png)

#### 4.3.6备忘录

原理：在不破坏封装性的前提下，补货一个对象的内部状态，并在该对象之外保存这个状态，这样以后就可将该对象恢复到原先保存的状态.

要点：
- 一个备忘录是一个对象，保存另一个对象在某一瞬态的内部状态
- 防止Originator以外的其他对象访问备忘录.
![](https://sailorlou.github.io/image/DesignPattern/Memento.png)

#### 4.3.7观察者

原理： 让多个观察者对象同时监听某一主题对象，当这个主题对象发生变化时会通知所有观察者对象，使他们能够自动更新自己.
注意：有时候会配合组合模式和职责链模式来处理消息机制.比如 画一副笛卡尔坐标系，当轴上的字体的类型变化时（subject变化），可以触发整个坐标系的绘制（subject 所attach的model对象）.
![](https://sailorlou.github.io/image/DesignPattern/Observer.png)

注意：当一个observer监听多个subject时，subject的Notify时可以把消息源传入IObserver的update函数中：update（ISubject*）

#### 4.3.8状态
原理：允许一个对象在其内部状态改变时改变它的行为。
应用场景：
- 一个对象的行为取决于它的状态，并且它必须在运行时刻根据状态改变它的行为
- 一个操作中含有庞大的多分支的条件语句，且这些分支依赖于该对象的状态.
![](https://sailorlou.github.io/image/DesignPattern/State.png)
注意：
状态模式将所有与一个特定的状态相关的行为都放入一个对象中，所有与该状态相关的代码都放在某一个state的子类中，所以当有新的状态时很容易扩展。
#### 4.3.9策略
原理：定义一系列的算法，把它们一个个封装起来，并且使它们可相互替换，本模式使得算法可独立于使用它的客户端而变化.
![](https://sailorlou.github.io/image/DesignPattern/Strategy.png)
#### 4.3.10模板方法
原理：定义一个算法骨架，而将一些步骤延迟到子类中，在子类中不改变算法整体结构，而可以调整算法某些细节（通过重写某一步骤）
![](https://sailorlou.github.io/image/DesignPattern/Template.png)
#### 4.3.11访问者
原理：表示一个作用于某对象结构中的各元素的操作，它使不改变各元素的类的前提下定义作用于这些元素的新操作.
应用场景：
- 要把处理操作从数据结构分离出来
- 有稳定的数据结构，又有易于变化的算法的话，使用访问者模式就是比较合适的因为访问者模式使得算法操作的增加变得容易

![](https://sailorlou.github.io/image/DesignPattern/Visitor.png)

## 5结束语

除了单例模式，其他的设计模式都是用UML画出来的（startUML）,我想无需再写代码.

目前仅仅简单总结了设计模式和设计准则，可能仍然需要详细总结每一个设计模式它的应用场景，不足之处，及其模式之间的
相通相异，这个可能以后逐渐补足.

设计准则是武功心法，设计模式是武功招式，用心法可以校验自己的设计而不拘泥于招式或者甚至自创招式，理解招式或熟练各个招式
更能触发自己领悟心法.我想设计模式和设计准则是一个长期领悟学习的过程.

设计模式说重要肯定重要，但一些公司可能并不注重这些，直接看着寥寥几笔的需求就开始写代码了，也没有什么完善的需求分析阶段，
概要设计及其详细设计阶段，依旧可以上线商用，有些人可能也陷入了纠结：系统行为和设计架构那个重要，我想只有艾森豪威尔矩阵可以
阐述清楚吧. 作为软件开发，我想说的时设计模式是内功修养，不管在什么样的企业，写出有素质的代码，做一个不狭隘的人.




  






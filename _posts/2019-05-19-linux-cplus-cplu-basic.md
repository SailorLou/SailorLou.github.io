---
layout:     post
title:      "linux c/c++ 程序入门"
subtitle:   "Linux Study"
date:       2019-05-19 16:00:00
author:     "Sailor"
categories:  linux
mathjax: false
tags:
    -Linux c++
---

本文主要是对makefile，gcc 以及gdb的学习




<!-- TOC -->

- [Linux C/C++ 程序基础入门](#linux-cc-程序基础入门)
    - [1. gcc/g++](#1-gccg)
    - [2. makefile](#2-makefile)
        - [2.1 概念](#21-概念)
        - [2.2 规则](#22-规则)
        - [2.3 变量](#23-变量)
        - [2.4 引用其他的makefile文件](#24-引用其他的makefile文件)
        - [2.5 查看c文件的依赖关系](#25-查看c文件的依赖关系)
        - [2.6 定义命令包或函数](#26-定义命令包或函数)
        - [2.7 条件判断](#27-条件判断)
        - [2.8 Makefile 中的函数](#28-makefile-中的函数)
        - [2.9 编写规则分析-实例](#29-编写规则分析-实例)
            - [2.9.1 案例1](#291-案例1)
            - [2.9.2 案列 2](#292-案列-2)
            - [2.9.3 案例3](#293-案例3)
            - [2.9.4 案例4](#294-案例4)
            - [2.9.5 案例5](#295-案例5)
        - [2.10 make 的工作方式](#210-make-的工作方式)
    - [3. gdb调试](#3-gdb调试)

<!-- /TOC -->


## 1. gcc/g++
- c++ 程序的编译过程如下图：
![](https://sailorlou.github.io/image/linux-cplusplus/compile_process.PNG)
![](https://sailorlou.github.io/image/linux-cplusplus/compile_process1.PNG)
- gcc/g++ 的常用命令
![](https://sailorlou.github.io/image/linux-cplusplus/gcc-commands.PNG)
- 静态库的生成
![](https://sailorlou.github.io/image/linux-cplusplus/staticlib.PNG)
- 动态库的生成
![](https://sailorlou.github.io/image/linux-cplusplus/sharelib.png)

## 2. makefile
### 2.1 概念
- makefile 是一条或多条规则组成
- 命名 makefile and Makefile
- 编写规则

```

目标1：目标依赖  然后回车+tab键
 命令

目标2：目标依赖  然后回车+tab键
 命令
...

目标n：目标依赖  然后回车+tab键
 命令

注意：命令必须是tab键开头的

```

### 2.2 规则
- 主要由三部分组成：目标，依赖和命令
- 一般形式是：“目标：依赖 命令
    - command太长, 可以用 "\" 作为换行符
    -比如

    ```
    gcc a.c b.c c.c -O app 可以改写成：
    app：a.c b.c c.c
         gcc a.c b.c c.c -o app

    ```
- 规则中的通配符
    - \* : 表示任意一个或多个字符，%标记和系统通配符*的区别在于，*是应用在系统中的，%是应用在这个Makefile文件中的。

    - ?  : 表示任意一个字符
    - [...]  : ex. [abcd] 表示a,b,c,d中任意一个字符, [^abcd]表示除a,b,c,d以外的字符, [0-9]表示 0~9中任意一个数字
    - ~ 表示用户的home目录
- 路径搜索
    - 当一个Makefile中涉及到大量源文件时(这些源文件和Makefile极有可能不在同一个目录中),这时, 最好将源文件的路径明确在Makefile中, 便于编译时查找. Makefile中有个特殊的变量 VPATH 就是完成这个功能的
    - 如果当前目录中没有找到相应文件或依赖的文件, Makefile 回到 VPATH 指定的路径中再去查找
    - VPATH src:../parent-dir   当前目录中找不到文件时, 按顺序从 src目录 ../parent-dir目录中查找文件
    - VPATH %.h ./header 示例2 - .h结尾的文件都从 ./header 目录中查找
    - VPATH 清除所有已经设置好的文件路径
    - vpath <pattern>  清除符合<pattern>格式的文件搜索路径;VPATH %.h 清除示例2中设置的规则 

### 2.3 变量
![](https://sailorlou.github.io/image/linux-cplusplus/variable.PNG)

### 2.4 引用其他的makefile文件
- 语法: include <filename>  (filename 可以包含通配符和路径)
- include ./other/Makefile
- export 
### 2.5 查看c文件的依赖关系
比如, 下面命令显示内核源码中 virt/kvm/kvm_main.c 中的依赖关系   
```   
$ cd virt/kvm/    
$ gcc -MM kvm_main.c 
```   
kvm_main.o: kvm_main.c iodev.h coalesced_mmio.h async_pf.h   <-- 这句就可以加到 Makefile 中作为编译 kvm_main.o 的依赖关系

### 2.6 定义命令包或函数
定义这种命令序列的语法以“define”开始，以“endef”结束，如： 

```
all:                    
    @$(cmd)

define cmd
    echo "test define 1"
    echo "test define 2"
    echo "test define 3"
endef
```   
“cmd”是这个命令包的名字，其不要和Makefile中的变量重名。在“define”和“endef”中的两行就是命令序列。在define 和　endef　间的部分即是命令主体。

### 2.7 条件判断

- 格式：

```   

conditional directive   
   text-if-true   
endif   

或者   
conditional-directive   
   text-if-true   
else   
   text-if-false     
endif      

```   

- 实例   

```   
# Makefile 内容
all:
ifeq ("aa", "bb")
    @echo "equal"
else
    @echo "not equal"
endif

# bash 中执行 make
$ make
not equal       
```   

### 2.8 Makefile 中的函数

- 语法如下：    

```    
$(<function> <arguments>)
或是
${<function> <arguments>}       
```    
make支持的函数不多。* arguments*是函数的参数，参数间以逗号“,”分隔，而函数名和参数之间以“空格”分隔

- 常用函数如下：
![](https://sailorlou.github.io/image/linux-cplusplus/makefile_math.jpg)

### 2.9 编写规则分析-实例

#### 2.9.1 案例1
- makefile 文件

```    
app：add.c sub.c mul.c
    gcc add.c sub.c mul.c -o app

```    

- 缺点是:效率低，修改一个文件，所有文件会被全部重新编译

#### 2.9.2 案列 2
- makefile 是：

```   
app ： main.o add.o sub.o mul.o
       gcc main.o add.o sub.o mul.o -o app
main.o:main.c
       gcc main.c -c  
add.o:add.c
       gcc add.c -c   
sub.o:sub.c
       gcc mul.c -c 
mul.o:mul.c
       gcc mul.c -c 

```    
- 工作原理：
    - 检测依赖是否存在，向下搜索下边的规则，如果有规则是用来生成查找的依赖的，执行规则中的命令。
    - 依赖存在，判断是否需要更新,如果目标时间大于依赖时间则不需要，否则需要更新

- 缺点：太冗余

#### 2.9.3 案例3
    
```    
obj = main.o add.o sub.o mul.o
target = app
$(target):$(obj)
          gcc $(obj) -o $(target)
%.o:%.c
   gcc -c $< -o $@
    
```    

- 案列解析
    - 自定义变量 obj=main.o add.o
    - 变量取值：aa = $(obj)
    - makefile 自带的变量是大写的如 CPPFLAGS ，CC
    - 自动变量
        - $@:规则中的目标
        - $<：规则中的第一个依赖
        - $^:规则中所有依赖
        - 只能在规则的命令中使用
    - 模式匹配
        - %.o：%.o
        - 第一次 mian.o 没有，则main.o：main.c                        gcc -c main.C -o main.O 以次类推
- 可移植性比较差

#### 2.9.4 案例4

- makefile文件    

```
src =$(wildcard ./*.c)
obj = $(patsubst %.c ,%.o,$(src))

target = app
$(target):$(obj)
          gcc $(obj) -o $(target)
%.o:%.c
   gcc -c $< -o $@

```    

- 案列解析
    - makefile 所有的函数都有返回值
    - 查找指定目录下指定类型的文件：src=$(wildcard ./*.c)首先使用“wildcard”函数获取工作目录下的.c文件列表
    - obj=$(patsubst %.c,%.o,s(src))，将列表中所有文件名的后缀.c替换为.o，这样我们就可以得到在当前目录可生成的.o文件列表
    - wildcard : 扩展通配符
    - notdir ： 去除路径:dir=$(notdir $(src))
    - patsubst ：替换通配符
- 缺点不能清理项目 

#### 2.9.5 案例5

- makefile文件    

```
src =$(wildcard ./*.c)
obj = $(patsubst %.c ,%.o,$(src))

target = app
$(target):$(obj)
          gcc $(obj) -o $(target)
%.o:%.c
   gcc -c $< -o $@

hello:
    echo "Hello makefile"

.PHONY:clean
clean:
    -mkdir /abc
    -rm $(obj) $(target) -f     
```    

- 命令前加 -：忽略执行失败的命令，继续向下执行其余的命令
- -f ：强制执行
- .PHONY后面的target表示的也是一个伪造的target, 而不是真实存在的文件target..
-  PHONY: clean
    - means the word "clean" doesn't represent a file name in this Makefile;
    - means the Makefile has nothing to do with a file called "clean" 
      in the same directory.


### 2.10 make 的工作方式
![](https://sailorlou.github.io/image/linux-cplusplus/make.PNG)


## 3. gdb调试

- 保留调试信息       
```
>>> gcc test.c -o app    
>>> gcc test.c -o app -g
```    

-g 会保留函数名和变量名

- 启动gdb      

格式：gdb 可执行程序的名字   
如：gdb app   
给程序传参：set args xx

- 查看代码 --list -l   
    - 当前文件
        - l
        - l 行号
        - l 函数名
    - 非当前文件
        - l 文件名：行号
        - l 文件名：函数名
- 断点操作  -break/b
    - 设置断点
        - b 行号
        - b 函数名
        - b 文件名：行号
        - b 文件名：函数名
    - 查看断点
        - info（i） b
    - 删除断点
        - d num（断点的编号）
        - 删除多个：d num1 num2
    - 设置断点无效
        - dis num
    - 断点生效
        - ena num
    - 设置条件断点
        - b 行号 if 变量== var
- 调试相关命令
    - 让gdb跑起来
        - start 运行一行，停止
        - run -r 停在第一个断点的位置
    - 打印变量的值
        - p 变量名
    - 打印变量的类型
        - ptype 变量名
    - 向下单步调试
        - next - n 不会进入函数体
        - step -s 会进入函数体
            - 跳出函数体：finish，如果出不去，看一下函数体重的循环中是否有断点，如果有则删掉，或者设置无效
    - 继续运行gdb，停在下一个断点的位置
        - continue -c
    - 退出gdb
        - quit -q
    - 变量的自动显示
        - display 变量名
        - 取消： undisplay 编号
        - i display
    - 从循环体直接跳出
        - until 不能有断点
    - 直接设置变量等于某一个值
        - set var 变量名=value



    




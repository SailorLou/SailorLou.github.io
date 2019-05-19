---
layout:     post
title:      "C++ package management -- conan"
subtitle:   ""
date:       2017-10-31 22:00:00
author:     "Sailor"
header-img: ""
tags:
    c++ package management
---

# C/C++ Package Manager -- Conan

## 1 简介
## 2 重要概念介绍

## 3 在window上的应用
### 3.1 前期准备
首先在window上安装conan，python，cmake，vs.
### 3.2 构建库文件
- 在Visual Studio中创建空项目HelloConan 和配置好项目属性
    - Debug ：常规->配置类型:动态库
    - Debug : 常规-》目标文件名：$(ProjectName)D
    - release：常规->配置类型:动态库
    - release : 常规-》目标文件名：$(ProjectName)D

- 添加头文件 hello.h

```c
#pragma once

#ifdef DLL_EXPORTS
  #define HELLO_EXPORT __declspec(dllexport) 
#else
  #define HELLO_EXPORT  
#endif

HELLO_EXPORT void hello();

```
- 添加源文件hello.cpp

```c

#define DLL_EXPORTS
#include <iostream>

#include "hello.h"


void hello() {
    std::cout << "Hello Conan!" << std::endl;
}

```

- 分别在debug和release两种模式编译成lib和dll文件
- 在创建一个目录WindowConanTestLib 并在此目录创建三个文件夹bin,include 和lib，把上一步编译出的结果分别放在此三个目录下，静态库放在lib中，头文件放在include中，动态库放在bin文件夹中。

### 3.3 将编译好的库文件构建成包

- 由于以上的库HelloConan是编译好的，这里不需要用conan来编译
- 起一个命令行窗口，cd 到WindowConanTestLib，然后用如下命令

```
conan new holloconan/1.0.0@lou/testing    
```
此时会创建一个conan模板文件conanfile.py，通过修改conanfile.py如下：

```
from conans import ConanFile, CMake, tools

class HelloconanConan(ConanFile):
    name = "HelloConan"
    version = "1.0.0"
    license = ""
    author = "lou"
    url = ""
    description = "test conan"
    topics = ("test", "helloconan")
    settings = "os", "compiler", "build_type", "arch"
    description = "A test Conan lib"
    options = {"shared" : [True,False]}
    default_options = {"shared": False}
	
    def package(self):
        self.copy("*")
		
        if self.options.shared:
            self.copy(pattern="*.dll", dst="bin", keep_path=False)

    def package_info(self):
        self.cpp_info.release.libs = ["HelloConan"]
        self.cpp_info.debug.libs = ["HelloConanD"]
```

- 输入以下命令 创建包，并把HelloConan库文件拷贝到本地缓存中
并可以在路径C:\Users\Administrator\.conan\data\HelloConan\1.0.0\lou\testing\package上查找缓存.
```
    conan export-pkg . HelloConan/1.0.0@lou/testing
```
- 可以用 conan search 命令查看本地的package 列表，如果没有找到，则可以用命令conan create . lou/testing 创建
并在此运行上面的export命令，加上参数 -f 表示强制覆盖
```
    conan export-pkg . HelloConan/1.0.0@lou/testing -f
```

- 也可以用命令“conan inspect holloconan/1.0.0@lou/testing ” 查看包的元数据

### 3.4 使用包
- 创建另外一个文件夹WindowTest，编写测试代码main.cpp，如下
```c
#include "hello.h"

int main (void) {
    hello();
}
```

- 添加文件 conanfile.txt 如：
```
 [requires]
 HelloConan/1.0.0@lou/testing

 [generators]
 cmake

 [options]
 HelloConan:shared=False

 [imports]
 bin, *.dll -> ./bin
```

- 运行命令 “conan install .”，会有几个文件生成
    - conanbuildinfo.cmake
    - conanbuildinfo

- 然后编写CMakeLists.txt文件如下
```
cmake_minimum_required(VERSION 2.8.12)
project(TestHelloConan)

add_definitions("-std=c++11")

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()

add_executable(TestHelloConan main.cpp)

foreach(_LIB ${CONAN_LIBS_RELEASE})
    target_link_libraries(TestHelloConan optimized ${_LIB})
endforeach()
foreach(_LIB ${CONAN_LIBS_DEBUG})
    target_link_libraries(TestHelloConan debug ${_LIB})
endforeach()

```

- 运行如下命令构建vs工程
```
cmake . -G "Visual Studio 15"
```

## 4 在linux上的应用
### 4.1 准备工作
- 用virtual box 安装ubuntu   
并设备--》安装增强模式以便于全屏显示linux系统。
挂载共享文件夹
进入虚拟Ubuntu，在命令行终端下输入：
sudo mkdir /mnt/shared
sudo mount -t vboxsf share /mnt/shared
其中"share"是之前创建的共享文件夹的名字。OK，现在Ubuntu和主机可以互传文件了。
- 安装python  
sudo apt-get install python3  
可能会出如下错误
E: 无法获得锁 /var/lib/dpkg/lock - open (11: 资源暂时不可用)
E: 无法锁定管理目录(/var/lib/dpkg/)，是否有其他进程正占用它
强制解锁,在终端中键入命令
sudo rm /var/cache/apt/archives/lock
sudo rm /var/lib/dpkg/lock
然后再次输入 sudo apt-get update 后 再次安装python
- 安装cmake
文件下载：
wget https://cmake.org/files/v3.12/cmake-3.12.2-Linux-x86_64.tar.gz   
解压：
tar zxvf cmake-3.12.2-Linux-x86_64.tar.gz   
创建软链接
注: 文件路径是可以指定的, 一般选择在/opt 或 /usr 路径下, 这里选择/opt
mv cmake-3.12.2-Linux-x86_64 /opt/cmake-3.12.2
ln -sf /opt/cmake-3.12.2/bin/*  /usr/bin/   
检查一下：
cmake --version   
或者直接用以下命令直接安装cmake
sudo apt-get install cmake   
以下命令可以启动cmake图形界面
cmake-gui
- 安装pip   
首先安装 pip：   
sudo apt install -y python-pip   
然后安装 pip3：   
sudo apt install -y python3-pip 
更新 pip 版本：   
sudo pip install --upgrade pip   
更新 pip3 版本：   
sudo pip3 install --upgrade pip   

- install conan
pip install conan
sudo pip3 install conan   
或者sudo -H pip3 install conan
检测：conan --version

- install g++
sudo apt-get install g++

### 静态库
#### 静态库的创建

- 添加头文件 hello.h

```c
#pragma once

void hello();

```
- 添加源文件hello.cpp

```c

#include <iostream>

#include "hello.h"


void hello() {
    std::cout << "Hello Conan!" << std::endl;
}

```

- 创建静态库

```
>>> g++ -c hello.cpp
>>> ar cr libhello.a hello.o
```
注意：构建静态库时库名的命名libXXX.a，我试着不要lib没成功，不知是什么原因.
- 构建一个新目录，并添加一个lib 和 include文件夹，并把头文件拷贝到include中，把库文件拷贝到lib目录下
- 添加conanfile.py文件
```
from conans import ConanFile, CMake, tools

class HelloconanConan(ConanFile):
    name = "HelloConan"
    version = "1.0.0"
    license = ""
    author = "staticlib"
    url = ""
    description = "test conan"
    topics = ("test", "helloconan")
    settings = "os", "compiler", "build_type", "arch"
    description = "A test Conan lib"
    options = {"shared" : [True,False]}
    default_options = {"shared": True}
	
    def package(self):
        self.copy("*")
		
        if self.options.shared:
            self.copy(pattern="*.so", dst="bin", keep_path=False)

    def package_info(self):
        self.cpp_info.libs = self.collect_libs()
```

- 创建包
```
>>> conan export-pkg . HelloConan/1.0.0@staticlib/testing
```

-测试包   
创建新目录，main.cpp文件：  
```c
#include "hello.h"

int main (void) {
    hello();
}
```
- 添加文件 conanfile.txt 如：
```
 [requires]
 HelloConan/1.0.0@staticlib/testing

 [generators]
 cmake

```
- 运行命令 “conan install .”，会有几个文件生成
    - conanbuildinfo.cmake
    - conanbuildinfo

- 然后编写CMakeLists.txt文件如下
```
cmake_minimum_required(VERSION 2.8.12)
project(TestHelloConan)

add_definitions("-std=c++11")

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()

add_executable(TestHelloConan main.cpp)

target_link_libraries(TestHelloConan ${CONAN_LIBS})

```

- cmake .
- make
- ./bin/TestHelloConan
  output:Hello Conan！

#### 动态库的创建
- hello程序的头文件和源文件如同静态库
- 用如下命令构建动态库
```
g++ -c -fPIC hello.cc -o hello.o
g++ -shared hello.o -o libhello.so
```

- 其他操作和静态库一致。



## reference
- https://docs.conan.io



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

## 简介

## 在window上的应用
### 前期准备
首先在window上安装conan，python，cmake，vs.
### 构建库文件
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

### 将编译好的库文件构建成包

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

### 使用包
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


## reference
- https://docs.conan.io



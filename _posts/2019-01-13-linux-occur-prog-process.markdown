---
layout:     post
title:      "Linux 进程"
subtitle:   "linux"
date:       2019-01-20 22:00:00
author:     "Sailor"
header-img: "img/post-think-try-write.jpg"
tags:
    -Linux 并发编程
---

# Linux 并发编程之进程

<!-- TOC -->

- [Linux 并发编程之进程](#linux-并发编程之进程)
    - [1 进程的定义：](#1-进程的定义)
    - [2 进程结构](#2-进程结构)
        - [2.1 内核中的进程结构](#21-内核中的进程结构)
        - [2.2进程结构和IO 操作](#22进程结构和io-操作)
    - [3 进程启动](#3-进程启动)
    - [4 进程终止](#4-进程终止)
        - [4.1 正常终止](#41-正常终止)
        - [4.2 异常终止](#42-异常终止)
        - [4.3 进程返回](#43-进程返回)
        - [4.4 进程终止方式区别](#44-进程终止方式区别)
        - [5 进程常见状态](#5-进程常见状态)
    - [6 进程的分类](#6-进程的分类)
        - [6.1 守护进程](#61-守护进程)
        - [6.2 孤儿进程](#62-孤儿进程)
        - [6.2 僵尸进程](#62-僵尸进程)
    - [7 进程相关指令介绍](#7-进程相关指令介绍)
    - [8 进程相关函数介绍](#8-进程相关函数介绍)
        - [8.1 atexit](#81-atexit)
        - [8.2 进程标识](#82-进程标识)
        - [8.3 创建进程](#83-创建进程)
            - [8.3.1 子进程的继承属性](#831-子进程的继承属性)
        - [8.4 wait 函数](#83-wait-函数)
        - [参数 status](#参数-status)
        - [8.5 exec 函数](#84-exec-函数)
        - [8.6 system 函数](#85-system-函数)
        - [8.7 函数调用和进程状态的关系](#85-函数调用和进程状态的关系)

<!-- /TOC -->


## 1 进程的定义：  
- 程序是存放在磁盘文件中的可执行文件  
- 进程是程序的执行实例，且其具有独立的权限和职责，如果系统中一个某一个进程奔溃，它不会影响到其余的进程。
- 每一个进程运行在其各自的虚拟地址空间中，进程之间可以通过由内核控制的机制相互通讯。
- 且每一个进程都由一个唯一非负的ID标识。 

## 2 进程结构  
### 2.1 内核中的进程结构   
![](https://sailorlou.github.io/image/occ/process_structure_from_kernel.PNG)

### 2.2进程结构和IO 操作  
![](https://sailorlou.github.io/image/occ/process_structure.PNG)
![](https://sailorlou.github.io/image/occ/process_structure_io.PNG)

## 3 进程启动
在进程的main函数之前内核会启动。
 
启动例程的作用：  

- 搜集命令行的参数传递给main函数中的argv和argc
- 搜集环境信息构件环境变传递给main函数
- 登记进程的终止函数

## 4 进程终止
### 4.1 正常终止

- 从main函数返回
- 调用exit（标准c库函数
- 调用_exit(系统调用)
- 租后一个线程从其启动例程返回
- 最后一个线程调用pthread_exit

### 4.2 异常终止

- 调用abort
- 接受到一个信号并终止
- 最后一个线程对取消请求做处理响应

### 4.3 进程返回
通常程序运行成功返回0，否则返回非0.在shell中可以查看京城返回值（echo $?)
### 4.4 进程终止方式区别  
| | return |exit | _exit/_Exit|
|:-:|:-:|:-:|:-:|
|是否刷新标准I/O缓存|是|是|否|
|是否自动调用终止函数|是|是|否|

### 5 进程常见状态
![](https://sailorlou.github.io/image/occ/process_state.PNG)
- 运行状态
    - ps命令的stat列为值R
- 等待状态
    - ps命令的stat列为值s
- 停止状态
    - ps命令的stat列为值t
    - 等待系统资源或事件发生
- 僵尸状态
    - 进程终止或结束
    - 在进程表项中仍然有记录
    - ps命令的STAT列值为z

## 6 进程的分类
### 6.1 守护进程
- 是生存期长的一种进程，它们常在系统引导装入时启动，在系统关闭时终止。
- 所有的守护进程都是以超级用户（用户ID为0）的优先权运行。
- 守护进程没有控制终端
- 守护进程的父进程都是init进程

### 6.2 孤儿进程
父进程结束，子进程就成为孤儿进程，会由1号进程（init进程）领养。

### 6.2 僵尸进程
- 子进程结束但是没有完全释放内存（在内河中的task—struct 没有释放），该进程就成为僵尸进程。
- 当僵尸进程的父进程结束后，就会被init进程领养，最终被回收。
- 避免僵尸进程
    - 让僵尸进程的父进程来回收，父进程每隔一段时间来查询子进程是否结束并回收，调用wait（）或者waitpid（）通知内河释放僵尸进程。
    - 采用信号SIGCHLD通知处理，并在信号处理程序中调用wait 函数
    - 让僵尸进程成为孤儿进程，由init进程回收。

## 7 进程相关指令介绍

 ### 7.1 ps 
 
$ ps -aux 查看进程的ID,进程的用户ID，进程状态和进程的command 
ps -ef 是用标准的格式显示进程的  
ps aux 是用BSD的格式来显示    
USER         //用户名   
%CPU         //进程占用的CPU百分比   
%MEM        //占用内存的百分比   
VSZ         //该进程使用的虚拟內存量（KB）   
RSS        //该进程占用的固定內存量（KB）  （驻留中页的数量）   
STAT      //进程的状态   
START     //该进程被触发启动时间   
TIME      //该进程实际使用CPU运行的时间  

其中STAT状态位常见的状态字符有  
D      //无法中断的休眠状态（通常 IO 的进程）；   
R      //正在运行可中在队列中可过行的；   
S      //处于休眠状态；   
T      //停止或被追踪；   
W      //进入内存交换 （从内核2.6开始无效）；   
X      //死掉的进程 （基本很少见）；   
Z      //僵尸进程；   
<      //优先级高的进程   
N      //优先级较低的进程   
L      //有些页被锁进内存；   
s      //进程的领导者（在它之下有子进程）；   
l      //多线程，克隆线程（使用 CLONE_THREAD, 类似 NPTL pthreads）；

## 8 进程相关函数介绍
### 8.1 atexit
```c
    #include<stdlib.h>  
    int atexit(void(*fouction)(void));
```	
    返回：若成功则为 0，出错则为-1  
    功能：向内核登记终止函数   
**关键点：** 
 
 - 每一个启动的进程都默认登记了一个标准的终止函数；  
 - 终止函数在进程终止时释放所占用的一些资源
 - 登记的多个终止函数执行顺序以栈的方式执行，先登记的后执行

### 8.2 进程标识

![](https://sailorlou.github.io/image/occ/process_get_ids.PNG)

### 8.3 创建进程     

```c
 #include <sys/types.h>
 #include <unistd.h>

 pid_t fork(void);
 返回：子进程中为0，父进程中子进程id，出错为-1
       等于0表示为子进程，大于零为父进程，小于0 创建出错

 pid_t vfork(void);
 返回：子进程中为0，父进程中子进程id，出错为-1
```
fork 创建新进程，该函数被调用一次，但返回两次，父子进程那个先运行由系统调度决定，且子进程复制父进程的内存空间.
vfork 创建子进程，但子进程先运行且不复制父进程的内存空间.

#### 8.3.1 子进程的继承属性
 - 用户信息和权限
 - 目录信息
 - 信号信息
 - 环境
 - 共享储存段
 - 资源限制
 - 堆，栈和数据段
 - 共享代码段
 
 ![](https://sailorlou.github.io/image/occ/process_fork_inherit.PNG)

#### 8.3.2 子进程的特有属性
  - 进程ID
  - 锁信息
  - 运行时间
  - 未决信号
        
#### 8.3.3 操作文件时内核结构变化

 - 子进程只继承父进程的文件描述表，不继承但共享文件表项和i-node。
 - 父进程创建一个子进程后，文件表项中的引用计数器加1变成2，当父进程作close操作后，计数器减一，子进程还是可以使用文件表项，只有当计数器为0时才会释放文件表项。也就是说，fork的另一个特性是所有由父进程打开的描述符都被复制到子进程中。父、子进程中相同编号的文件描述符在内核中指向同一个file结构体，也就是说，file结构体的引用计数要增加。
 - fork()函数调用完成以后父进程的虚拟存储空间被拷贝给了子进程的虚拟存储空间，因此也就实现了共享文件等操作。但是虚拟的存储空间映射到物理存储空间的过程中采用了写时拷贝技术（具体的操作大小是按着页控制的），该技术主要是将多进程中同样的对象（数据）在物理存储其中只有一个物理存储空间，而当其中的某一个进程试图对该区域进行写操作时，内核就会在物理存储器中开辟一个新的物理页面,将需要写的区域内容复制到新的物理页面中，然后对新的物理页面进行写操作。这时就是实现了对不同进程的操作而不会产生影响其他的进程，同时也节省了很多的物理存储器。
 ![](https://sailorlou.github.io/image/occ/process_forc_file_table.PNG)

 ```c
 int main (void)
 {
     pid_t pid;
     pid = fork();

     if(pid <0)
     {
         perror("fork wrror\n");
         exit(1);

     }
     else if(pid ==0)  // child process
     {
          printf("pid=%d,parentPid=%d\n",getpid(),getppid());
          exit(0);  // 子进程结束成为僵尸进程
     }

     // parent process wait for child process finished

     while(1)
     {sleep(1);}

     exit(0);
}
 ```
 
### 8.4 wait 函数

```c
#include <sys/types.h>
#include <sys/wait.h>
// 返回：成功返回子进程ID，出错返回-1
// 功能：等待子进程退出并回收，防止孤儿进程产生
// 子进程终止前，wait 调用者阻塞,等待所有的
// 子进程返回任一终止子进程的状态
pid_t wait(int *status);
// 返回：成功返回子进程ID，出错返回-1
// 功能：wait函数的非阻塞版本
// options允许改变waitpid的行为，最有用的一个选项是WNOHANG，
// 它的作用是防止waitpid把调用者的执行挂起
// 返回值：成功返回等待子进程的pid，失败返回-1
pid_t waitpid(pid_t pid,int *status，int options);
```
*参数 status*   
参数传空时，代表任意状态结束的子进程，若不为空，则代表指定状态结束的子进程。
wait获取staus后检测处理  
- WIFEXITED(status) 如果进程子进程正常结束，返回一个非零值
- WEXITSTATUS(status) 如果WIFEXITED非零，返回子进程退出码
- WIFSIGNALED(status) 子进程因为捕获信号而终止，返回非零值
- WTERMSIG(status) 如果WIFSIGNALED非零，返回信号代码
- WIFSTOPPED(status) 如果进程被暂停，返回一个非零值
- WSTOPSIG(status) 如果WIFSTOPPED非零，返回信号代码

```c
int main()
{
    int status;
    pid_t pid;

    if(pid = fork())<0)
    {
        perror("fork error\n");
        exit(0);
    }
    else if(pid == 0)
    {
        printf("pid:%d,ppid:%d\n",getpid(),getppid());

        exit(3);
    }

    // 父进程调用wait 函数阻塞，等待子进程
    // 结束并回收
    wait(&status);
 }
```

### 8.5 exec 函数
```c
#include<unlstd.h>

int execl(const char *path, const char *arg, ...) 
int execv(const char *path, char *const argv[]) 
int execle(const char *path, const char *arg, ..., char *const envp[]) 
int execve(const char *path, char *const argv[], char *const envp[]) 
int execlp(const char *file, const char *arg, ...) 
int execvp(const char *file, char *const argv[])
/*
 * execve 函数为系统调用，其余为库函数；
 * execlp 和execvp 函数的path 是相对路径和绝对路径* 都可以，其他职能是绝对路径。
 * argv 参数 为新程序执行的mian函数的传递的argv参数，最后一个元素为null
 * envp 为进程的环境表
**/
```
- 在用fork函数创建子进程后，子进程往往要调用一种exec函数以执行另一个程序
- 当进程调用一种exec函数时，该进程完全由新程序代换，替换原有的进程的正文，而新程序则从其main函数开始调用，因为调用exec并不创建新进程，所以前后的进程ID并不改变，exec只是用另一个新的程序替换了当前的进程正文，数据，堆和栈段。

- 函数关系如下
![](https://sailorlou.github.io/image/occ/exec_func_relationship.PNG)


```c
int main()
{
    int status;
    pid_t pid;

    if(pid = fork())<0)
    {
        perror("fork error\n");
        exit(0);
    }
    else if(pid == 0)
    {
       if（execl(cmd1,cmd2,argv1,argv2,NULL)<0)
       {
           perror("execl error \n");
       }
        exit(1);
    }

    // 父进程调用wait 函数阻塞，等待子进程
    // 结束并回收
    wait(&status);
 }

 //another example
 {
     char *const ps_argv[] ={"ps", "-o", "pid,ppid,pgrp,session,tpgid,comm", NULL}; 
     char *const ps_envp[] ={"PATH=/bin:/usr/bin", "TERM=console", NULL};

    execl("/bin/ps", "ps", "-o", "pid,ppid,pgrp,session,tpgid,comm", NULL);
    execv("/bin/ps", ps_argv); 
    execle("/bin/ps", "ps", "-o", "pid,ppid,pgrp,session,tpgid,comm", NULL, ps_envp); 
    execve("/bin/ps", ps_argv, ps_envp);       
    execlp("ps", "ps", "-o", "pid,ppid,pgrp,session,tpgid,comm", NULL); execvp("ps", ps_argv);
 }
```

### 8.6 system 函数

```c
#include<stdlib.h>

//返回：成功返回执行命令的状态，出错返回-1
//简化exec函数的使用
int system(const char* command);

```

- system 函数内部构件一个子进程，由子进程调用exec函数
- 等同于 /bin/bash -c "cmd" 或者 exec（"bash"，"-c","cmd")：

### 8.7 函数调用和进程状态的关系
![](https://sailorlou.github.io/image/occ/funct_status_relationship.PNG)


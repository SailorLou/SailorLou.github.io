---
layout:     post
title:      "Linux 线程"
subtitle:   "Linux"
date:       2017-10-31 22:00:00
author:     "Sailor"
categories:  linux
mathjax: false
tags:
    -linux 并发编程
---

<!-- TOC -->

- [Linux 线程](#linux-线程)
    - [线程的定义](#线程的定义)
    - [线程分类](#线程分类)
    - [线程标识](#线程标识)
    - [线程的创建](#线程的创建)
    - [线程的终止](#线程的终止)
    - [线程的清理和控制函数](#线程的清理和控制函数)
    - [线程的状态的转换](#线程的状态的转换)
    - [线程属性的初始化和销毁](#线程属性的初始化和销毁)
    - [线程的同步和互斥](#线程的同步和互斥)
        - [概述](#概述)
        - [互斥锁](#互斥锁)


<!-- /TOC -->

## 线程的定义
- 进程是资源的管理的最小单元，线程是程序执行的最小单位
- 每一个进程有自己的数据段，代码段和堆栈段，线程通常叫做轻型的进程，它包含独立的栈和CPU寄存器状态，线程是进程的一条执行路径，每一个线程共享其所附属进程的所有的资源，包括打开的文件，内存页面，信号的标识及动态分配的内存等。
- 因为线程比进程要小，所以所占cpu资源会要小
- 从操作系统层面设计中，从进程演化出线程，最主要的目的就是更好地支持多处理器，并且减小进程上下文切换的开销.
- 当进程退出时，该进程所产生的线程都会被强制退出并清除，一个进程至少需要一个线程作为他的指令执行体，进程管理资源，如cpu，内存文件等，而奖线程分配到某一个cpu上执行.

## 线程分类
- 用户态
    - 解决上下文切换的问题，其调度过程由用户决定.
- 内核态
    - 由内核调度机制实现
- 两者关系：
    - 用户级线程要绑定内核级线程运行，一个进程中的内核级线程会分配到固定的时间片，用户级线程分配的时间片以内核级线程为准。
    - 默认情况下，用户级和内核级线程是一对一，也可以多对以
    - 当CPU 分配给线程的时间片用完后但线程没有执行完毕，此时线程会从运行状态返回就绪状态，将cpu让给其他线程。
- 通常pthread线程库实现的线程访问与控制，由POSIX提供，具有良好的可移植性。且注意linux 线程的程序需要gcc上链接库pthread。

## 线程标识
线程标识是pthread_t数据类型
```c
#include <pthread.h>
// 相等返回非0，否则返回0
int pthread_equal(pthread_t,pthread_t);
// 返回线程ID
pthread_t pthread_self(void);
```

## 线程的创建
```c
#include <pthread.h>
// 返回：成功返回0，出错返回错误编号
// 参数：
//     tidp:线程标识符指针
//     attr：线程属性指针
//     start_rtn：线程运行函数的起始地址
//     arg：传递给线程运行的函数的参数
int pthread_create
(
    pthread_t * tidp, 
    const pthread_attr_t *attr,
    void *(*start_rtn)(void *),
    void *arg
);

// Example
int err;
pthread_t newThread;
void func(void * input);
if(err = pthread_create(&newthread,NULL,func,(void*)100)
{
    perror("pthread created error \n");
}
    
```
注意：不能保证新线程和调用线程的执行顺序.
## 线程的终止

- 主动终止
    - 线程的执行函数中调用return 语句
    - 调用pthread_exit
- 被动终止
    - 线程可以被同一进城的其他线程取消，其它线程调用
      pthread_cancel(pthid).

```c
#include<pthread.h>
//线程可以被同一进程的其他线程取消，tid为被终止的线程标识符
int pthread_cancel(pthread_t tid);
/*
 * retval,调用者线程的返回值，可有其他函数和
 * pthread_join来检测获取
 *由于一个进程中多个线程共享数据段，因此通常在线程退
 *出后，退出线程所占有的资源并不会随线程结束而释放，
 *所有需要pthread_join 函数来等待线程结束，类似于
 *wait系统调用.
 *
**/
void pthread_exit(void *retval);
/*
 * th 被等待线程的标识符
* thread_return用户定义指针，用来储存被等待线程的返
* 回值
**/
int pthread_joint(pthread_t th,void**thread_return);
```

```c
// 
struct ResultArg
{
    int start;
    int end;
}

void* func(void * arg)
{
    ResultArg *r = (ResultArg*)arg;
    if(r==nullptr)
       return nullptr;
    
    printf("start = %d,end = %d\n",r->start，r->end)；

    // convert 70 to address
    return (void*)(r->start+r->end);//(void*)70
}

void main()
{
   int err;
   pthread_t newThread;
   ResultArg arg={20,50};

   if(err = pthread_create(&newthread,NULL,func,(void*)&arg) !=0)
    {
        perror("pthread created error \n");
    }
    
    // 主控线程调用pthread_joint(),自己会阻塞
    // 直到 newThread 线程 结束才能继续执行.
    int *result;
    pthread_join(newThread,(void**)&result);

    printf（"control thread ID ：%lx\n"，pthread_self());

   // result stands for value regarded as address
    printf("result = %d\n",(int)result);
}

```
## 线程的清理和控制函数
线程可以安排他退出时需要调用的函数，这与进程可以用atexit函数安排进程退出时需要调用的函数是类似的。这样的函数称为线程清理处理程序，线程可以建立多个清理处理程序。处理程序记录在栈中，也就是说他们的执行顺序与他们注册的顺序相反。，pthread_cleanup_push 和pthread_cleanup_pop需要成对出现.push 和pop 是栈的特性，先压栈再结束清理函数先执行。
```c
# include<pthread.h>
/*
   rth:清理函数指针
   arg：调用清理哈数传递的参数
**/
void pthread_cleanup_push(
    void(*rth)(void*)
    void *arg);
/*
   execute: 值为1时执行线程清理函数，
            值为0 时不执行线程清理函数
**/
void pthread_cleanup_pop(int execute)
```

触发线程清理函数的动作有
- 调用pthread_exit（注意：return不会触发，也就是线程是通过从它的启动历程中返回而终止的话，那么它的清理处理程序就不会被调用）
- 响应取消请求：pthread_cancel
- 用非零execute参数调用thread_cleanup_pop

```c
#include<pthread.h>
#include<stdlib.h>
#include<stdio.h>

//define the cleanup method
void clean_function(void *arg)
{
    char *input = (char*)arg;
    printf("clean function result = %s\n",input);
}

void * thread_processing_func(void *arg)
{
    int execute = (int)arg;
    pthread_cleanup_push(clean_function,"first clean function");
    pthread_cleanup_push(clean_function,"second clean function");

    printf("the id of thread running=%lx\n",pthread_self()));

    pthread_cleanup_pop(execute);
    pthread_cleanup_pop(execute);

    return nullptr;
}

int main(void)
{
    int error;
    pthread_t th1,th2;

    if(error = pthread_create(&th1,NULL, thread_processing_func,(void*)1)!=0)
    {
        perror("create thread incorrectly");
    }
    
    pthread_joint(th1,nullptr);

    printf("first thread(%lx )is finished\n",th1);

        if(error = pthread_create(&th2,NULL, thread_processing_func,(void*)1)!=0)
    {
        perror("create thread incorrectly");
    }
    
    pthread_joint(th2,nullptr);

    printf("second thread(%lx )is finished\n",th2);
}

```

## 线程的状态的转换
![](https://sailorlou.github.io/image/occ/pthread_state_conversion.PNG)

## 线程属性的初始化和销毁
以默认方式启动线程时，结束后不会自动释放占有的系统资源，要在主控线程中调用pthread_joint后才会释放.
```c
#include <pthread.h>
int pthread_att_init(pthread_attr_t *attr)

int pthread_attr_destory(pthread_attr *attr)

//其中pthread_addr_t为线程属性对象的结构类型，
//属性对象对于应用程序来说是透明的，pthread_addr_t的结构如下:
typedef struct
{
    int                   detachstate;      //线程的分离状态
    int                   schedpolicy;     //线程调度策略
    structsched_param     schedparam;      //线程的调度参数
    int                   inheritsched;    //线程的继承性
    int                   scope;           //线程的作用域
    size_t                guardsize;       //线程栈末尾的警戒缓冲区大小
    int                   stackaddr_set;   //线程的栈设置
    void*                 stackaddr;       //线程栈的位置
    size_t                stacksize;       //线程栈的大小
} pthread_attr_t;
```
在线程中在线程终止的时候有两种情况，第一种情况是，线程知道它的的终止信息有别人感兴趣，因此暂时不释放资源，等着别人来获取；第二种情况是，线程知道它的终止信息没有人感兴趣，因此它就不等了，直接在终止之后让内核将其资源全部释放掉。线程都是通过detachstate属性来判断其在终止时候该做什么，前一种状态称为可结合状态（joinable），后一种状态称为分离状态（detached），两种状态的detachstate取值分别为：PTHREAD_CREATE_JOINABLE和PTHREAD_CREATE_DETACHED。应用程序只能通过其提供的函数接口来访问其中的各个属性。

特别注意：如果detach的线程 用join 时则会出现错误

```c
#include <pthread.h>
int pthread_attr_setdetachstate(const pthread_attr_t *restrict attr, int detachstate);

int pthread_attr_getdetachstate(const pthread_attr_t attr, int *detachstate);

```

实例
```c

void output_state(pthread_attr_t *attr)
{
    int state;
    if(pthread_attr_getdetachstate(attr,&state)!=0)
    {
        perror("get state error\n");
    }
    else
    {
        if (state = PTHREAD_CREATE_JOINABLE)
        {
            prinft("this thread is joinable\n");
        }
        else
        {
            prinft("this thread is detachable\n");
        }
    }
}

void *thread_processing_func(void *arg)
{
    int i=1;
    int sum =0;
    while(i<=100)
    {
        sum += i;
    }

    return (void*)sum;
}

int main ()
{
    int error;
    pthread_t default_th,detach_th;
    if(error = pthread_create(&th1,NULL,   thread_processing_func,nullptr)!=0)
    {
        perror("create default thread incorrectly");
    }

    int result;
    if(error = pthread_join(defualt_th,void*(&result))!= 0)
    {
        perror("default thread join error\n");
    }
    else
    {
        printf("default return is %d\n",(int)res);
    }

    //define pthread attribute
    pthread_attr_t attr;

    pthread_attr_init(&attr);
    pthread_attr_setdetachstate(&attr,PTHREAD_CREATE_DETACHED);

    // output attribute state
    output_state(&attr);

    if(error = pthread_create(&detach_th,&attr,thread_processing_func,nullptr)!=0)
    {
        perror("detach thread create error\n");
    }
    
    //destroy thread attribute
    pthread_attr_destory(&attr);
}
```

## 线程的同步和互斥
### 概述
- 线程同步是一个宏观概念，在微观上包含线程的相互排斥和线程先后执行的约束问题，解决同步方式有：条件变量和线程信号量.
- 线程互斥就是线程执行相互排斥，解决互斥的方式有：
    - 互斥锁
    - 读写锁
    - 线程信号量

### 互斥锁
- 互斥锁是一种简单的方法控制对共享资源的访问，在同一时刻只能有一个线程掌握某个互斥锁，拥有上锁状态的线程能够对临界区进行访问，其他线程希望上锁一个已经被上了互斥锁的资源，则该线程挂起，直到上锁的线程释放互斥锁为止。
- 互斥锁数据类型是 pthread_mutex_t

```c
#include<pthread.h>
// return 0 if sucessful.
// mutexattr互斥锁创建方式
// 1.创建快速互斥锁： PTHREAD_MUTEX_INITIALIZER
// 2.创建递归互斥锁：PTHREAD_RECURSIVE_MUTEX_INITIALIZER 
// 3.创建检错互斥锁：PTHREAD_ERRORCHECK_MUTEX_INITIALIZER_NP
int pthread_mutex_init
(
    pthread_mutex_t *mutex,
    const pthread_mutex_attr_t *mutexattr
);
int pthread_mutex_destroy(pthread_mutex_t * mutex);

// 互斥锁上锁和解锁
#include <pthread.h>
// 上锁，拿不到锁就阻塞
int pthread_mutex_lock(pthread_mutex_t *mutex);
// 上锁，拿不到锁返回出错信息
int pthread_mutex_trylock(pthread_mutex_t *mutex);
// 释放锁，成功返回0，出错返回出错码
int pthread_mutex_unlock(pthreadd_mutex_t *mutex);

```
互斥锁实例-银行存取款
```c
// account info
 typedef struct 
 {
     int code;
     double balance;
     pthread_mutex_t mutex;
 } Account

 // create account
 extern Account * CreateAccount(int code,double balance)
 {
    Account *user = (Account*)malloc(sizeof(Account));
    assert(user != NULL);

    user->code = code;
    user->balance = balance;
    pthread_mutex_init(&user->mutex,nullptr);
 }

 // destroy account
 extern void DestroyAccount(Account *account)
 {
    if(account != nullptr)
    {
        pthread_mutex_destroy(&account->mutex);
        free account;
    }
 }

 // withdraw 
 extern doulbe withdraw(Account * user,double amt)
 {
     if(user == nullptr)
     return 0.0;

     pthread_mutex_lock(&user->mutex);

     if(amt <0 || amt>user->balance)
     {
         pthread_mutex_unlock(&user->mutex)
         return 0.0;
     }

     double balance = user->balance;
     balance -= amt;
     user->balance = balance;

     pthread_mutex_unlock(&user->mutex);

     return amt;
 }
 // deposit 
 extern double deposit(Account *user,double amt)
 {
     if(user == nullptr)
       return 0.0;

    pthread_mutex_lock(&user->mutex);
    if(amt<0)
    {
        pthread_mutex_unlock(&user->mutex);
        return 0.0;
    }

    double balance = user->balance;
    balance += amt;

    user->balance =balance

    pthread_mutex_unlock(&user->mutex);
    return amt;
 }

 extern double get_balance(Account *user)
 {
    if(user ==nullptr)
        return 0.0;

    pthread_mutex_lock(&user->mutex);
    double balance = a->balance;

    pthread_mutex_unlock(&user->mutex);
    return balance;
 }

 typedef struct{
     char UserName[20];
     Account *account;
     double amt;
 } operatorArg;

 void *thread_withdraw_func(void *arg)
 {
     operatorArg * oper = (operatorArg*)arg;
     double amt = withdraw(oper->account,oper->amt);

     // print something you want

     return nullptr;
 }

 int main(void)
 {
     int error;
     pthread_t boy,girl;

     Account *user = CreateAccount(100001,10000);
     operatorArg  boyArg girlArg;
     strcpy(boyArg.UserName,"boy");
     boyArg.account = user;
     boyArg.amt =10000;

     strcpy(girlArg.UserName,"girl");
     girlArg.account = user;
     girlArg.amt =10000;

    if(error = pthread_create(&boyArg,nullptr,thread_withdraw_func,(void*)boyArg)!=0)
    {
        perror("thread create error\n");
    }

    if(error = pthread_create(&girlArg,nullptr,thread_withdraw_func,(void*)girlArg)!=0)
    {
        perror("thread create error\n");
    }

     return 0;
 }
 ```

 ### 读写锁

 - 线程使用互斥锁 缺乏读并发性，当读操作较多，写操作较少时，可使用读写锁提高线程读并发性.
 - 读写锁可以有三种状态：读模式下加锁状态、写模式下加锁状态和不加锁状态。一次只有一个线程可以占有写模式的读写锁，但多个线程可以同时占有读模式的读写锁。
 - 读写锁在写加锁状态时，其他试图以写状态加锁的线程都会被阻塞。
 - 读写锁在读加锁状态时，如果有线程希望以写模式加锁时，必须阻塞，直到所有线程释放锁，
 - 读写锁在读加锁状态时，如果有线程希望以读模式加锁时，可以成功枷锁，
 - 当读写锁以读模式加锁时，如果有线程试图以写模式对其加锁，那么读写锁会阻塞随后的读模式锁请求，以避免读锁长期占用，而写锁得不到请求
 - 读和读不排斥，写与写或是读排斥
 - 读写锁的数据类型：pthread_rwlock_t

 ```c
 #include <pthread.h>

int pthread_rwlock_init(pthread_rwlock_t *restrict rwlock, const pthread_rwlockattr_t *restrict attr);
int pthread_rwlock_destroy(pthread_rwlock_t *rwlock);

// read lock
int pthread_rwlock_rdlock(pthread_rwlock_t *rwlock);
// write lock
int pthread_rwlock_wrlock(pthread_rwlock_t *rwlock);
int pthread_rwlock_unlock(pthread_rwlock_t *rwlock);

int pthread_rwlock_tryrdlock(pthread_rwlock_t *rwlock);
int pthread_rwlock_trywrlock(pthread_rwlock_t *rwlock);
 ```

 读写锁实例-银行存取款
 ```c
// account info
 typedef struct 
 {
     int code;
     double balance;
     //pthread_mutex_t mutex;

     //define read-write lock
     pthread_rwlock_t rwlock; 
 } Account

 // create account
 extern Account * CreateAccount(int code,double balance)
 {
    Account *user = (Account*)malloc(sizeof(Account));
    assert(user != NULL);

    user->code = code;
    user->balance = balance;
    //pthread_mutex_init(&user->mutex,nullptr);
    pthread_rwlock_init(&user->rwlock,nullptr);
 }

 // destroy account
 extern void DestroyAccount(Account *account)
 {
    if(account != nullptr)
    {
        //pthread_mutex_destroy(&account->mutex);
        pthread_rwlock_destroy(&account->rwlock);
        free account;
    }
 }

 // withdraw 
 extern doulbe withdraw(Account * user,double amt)
 {
     if(user == nullptr)
     return 0.0;

     //pthread_mutex_lock(&user->mutex);
     // add write lock
     pthread_rwlock_wrlock(&user->rwlock);

     if(amt <0 || amt>user->balance)
     {
         pthread_rwlock_unlock(&user->rwlock);
         //pthread_mutex_unlock(&user->mutex)
         return 0.0;
     }

     double balance = user->balance;
     balance -= amt;
     user->balance = balance;

     //pthread_mutex_unlock(&user->mutex);
     pthread_rwlock_unlock(&user->rwlock);
     return amt;
 }
 // deposit 
 extern double deposit(Account *user,double amt)
 {
     if(user == nullptr)
       return 0.0;

    //pthread_mutex_lock(&user->mutex);
    pthread_rwlock_wrlock(&user->rwlock);
    if(amt<0)
    {
        //pthread_mutex_unlock(&user->mutex);
        pthread_rwlock_unlock(&user->rwlock);
        return 0.0;
    }

    double balance = user->balance;
    balance += amt;

    user->balance =balance

    //pthread_mutex_unlock(&user->mutex);
    pthread_rwlock_unlock(&user->rwlock);
    return amt;
 }

 extern double get_balance(Account *user)
 {
    if(user ==nullptr)
        return 0.0;

    //pthread_mutex_lock(&user->mutex);
    //add read lock
    pthread_rwlock_rdlock(&user->rwlock);
    double balance = a->balance;

    //pthread_mutex_unlock(&user->mutex);
    pthread_rwlock_unlock(&user->rwlock);
    return balance;
 }

 typedef struct{
     char UserName[20];
     Account *account;
     double amt;
 } operatorArg;

 void *thread_withdraw_func(void *arg)
 {
     operatorArg * oper = (operatorArg*)arg;
     double amt = withdraw(oper->account,oper->amt);

     // print something you want

     return nullptr;
 }

 int main(void)
 {
     int error;
     pthread_t boy,girl;

     Account *user = CreateAccount(100001,10000);
     operatorArg  boyArg girlArg;
     strcpy(boyArg.UserName,"boy");
     boyArg.account = user;
     boyArg.amt =10000;

     strcpy(girlArg.UserName,"girl");
     girlArg.account = user;
     girlArg.amt =10000;

    if(error = pthread_create(&boyArg,nullptr,thread_withdraw_func,(void*)boyArg)!=0)
    {
        perror("thread create error\n");
    }

    if(error = pthread_create(&girlArg,nullptr,thread_withdraw_func,(void*)girlArg)!=0)
    {
        perror("thread create error\n");
    }

     return 0;
 }
 ```

 ### 线程同步之条件变量
 - 互斥锁的确定是只有两种状态：锁定和非锁定
 - 条件变量通过允许线程阻塞和等待另一个线程发送信号的方法弥补了互斥锁的不足.
 - 条件变量内部是一个等待队列，放置等待的线程，线程在条件变量上等待和通知，互斥锁用来保护等待队列（对等待队列上锁），条件变量通常和互斥锁一起使用.
 - 条件变量允许线程等待特定条件发生，当条件不满足时，线程通常先进入阻塞状态，等待条件发生变化，一但其他的某一个线程改变了条件，可唤醒一个或多个阻塞的线程。

 - 条件变量的原理实现  

 ```c

 class Condition
 {
     int numWaiting =0;
     WaitQueue q;
 }

Condition::Wait(lock)
{
    numWaiting ++;
    add this thread t to q;
    release(lock);
    schedule();//need mutex
    require(lock);
}

Condition::Signal()
{
    if(numwaiting>0)
    {
        remove a thread t from q;
        wakeup(t);//need mutex
        numwaiting--;
    }
}
//其中pthread_cond_wait函数内部流程可以理解为：
pthread_cond_wait(cond,mutex)
{
    1.unlock(mutex) 
    2.lock(mutex) 
    3.将线程自己插入到条件变量的等待队列中 
    4.unlock（mutex) 
    5.当前等待的线程阻塞《==等其他线程通知唤醒 
    6.在唤醒后，lock(&mutex)； 
    7.从等待队列中删除线程自己 
}

 ```

 - 条件变量的数据类型：pthread_cond_t

 - 条件变量的创建和销毁   

 ```c
 #include <pthread.h>

int pthread_cond_init(pthread_cond_t *cond, const pthread_condattr_t *attr);
int pthreead_cond_destroy(pthread_cond_t *cond);

 ```
- 条件变量的等待操作       

```c

// 线程由于调用wait 函数阻塞，否则释放互斥锁
int pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t * mutex);

int pthread_cond_timewait
(
    pthread_cond_t *cond,
    pthread_mutex_t *restrict mutex，
    const struct timespec *timeout
);

struct timespec 
{
    time_t tv_sec;// seconds
    long tv_nsec; //nanoseconds
}

```

- 条件变量的通知   

```c
#include<pthread.h>
/*
* 通知一个线程
* 该函数的参数为条件变量指针。该函数向队列第一个等待
* 线程发送信号，解除这个线程的阻塞状态
**/
int pthread_cond_signal(pthread_cond_t *cond);
/*
* 通知所有的线程
* 该函数的参数为条件变量指针。该函数向队列所有等待线
* 程发送信号，解除这些线程的阻塞状态
**/
int pthread_cond_broadcast(pthread_cond_t *cond);
```

- 条件变量的案列 one :一个线程计算结果，多个线程获取结果   

```c
#include<thread.h>
#include<stdio.h>
#include<stdlib.h>

#define COUNT_EXPECT (2);// 期望的数量

typedef struct
{
    int res;
    int counter;// 用于统计获取结果的线程数量
    pthread_cond_t cond;
    pthread_mutext_t mutex;
} Result;

// calculation method
void* calculation_func(void *arg)
{
    int i=1,sum=0;
    while(i<=100)
        sum += i;

    Result *res = (Result*)arg;
    r->res = sum;

    pthread_mutex_lock(&res->mutex);
    //判断获取结果的线程是否达到指定的数量
    while(res->counter < COUNT_LIMIT)
    {
        pthread_mutex_unlock(&res->mutex);
        usleep(100);
        pthread_mutex_lock(&res->mutex);
    }

    pthread_mutex_unlock(&res->mutex);

    pthread_cond_broadcast(&res->cond);

    // to get resutl and  out 

    int resnum = res->res;
    // print resunum;

    return nullptr;
}

// gain result of calcualion
void* ask_func(void *arg)
{
    Result *res = (Result*)arg;

    pthread_mutex_lock(&res->mutex);  
 
    res->counter++;

    pthread_cond_wait(&res->cond,&res->mutex)

    pthread_mutex_unlock(&res->mutex);
    return nullptr;
}

// entry method
int main()
{
    int error;
    pthread_t cal_th ,ask_th;

    Result res;
    res.counter = 0;
    pthread_cond_init(&res.cond,nullptr);
    pthread_mutex_init(&res.mutex,nullptr);

    if((error= pthread_create(&cal_th,nullptr,calculation_func,(void*)res))!=0 )
    {
        perrror("thread create error\n");
    }

    if((error = pthread_create(&ask_th,nullptr,ask_func,(void*)res))!=0)
    {
        perror("thread create error \n");
    }

    pthread_join(cal_th,nullptr);
    pthread_join(ask_th,nullptr);

    pthread_cond_destory(&res.cond);
    pthread_mutex_destory(&res.mutex);
}

```

- 条件变量的案列 two :
    - This simple example code demonstrates the use of several Pthread condition variable routines.
    - The main routine creates three threads.
    - Two of the threads perform work and update a "count" variable.
    - The third thread waits until the count variable reaches a specified value.
    - code could be found in the [example link][1].

[1]: https://computing.llnl.gov/tutorials/pthreads/#ConditionVariables

### 线程同步和互斥之线程信号量
#### 概述和原理探讨
- 信号量是操作系统提供的一种协调共享资源访问的方法
- 信号量从本质上是一个非负整数计数器，是共享资源的数目。
- 信号量可以实现线程的同步和互斥
- 线程的同步和互斥可以参考下图
![](https://sailorlou.github.io/image/occ/thread_schronize_methods.PGN)
- 信号是抽象的数据结构，信号量是被保护的整数变量
    - 由一个整数（sem）变量和两个原子操作组成；
    - P()（prolaag，荷兰语尝试减少），sem减一，sem<0进入等待，否则继续。所以P操作可能阻塞.
    - V()（verhoog，荷兰语增加），sem加一，如sem<=0 唤醒一个等待进程.所以V操作不会阻塞.
- 
- 信号量的实现伪代码
```c
class Semaphore
{
    int sem;
    WaitQueue q;
}

Semaphore::p()
{
    sem--;
    if(sem<0)
    {
        add this thread t to q;
        block(t);
    }
}

Semaphore::v()
{
    sem++;
    if(sem<=0)
    {
        remove a thread t from q;
        wakeup(t);
    }
}

// 访问临界区伪代码
mutex = new Semaphore(1);
mutex->P();
Critical Section;
mutex->V();

```
### 信号量的创建和销毁
```c
#include<semaphore.h>
int sem_init
(
    sem_t *sem, //信号量指针
    int pshared, //是否在进程间共享的标志，0不共享，1为共享
    unsigned int value // 初始值
);  

int sem_destroy (sem_t *__sem);
```
### 信号量的加减操作（P 和 V）
```c
#include<semaphore.h>
// 增加信号量的值，加1操作
int sem_post(sem_t *sem);
// 减小信号量的值，减1操作
int sem_wait(sem_t *sem);
//减小信号量的值，非阻塞
int sem_trywait(sem_t *sem);
```
当线程调用sem_wait()后，若信号量的值小于0则线程阻塞，只有当其他线程调用sem_post对信号量加1操作后并且其值大于或等于0时，阻塞的线程才能继续运行.

### 实例
启动进程的顺序是 A,B,C；
但执行的顺序是C->B->A：
```c
#include<semahore.h>

sem_t sem1,sem2;

void* a_fn(void *arg)
{
    sem_wait(&sem1);
    printf("thread a is running");
    return nullptr;
}

void* b_fn(void *arg)
{
    sem_wait(&sem2);
    printf("thread b is running");
    sem_post(&sem1);

    return nullptr;
}

void* c_fn(void *arg)
{
    // 最先是不阻塞，先执行print函数
    printf("thread c is running");
    // 加一操作，让阻塞的线程b 继续运行
    sem_post(&sem2);
    return nullptr;
}

int main()
{
    pthread_t a_th,b_th,c_th;
    sem_init(&sem1,0,0);
    sem_init(&sem2,0,0);

    pthread_create(&a_th,nullptr,a_fn,nullptr);
    pthread_create(&b_th,nullptr,b_fn,nullptr);
    pthread_create(&c_th,nullptr,c_fn,nullptr);

    pthread_join(a,NULL);
    pthread_join(b,NULL);
    pthread_join(c,NULL);

    sem_destory(&sem1);
    sem_destory(&sem2);
}
```

## 线程同步问题经典实例
### 生产者消费者问题
#### 问题描述
实际上主要是包含了两类线程，一种是生产者线程用于生产数据，另一种是消费者线程用于消费数据，为了解耦生产者和消费者的关系，通常会采用共享的数据区域，就像是一个仓库，生产者生产数据之后直接放置在共享数据区中，并不需要关心消费者的行为；而消费者只需要从共享数据区中去获取数据，就不再需要关心生产者的行为。但是，这个共享数据区域中应该具备这样的线程间并发协作的功能：
- 如果共享数据区已满的话，阻塞生产者继续生产数据放置入内；
- 如果共享数据区为空的话，阻塞消费者继续消费数据；

#### 有界缓冲区的生产者-消费者
##### 问题描述
- 一个或多个生产者在生成数据后放在一个缓冲区里
- 单个消费者从缓冲区取出数据处理
- 任何时刻只能有一个生产者或消费者可访问缓冲区
##### 问题分析
- 任何时刻只能有一个生产者或消费者可访问缓冲区（互斥访问）
- 缓冲区空时，消费者必须等待生产者（条件同步）
- 缓冲区满时，生产者必须等待消费者（条件同步）
##### 用信号量 描述每一个约束
- 二进制信号量mutex
- 资源信号量 fullBuffers
- 资源信号量 emptyBuffers
##### 信号量伪代码实现
```c
class BoundedBuffer
{
    mutex = new Semaphore(1);
    fullBuffers= new Semaphore(0);
    emptyBuffers = new Semaphore(n);
}

BoundedBuffer::Deposite(c)
{
    emptyBuffers->P();
    mutex->P();
    add c to the buffer;
    mutex->V();
    fullBuffers->V();
}

BoundedBuffer::remove(c)
{
    fullBuffers->P();
    mutex->P();
    remove c to the buffer;
    mutex->V();
    emptyBuffers->V();
}

```

##### 条件变量伪代码实现
```c
class BoundedBuffer
{
    Lock lock；
    int count =0；
    Condition notFull, notEmpty；
}

BoundedBuffer::Deposite(c)
{
    lock->Acquire();
    while(count == n)
        notFull.wait(&lock);
    add c to the buffer;
    Count++;
    notEmpty.Signal();

    lock->Release();
}

BoundedBuffer::remove(c)
{
    lock->Acquire();
    while(count == 0)
        notEmpty.wait(&lock);
    remove c to the buffer;
    Count--;
    notFull.Signal();
    lock->Release();
}

```

### 读者写者问题
#### 问题描述：
读者写者问题描述的是这么一种情况：对象在多个线程（或者进程）之间共享，其中一些线程只会读数据，另外一些线程只会写数据。为了保证写入和读取的正确性，我们需要保证，只要有线程在写，那么其他线程不能读，否则可能读到写了一半的数据；另外，也不能有两个线程同时写，否则导致数据错乱。当然，多个线程是可以同时读数据。

　　读者写者问题在计算机领域非常普遍，它为数据库访问建立了一个模型，数据库的读写分离也是为了减少因为读者写者问题加锁带来的对并发的影响。
读者写者问题的解决方案一般都有两种不同的侧重：读者优先或者写者优先。简单来说，读者优化就是尽量满足并发的读操作，当已经有线程在读数据的时候，其他读线程无需等待，而写线程需要等待所有正在进行的读操作之后才能执行。写者优先就是尽量满足写操作，尽管写操作不能并发，但是可以排队，优先于等待的读线程获得执行权。

#### 实例1：一个写者，一个读者。
写者写完后需要通知读者，读者读完后需要通知写者。读者和写者是双向同步.

```c  
#include<pthread.h>
#include<string.h>
#include<stdio.h>
#include<stdlib.h>


typedef struct
{
    int value;
    pthread_cond_t rc;
    pthread_mutex_t rm;
    int r_wait;

    pthread_cond_t wc;
    pthread_mutex_t wm;
    int w_wait;
}Storage;

void set_data(Storage *data,int value)
{
    data->value = value;
}

int get_data(Storage *data,int value)
{
   return data->value ;
}

//
void* reader_thread_handler(void *arg)
{
    Storage *share_data =(Storage*)arg;

    pthread_mutex_lock(&share_data->rm);
    share_data->r_wait =1;
    pthread_cond_wait(&share_data->rc,&share_data->rm);
    pthread_mutex_unlock(&share_data->rm); 

    int value = get_data(share_data); 

    //判断写者线程是否准备好
    pthread_mutex_lock(&share_data->wm);

    while(!share_data->w_wait)
    {
        pthread_mutex_unlock(&share_data->wm);
        sleep(1);
        pthread_mutex_lock(&share_data->wm);

    } 
   
    share_data->w_wait = 0;
    pthread_mutex_unlock(&share_data->wm);
    pthread_cond_broadcast(&share_data->wc);

    return nullptr;
}

void* writer_thread_handler(void *arg)
{
    Storage *share_data =(Storage*)arg;
    set_data(share_data,100);

    pthread_mutex_lock(&share_data->rm);
    while(!s->r_wait)
    {
        pthread_mutex_unlock(&share_data->rm);
        sleep(1);
        pthread_mutex_lock(&share_data->rm);
    }

    share_data->r_wait = 0;

    pthread_mutex_unlock(&share_data->rm);
    pthread_cond_broadcast(&share_data->rc);

    //写者线程等待阻塞
    //等待读者线程读取玩数据后通知唤醒它
    // 然后继续写入数据

    pthread_mutex_lock(&share_data->wm);
    s->w_wait = 1;
    pthread_cond_wait(&share_data->wc,&share_data->wm);
    pthread_mutex_unlock(&share_data->wm);
    
    return nullptr;    
}

int main()
{
    int error;

    pthread_t reader_th,write_th;

    Storage share_data;
    share_data.r_wait =0;
    share-data.w_wait =0;
    
    pthread_mutex_init(&share_data.rm,NULL);
    pthread_mutex_init(&share_data.Wm,NULL);
    pthread_cond_init(&share_data.rc,NULL);
    pthread_cond_init(&share_data.wc,NULL);


    if((error = pthread_create(&reader_th,NULL,reader_thread_handler,(void *)&share_data))!=0)
    {
        perror("thread create error\n");
    }

    if((error = pthread_create(&writer_th,NULL,writer_thread_handler,(void *)&share_data))!=0)
    {
        perror("thread create error\n");
    }

    pthread_joint(reader_th,NULL);
    pthread_joint(writer_th,NULL);

    pthread_mutex_destory(&share_data.rm);
    pthread_mutex_destory(&share_data.Wm);
    pthread_cond_destory(&share_data.rc);
    pthread_cond_destory(&share_data.wc);
    return 0;  
}
```
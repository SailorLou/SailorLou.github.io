---
layout:     post
title:      "linux 文件和目录总结"
subtitle:   "linux 基础学习"
date:       2018-10-31 22:00:00
author:     "Sailor"
header-img: ""
tags:
    linux
---
## 1.0 Linux 目录配置
### 1.1 Filesystem Hierarchy Standard (FHS)标准是linux目录配置的依据：
![](https://sailorlou.github.io/image/linux/linux-fhs-dir-definition.png)

- 可分享的：可以分享给其他系统挂载使用的目录，所以包括执行文件与用户的邮件等数据， 是能够分享给网络上其他主机挂载用的目录；

- 不可分享的：自己机器上面运作的装置文件或者是与程序有关的socket文件等， 由于仅与自身机器有关，所以当然就不适合分享给其他主机了。

- 不变的：有些数据是不会经常变动的，跟随着distribution而不变动。 例如函式库、文件说明文件、系统管理员所管理的主机服务配置文件等等；

- 可变动的：经常改变的数据，例如登录文件、一般用户可自行收受的新闻组等。
### 1.2 根目录 (/) 的意义与内容
根目录是整个系统最重要的一个目录，因为不但所有的目录都是由根目录衍生出来的， 同时根目录也与开机/还原/系统修复等动作有关。 由于系统开机时需要特定的开机软件、核心文件、开机所需程序、 函式库等等文件数据，若系统出现错误时，根目录也必须要包含有能够修复文件系统的程序才行。 因为根目录是这么的重要，所以在FHS的要求方面，他希望根目录不要放在非常大的分割槽内， 因为越大的分割槽妳会放入越多的数据，如此一来根目录所在分割槽就可能会有较多发生错误的机会。

因此FHS标准建议：根目录(/)所在分割槽应该越小越好， 且应用程序所安装的软件最好不要与根目录放在同一个分割槽内，保持根目录越小越好。 如此不但效能较佳，根目录所在的文件系统也较不容易发生问题
![](https://sailorlou.github.io/image/linux/linux-root-dir-def.png)
![](https://sailorlou.github.io/image/linux/linux-other-dir.png)
### 1.3 /usr 的意义与内容：
据FHS的基本定义，/usr里面放置的数据属于可分享的与不可变动的(shareable, static)， 如果你知道如何透过网络进行分割槽的挂载(例如在服务器篇会谈到的NFS服务器)，那么/usr确实可以分享给局域网络内的其他主机来使用
![](https://sailorlou.github.io/image/linux/linux-usr-dir-def.png)

### 1.4 /var 的意义与内容
如果/usr是安装时会占用较大硬盘容量的目录，那么/var就是在系统运作后才会渐渐占用硬盘容量的目录。 因为/var目录主要针对常态性变动的文件，包括缓存(cache)、登录档(log file)以及某些软件运作所产生的文件， 包括程序文件(lock file, run file)，或者例如MySQL数据库的文件等等

![](https://sailorlou.github.io/image/linux/linux-var-dir-def.png)
### 1.5 目录树
在Linux底下，所有的文件与目录都是由根目录开始的，然后再一个一个的分支下来，有点像是树枝状
![](https://sailorlou.github.io/image/linux/linux-dir-tree-def.png)
## 2 Linux 文件权限
敲以下命令可以列出文件信息：

`$ ls -al`

drwxr-xr-x  3 sailor sailor 4096 11月  4 09:59 image

这是我用思维导图对linux文件权限的相关总结：
![](https://sailorlou.github.io/image/linux/linux-file-permission.png)

## 3 文件和目录管理的相关命令
![](https://sailorlou.github.io/image/linux/linux-file-dir-commands.png)





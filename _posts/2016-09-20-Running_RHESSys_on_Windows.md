---
layout: post
title: "Compiling, Installing, and Running RHESSys on Windows using MinGW"
category: [RHESSys]
tag: [RHESSys, MinGW, CLion]
date: 2016-09-20 22:00:00
comments: true
---

* TOC
{:toc}

# RHESSys在Windows的编译、安装和运行 (利用MinGW)

--------------

# 1 动机及想法

[RHESSys](http://fiesta.bren.ucsb.edu/~rhessys/index.html)模型是基于GIS的水文生态模型，可用于水、碳、氮的模拟。

模型采用C语言开发，并与[GRASS GIS](https://grass.osgeo.org/)结合，可在macOS和Linux系统下运行，而我们想在自己熟悉的Windows上使用，该怎么做呢？

我们已经知道，RHESSys代码是通过[makefile](https://www.gnu.org/software/make/)管理，[GCC](https://gcc.gnu.org/)编译的，为了避免不同编译器（比如Windows下的Visual Studio）带来的不兼容，如果Windows下能使用GCC就好了。

于是，我想到了[MinGW](http://www.mingw.org/)，以及大名鼎鼎JetBrains公司出品的跨平台C/C++集成开发环境：[CLion](https://www.jetbrains.com/clion/)！

<!-- more -->

# 2 说干就干

## 2.1 假设我们已经了解这些知识

+ make命令速览，可参考阮一峰的教程[^ref1]
+ Window下的类似UNIX命令，可学习这篇“Windows for UNIX Users”[^ref2]

## 2.2 环境搭建

+ GRASS GIS 6.4.4, Windows下使用MinGW编译[^ref3]
+ GnuWin32-sed：文本替换工具，[下载地址](https://sourceforge.net/projects/gnuwin32/)

	+ 但是Windows下仍未解决替换文件中变量的问题，即
	`sed -e "s/<<RHESSYS_VERSION>>/$(VERSION)/g" ./main.c`
	意为将`./main.c`中的`const char RHESSYS_VERSION[] = "5.20.1";`
	替换为`const char RHESSYS_VERSION[] = "$(VERSION)";`


## 2.3 遇到的问题

### 2.3.1 适配makefile

+ CLion目前仅支持CMakeLists.txt管理项目，所以我们需要给RHESSys加上一个CMakeLists.txt，使CLion能够通过CMakeLists执行makefile[^ref4]
+ makefile中如何判断文件或文件夹是否存在[^ref5]
+ makefile: 4:  missing separator. Stop. 原因很简单，makefile缩进只能用Tab键，不能用4个或8个空格！
+ Make error: make (e=2): The system cannot find the file specified. 原因是这行语句的命令在Windows下找不到，比如`OS := $(shell uname)`,Windows是没有`uname`命令的，但是OS本身就是Windows的关键字，所以可以直接用`ifneq ($(OS), Windows_NT)` [^ref6]

### 2.3.2 RHESSys源码

+ MinGW中没有strtok_r定义，位置`construct_ascii_grid.c`[^ref7]

[^ref1]: http://www.ruanyifeng.com/blog/2015/02/make.html
[^ref2]: http://www.covingtoninnovations.com/mc/winforunix.html
[^ref3]: https://trac.osgeo.org/grass/wiki/CompileOnWindows
[^ref4]: http://stackoverflow.com/questions/26918459/using-local-makefile-for-clion-instead-of-cmake
[^ref5]: http://blog.csdn.net/qiaoliang328/article/details/7568141
[^ref6]: http://stackoverflow.com/questions/714100/os-detecting-makefile
[^ref7]: http://stackoverflow.com/questions/12975022/strtok-r-for-mingw

## 3 一步步来做




# Reference
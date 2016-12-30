---
layout: post
title: "Using SWIG to Wrap C/C++ libraries for Python "
category: [Python]
tag: [Python, SWIG, Toturial]
date: 2016-04-24 20:00:00
comments: true
---

* TOC
{:toc}

# 利用SWIG封装C/C++库供Python调用
------

# Questions

+ 如何利用SWIG封装C/C++代码供Python调用？
+ 如何分别生成适用于x64、x86位Python版本的库？
+ 如何封装C/C++版MPI并行程序？

<!-- more -->

# 1.Hello World

```cmake
CMakeList.txt
cmake_minimum_required(VERSION 2.8)
SET(CMAKE_BUILD_TYPE "Release")
FIND_PACKAGE(SWIG REQUIRED)
INCLUDE(${SWIG_USE_FILE})
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})
## SET Python and mpi4py
FIND_PACKAGE(PythonLibs)
SET(PYTHON_EXECUTE ${PYTHON_INCLUDE_PATH}/../python)
EXECUTE_PROCESS(COMMAND ${PYTHON_EXECUTE} -c "import mpi4py; print(mpi4py.get_include())"
                OUTPUT_VARIABLE MPI4PY_INCLUDE_PATH)
INCLUDE_DIRECTORIES(${PYTHON_INCLUDE_PATH})
INCLUDE_DIRECTORIES(${MPI4PY_INCLUDE_PATH})
## END SETTING python and mpi4py
## SET MPI
if(WIN32)
   #For MS-MPI in Windows, version v6 and above.
   set(MPIEXEC "C:/Program Files/Microsoft MPI/Bin/mpiexec.exe")
   # For building MPI programs the selected Visual Studio compiler is used, namely cl.exe.
   # So there is no need to set a specific MPI compiler.
   set(MPI_CXX_INCLUDE_PATH "C:/Program Files (x86)/Microsoft SDKs/MPI/Include")
   # Make sure the correct libraries (64-bit or 32-bit) are selected.
   # Decide between 32-bit and 64-bit libraries for Microsoft's MPI
   if("${CMAKE_SIZEOF_VOID_P}" EQUAL 8)
     set(MS_MPI_ARCH_DIR x64)
   else()
     set(MS_MPI_ARCH_DIR x86)
   endif()
   set(MPI_CXX_LIBRARIES "C:/Program Files (x86)/Microsoft SDKs/MPI/Lib/${MS_MPI_ARCH_DIR}/msmpi.lib")
   set(MPI_C_INCLUDE_PATH "${MPI_CXX_INCLUDE_PATH}")
   set(MPI_C_LIBRARIES "${MPI_CXX_LIBRARIES}")
   set(MPIEXEC_NUMPROC_FLAG "-np" CACHE STRING "Flag used by MPI to specify the number of processes for MPIEXEC; the next option will be the number of processes.")
else()
    find_package(MPI REQUIRED)
    #set(MPI_CXX_COMPILER "${CMAKE_CXX_COMPILER}")
endif()
INCLUDE_DIRECTORIES(${MPI_C_INCLUDE_PATH})
## END SETTING MPI
SET(CMAKE_SWIG_FLAGS "-Wall")

SET_SOURCE_FILES_PROPERTIES(helloworld.i PROPERTIES CPLUSPLUS ON)
SET_SOURCE_FILES_PROPERTIES(helloworld.i PROPERTIES SWIG_FLAGS "-builtin")

SWIG_ADD_MODULE(helloworld python helloworld.i helloworld.c)
SWIG_LINK_LIBRARIES(helloworld ${PYTHON_LIBRARIES} ${MPI_C_LIBRARIES})
```

```
mkdir build64 & pushd build64
cmake -G "Visual Studio 10 2010 Win64" e:\test\swigpy
cd ..
cmake --build build64 --config Release

mkdir build32 & pushd build32
cmake -G "Visual Studio 10 2010" e:\test\swigpy
cd ..
cmake --build build32 --config Release
```


+ ERROR1: 提示LINK错误，找不到`python27_d.lib`; 参考[Stackoverflow上的一个回答](http://stackoverflow.com/questions/13766785/python-interpreter-for-c)，发现`Python`默认安装包并不带有`Debug`模式的链接库，即`python27_d.lib`，因此，改为`Release`编译即可解决。



# Reference

http://stackoverflow.com/questions/17043088/cmake-build-calling-swig-with-multiple-arguments

https://cmake.org/pipermail/cmake/2015-March/059981.html

https://cmake.org/cmake/help/v3.0/command/execute_process.html?highlight=execute_process
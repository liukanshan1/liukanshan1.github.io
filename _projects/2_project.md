---
layout: page
title: 矩阵乘法的GPU实现和优化
description: 使用Block分块法基于cuda实现，与MKL和cublas对比性能
img: https://github.com/liukanshan1/GEMM/raw/main/benchmark.png?raw=true
importance: 3
category: work
giscus_comments: false
---

### 简介

#### [项目地址](https://github.com/liukanshan1/GEMM/)

使用Block分块法基于cuda实现，与MKL和cublas对比性能

实现了一个矩阵乘法的CUDA内核，采用了分块策略和共享内存来优化性能。每个线程块负责计算一个小的子矩阵的乘法，通过共享内存减少了全局内存的访问，从而提高了性能。为了在多个线程之间共享数据，我们定义了两个共享内存数组 sa和 sb，分别用于存储矩阵 A 和 B 的片段。

定义变量 btemp 用于临时存储矩阵 B 中某一行的数据。在计算 Cres 数组时，需要将 btemp 与 sa 中对应行的数据相乘，然后将结果累加到 Cres 数组的对应位置。这里的 btemp 是一个临时变量，仅在当前内循环中使用，用于存储 B 中某一行的数据，以便在计算过程中能够快速地访问和处理。在计算完一个内循环之后，btemp 的值会被更新，以存储下一行的数据。

使用 syncthreads 同步：在每次迭代之后，使用__syncthreads() 函数来同步所有线程的计算结果。这确保了在接下来的计算中，所有线程都使用相同的共享内存值。

子矩阵的规模为4*4。线程块大小设定为32*32。计算权重设为1，偏置设为0。

### 性能对比

计算相同场景下，不同内核GEMM的计算效率。

控制K值不变，调整M、N的大小，比较相同场景下各方案的运行效率。其中：

option_basic为基础计算策略;

option_1为保证了计算正确率的分块方案，运行效率低于基础策略，原因是过多的边界判断条件使得GPU分块处理困难，已舍弃；

option_2为4*4分块策略；

option_cublas为使用官方cublas库的计算策略。

曲线越接近x轴，同场景下运行效率越高。

![图表, 折线图  描述已自动生成](https://github.com/liukanshan1/GEMM/blob/main/benchmark.png?raw=true)

可以发现，4*4分块策略与cublas在矩阵规模达到500~600时，计算效率逼近cublas，但在矩阵规模较大时，与cublas的差距逐渐增大。

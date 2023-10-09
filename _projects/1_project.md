---
layout: page
title: 灰度图像分割/压缩的STC和RNAM算法实现
description: C++课程大作业
img: assets/img/pj1.png
importance: 3
category: work
related_publications: zyp2010
---

# 灰度图像压缩STC和RNAM算法

​		传统的图像表示方法主要是二维数组（矩阵）表示方法。但由于图像处理在各方面的广泛应用和其它学科的飞速发展,对图像处理的研究提出了处理速度和存贮空间的更高要求。这样,二维数组的空间效率及其“样点—样点”的运算方式已不能适应发展的需要,取而代之的应该是既紧凑又便于做各种图像处理运算的表示方法。本项目提供灰度图像压缩的STC和RNAM算法，RNAM算法采用的是对角线优先的策略。RNAM算法和STC算法编码时间复杂度为 O(nlogn)，解码时间复杂度为 O(n)。

[项目地址]: https://github.com/SCUT-CS/STC-RNAM

## 内容列表

- [安装](#安装)
- [使用说明](#使用说明)
- [项目结构](#项目结构)
- [维护者](#维护者)
- [贡献者](#贡献者)
- [如何贡献](#如何贡献)
- [使用许可](#使用许可)
- [参考文献](#参考文献)

## 安装

[Github Release](https://github.com/SCUT-CS/Project-1/releases)

## 使用说明

<img src="https://github.com/SCUT-CS/Project-1/blob/main/Image/%E7%A8%8B%E5%BA%8F%E8%BF%90%E8%A1%8C%E7%A4%BA%E6%84%8F%E5%9B%BE.png?raw=true" alt="程序运行示意图" style="zoom: 50%;" />

1. 打开图片：菜单栏—打开图片或在点击界面上半部分的打开图像按钮。

2. 编辑参数：在程序界面上半部分输入算法需要的参数。

3. 点击主界面上的Start按钮，算法即开始执行。

4. 算法执行完毕后在主界面的下半部分查看结果。

5. 保存图片：菜单栏—保存图片或点击界面下半部分的保存按钮。

## 项目结构

- 图形化界面采用Qt开发，算法实现采用C++和OpenCV 4，测试采用Google Test。

- 项目文件采用Visual Studio 2022

- 类结构：

  1. Class TreeNode：树的节点，存储树的数据结构。

  2. Class Tree：内部包含树的数据结构类TreeNode，存储树的数据结构。Tree类主要提供树的方法，例如addChild、levelOrder等。

  3. Class TreeIterator: 树的迭代器。

  4. Class Algo：实现算法流程。

  5. Class Calculate：计算算法执行结果。

  6. Class DPInterface：RNAM的算法接口，图形化界面通过调用此类下面的静态函数来调用RNAM算法。

  7. Class STCInterface：STC算法接口，使用方法同上。

  8. Class Segment：存储分割有关数据，提供区域分割方法。

  9. Class Variables：存储STC算法执行时需要的输入信息，提供一部分内联函数。
  10. Class AlgoThread：创建新线程，执行算法。
  11. Class Window：图形化界面。

- 程序流程：

  ![程序流程图](https://github.com/SCUT-CS/Project-1/blob/main/Image/%E7%A8%8B%E5%BA%8F%E6%B5%81%E7%A8%8B.png?raw=true)

## 维护者

[@liukanshan1](https://github.com/liukanshan1)

## 贡献者

感谢以下参与项目的人：

[@Palettone](https://github.com/Palettone)

[@kkxx0v0](https://github.com/kkxx0v0)

[@haohao1103](https://github.com/haohao1103)

## 如何贡献

非常欢迎你的加入！[提一个 Issue](https://github.com/SCUT-CS/Project-1/issues) 或者提交一个 Pull Request。

本项目遵循 [Contributor Covenant](http://contributor-covenant.org/version/1/3/0/) 行为规范。


## 使用许可

[MIT License](licence) © SCUT-CS/team-11

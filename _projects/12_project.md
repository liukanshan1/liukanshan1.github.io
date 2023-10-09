---
layout: page
title: 隐私保护的蚁群优化算法
description: Private-ACO 一个同态密文蚁群优化算法和明文蚁群优化算法
img: https://github.com/liukanshan1/Private-ACO/blob/main/results/berlin52-path-2.png?raw=true
importance: 3
category: work
related_publications:
---

### [项目地址](https://github.com/liukanshan1/Private-ACO)

### 基于加法秘密分享同态方案的隐私保护蚁群优化算法。
包含一个明文蚁群优化算法和密文蚁群优化算法。

设计了多线程版本并行优化，提高了密态蚁群优化的速度。

### 测试
使用TSP问题测试密态蚁群优化算法。

经过测试，密态算法的结果与明文算法一致，证明了密态算法仍具有高可用性，同时保护了需要优化的问题和数据不泄露给第三方。

![result](https://github.com/liukanshan1/Private-ACO/blob/main/results/berlin52-path-2.png?raw=true)

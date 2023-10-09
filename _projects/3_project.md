---
layout: page
title: PrivateLocation
description: 基于Paillier同态加密实现的经纬度坐标的转换、加密、以及距离计算
img: 
importance: 3
category: work
related_publications:
---

### [项目地址](https://github.com/liukanshan1/PrivateLocation)

- 默认使用国标的经纬度标准（火星坐标）
- 表示算法：使用坐标系映射，将经纬度转换为xyz空间坐标系
- 加密算法：预计算坐标平方后加密（预处理减少距离计算时间）
- 距离算法：在空间坐标系中计算
  - 避免的椭球上长度计算的sin和cos等操作（Paillier同态加密不支持/速度慢）
  - 大部分计算在本地完成，同时使用预计算加快速度
  - 计算简单，误差较低

---
layout: page
title: 联邦学习ECG心电图诊断模型训练与预测
description: 采用明文模型平均 Python
img: assets/img/xdt.jpg
importance: 3
category: work
related_publications: ribeiro2020automatic
---

采用明文模型平均，模型结构和部分参考论文Automatic diagnosis of the 12-lead ECG using a deep neural network及其开源实现

### [项目地址](https://github.com/liukanshan1/Fed-Avg)

### Scripts

- `train.py`: Script for training the neural network. To train the neural network run:

```
$ python train.py .\data\ .\data\
```



- `predict.py`: Script for generating the neural network predictions on a given dataset.

```
$ python predict.py .\data\test_set\ .\final_model.hdf5
```



- `generate_figures_and_tables.py`: Generate results.

```
$ python generate_results.py
```


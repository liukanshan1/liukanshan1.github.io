---
layout: page
title: 在线购物网站
description: 基于SpringBoot实现的购物网站
img: assets/img/image12.png
importance: 3
category: work
---

网络应用开发大作业

#### [项目地址](https://github.com/liukanshan1/ShoppingWebsite)

## 系统设计

###  后端架构

后端采用SpringBoot框架开发，基于RESTful规范设计接口，前后端数据交换采用JSON的形式。整体后端架构分为控制层、服务层、数据层实现。  其中数据层采用Mybatis简化开发，连接MySQL数据库，数据库的表结构如下所示：

 ![img](https://github.com/liukanshan1/ShoppingWebsite/blob/main/img/1.png?raw=true)  

根据表结构，设计实体类如下：

  ![img](https://github.com/liukanshan1/ShoppingWebsite/blob/main/img/2.png?raw=true)  

邮件模块采用SpringBoot框架提供的邮件支持，使用Outlook邮箱。 

### 前端架构

前端采用Vue框架进行开发，使用AXIOS发送HTTP请求，使用ElementUI和CSS美化界面实现，

## 代码实现

数据库事务控制：  在关键操作中，启用数据库事物管理增强一致性：  当用户余额不足时，自动回滚修改。

数据层Mapper实现  数据层Mapper继承BaseMapper<T>类，将SQL语句放至xml文件中。

## 系统展示

### 登录及注册

注册用户user11

![img](assets/img/image12.png) 

### 购物车界面

![img](assets/img/image14.png)

### 完成订单

打开邮箱，收到订单邮件，点击链接完成订单：

![img](assets/img/image18.png)

### 商品管理界面

![img](assets/img/image19.png)

### 数据报表界面

![img](assets/img/image20.png)

![img](assets/img/image21.png)

![img](assets/img/image22.png)

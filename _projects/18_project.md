---
layout: page
title: FileSystem
description: 模拟文件系统和Shell，使用Boost 多进程和日志框架。支持多Shell进程同时发送指令而不发生竞争和死锁。
img: https://github.com/liukanshan1/FileSystem/blob/master/img/3.png?raw=true
importance: 3
category: work
related_publications:
---

### [项目地址](https://github.com/liukanshan1/FileSystem)

# 一、 系统结构讲解

## 1.1 系统依赖和构建

采用C++ Boost库依赖辅助完成了以下功能：

l 使用Boost String的分割功能，用于Shell分析输入的命令

l 使用Boost Interprocess的共享内存和信号量的功能，一方面简化了系统的实现，使代码具有更高的可读性，同时支持跨平台部署，不仅仅局限于Linux平台。

l 使用Boost Log模块完成file system的日志格式化输出和记录

采用Cmake方式管理和构建系统

l 支持在IDE中多开shell进程，便于测试多个shell登录的场景

## 1.2 系统数据结构

### 1.2.1 IPC进程传递结构

| **类名** | **成员变量** | **类型**   | **说明**                         |
| -------- | ------------ | ---------- | -------------------------------- |
| Message  | userId       | int        | 命令的发送者的用户ID             |
| Message  | message      | char[1024] | 命令的内容和回复，最多1024个字符 |

### 1.2.2 Inode结构

| **类名** | **成员变量**    | **类型**         | **说明**                                             |
| -------- | --------------- | ---------------- | ---------------------------------------------------- |
| Inode    | type            | int              | 文件类型，0表示目录，1表示文件                       |
| Inode    | name            | std::string      | 文件名                                               |
| Inode    | size            | unsigned int     | 文件大小，以字节为单位                               |
| Inode    | block_seq       | unsigned int     | 文件所在的磁盘块号                                   |
| Inode    | num_of_children | unsigned int     | 文件的子目录或者子文件的数量，初始值为0              |
| Inode    | parent          | unsigned int     | 文件的父目录的磁盘块号                               |
| Inode    | children        | unsigned int[10] | 文件的子目录或者子文件的磁盘块号，最多10个           |
| Inode    | mode            | unsigned int[3]  | 文件的权限，分别表示用户、组和其他的读、写、执行权限 |
| Inode    | creator         | std::string      | 文件的创建者                                         |
| Inode    | last_modified   | std::string      | 文件的最后修改时间                                   |

### 1.2.3 目录结构

| **类名** | **成员变量** | **类型**                    | **说明**                                        |
| -------- | ------------ | --------------------------- | ----------------------------------------------- |
| Dir      | inode        | Inode*                      | 目录对应的inode指针，初始值为nullptr            |
| Dir      | name         | std::string                 | 目录名                                          |
| Dir      | parent       | Dir*                        | 父目录指针，初始值为nullptr                     |
| Dir      | files        | std::map<std::string, Dir*> | 子目录或者子文件的映射，键为文件名，值为Dir指针 |

### 1.2.4 用户登录信息

| **类名** | **成员变量** | **类型** | **说明**                                  |
| -------- | ------------ | -------- | ----------------------------------------- |
| User     | userId       | int      | 用户的ID                                  |
| User     | vcmd         | bool*    | 指向一个布尔值的指针，表示是否有命令输入  |
| User     | cmd          | Message* | 指向一个Message结构的指针，表示命令的内容 |
| User     | resp         | Message* | 指向一个Message结构的指针，表示命令的响应 |

## 1.3 系统整体结构

l CMakeLists：项目构建、依赖信息

l utils.hpp：系统数据结构，公用辅助函数（int转string、打印帮助等）

l disk.hpp：磁盘的实现，包括dir、复制等的实现

l file_system.cpp：文件系统main函数实现，启动文件系统进程

l shell.cpp：shell的实现，支持多开启动

## 1.4 Disk结构

文件系统的实现采用了多层的结构实现，首先实现disk数据基础的建立、读取、和写入，主要有以下函数：

| **函数名**           | **参数**                                                    | **返回值**     | **说明**                                                     |
| -------------------- | ----------------------------------------------------------- | -------------- | ------------------------------------------------------------ |
| init                 | 无                                                          | void           | 初始化函数，创建根目录，加载位图，创建用户                   |
| add_user_ptr         | int userId                                                  | void           | 添加用户指针，根据用户ID创建用户对象，并将其加入到用户列表中 |
| remove_user_ptr      | int userId                                                  | void           | 移除用户指针，根据用户ID删除用户对象，并将其从用户列表中移除 |
| show_path            | 无                                                          | string         | 显示当前路径，返回一个字符串表示当前目录的绝对路径           |
| cut_path             | string path                                                 | vector<string> | 路径切分，将一个路径字符串按照"/"分割，返回一个字符串向量表示路径的各个部分 |
| get_ptr_by_path      | string path                                                 | Dir*           | 根据路径获取指针，根据一个路径字符串，返回一个Dir指针，表示该路径对应的目录或者文件 |
| load_dir             | ifstream& file, Dir* dentry                                 | void           | 加载目录，从一个输入文件流中读取目录的信息，并将其存储到一个Dir对象中 |
| del_dir              | ofstream& file, Dir* dentry                                 | void           | 删除目录，从一个输出文件流中写入目录的信息，并将其从一个Dir对象中移除 |
| set_busy             | ofstream& file, unsigned int index                          | void           | 设置繁忙，将一个输出文件流中的某个位置的位图设置为1，表示该位置的磁盘块已经被占用 |
| set_free             | ofstream& file, unsigned int index                          | void           | 设置空闲，将一个输出文件流中的某个位置的位图设置为0，表示该位置的磁盘块已经被释放 |
| load_bit_map         | 无                                                          | void           | 加载位图，从磁盘中读取位图的信息，并将其存储到一个全局变量中 |
| find_block_for_inode | 无                                                          | int            | 寻找inode块，从位图中寻找一个空闲的磁盘块，用于存储inode的信息，返回该磁盘块的序号 |
| find_block_for_file  | 无                                                          | int            | 寻找文件块，从位图中寻找一个空闲的磁盘块，用于存储文件的内容，返回该磁盘块的序号 |
| init_inode           | int type, string name, unsigned int parent, string  creator | Inode*         | 初始化inode，根据给定的参数，创建一个新的inode对象，并返回其指针 |
| read_inode           | ifstream& file, int block_seq                               | Inode*         | 读取inode，从一个输入文件流中读取inode的信息，并返回一个新的inode对象的指针 |
| write_inode          | Inode* inode, unsigned int index                            | void           | 写入inode，将一个inode对象的信息写入到一个输出文件流中的指定位置 |
| del_child            | Inode* parent, unsigned int child                           | Inode*         | 删除子节点，将一个inode对象的子节点从其子节点列表中移除，并返回该inode对象的指针 |
| add_child            | Inode* parent, unsigned int child                           | Inode*         | 添加子节点，将一个inode对象的子节点加入到其子节点列表中，并返回该inode对象的指针 |
| clean_block          | unsigned int index                                          | void           | 清空块，将一个磁盘块的内容清空，用于释放空间                 |
| write_file_block     | ofstream& disk, string content, unsigned int index          | string         | 写入文件块，将一个字符串的内容写入到一个输出文件流中的指定位置，并返回剩余的字符串 |
| read_file_block      | ifstream& disk, unsigned int index                          | string         | 读取文件块，从一个输入文件流中的指定位置读取文件的内容，并返回一个字符串 |

在以上函数的基础上，针对所需实现命令，分别实现对应逻辑：

| **函数名**  | **参数**                 | **返回值** | **说明**                                                     |
| ----------- | ------------------------ | ---------- | ------------------------------------------------------------ |
| info        | 无                       | string     | 信息函数，返回一个字符串，表示当前用户的ID，当前目录的绝对路径，当前目录的权限和创建者 |
| cd          | string path              | string     | 切换目录函数，根据一个路径字符串，切换到该路径对应的目录，并返回一个字符串，表示切换成功或者失败的信息 |
| dir         | string arg               | string     | 列出目录函数，根据一个参数字符串，列出当前目录或者指定目录的子目录或者子文件的信息，并返回一个字符串，表示目录的内容 |
| md          | string name              | string     | 创建目录函数，根据一个目录名字符串，创建一个新的目录，并返回一个字符串，表示创建成功或者失败的信息 |
| rd          | string name              | string     | 删除目录函数，根据一个目录名字符串，删除一个已有的目录，并返回一个字符串，表示删除成功或者失败的信息 |
| newfile     | string name              | string     | 创建文件函数，根据一个文件名字符串，创建一个新的文件，并返回一个字符串，表示创建成功或者失败的信息 |
| cat         | string name              | string     | 查看文件函数，根据一个文件名字符串，查看一个已有的文件的内容，并返回一个字符串，表示文件的内容 |
| copy        | string arg1, string arg2 | string     | 复制文件函数，根据两个参数字符串，复制一个已有的文件到另一个位置，并返回一个字符串，表示复制成功或者失败的信息 |
| del         | string name              | string     | 删除文件函数，根据一个文件名字符串，删除一个已有的文件，并返回一个字符串，表示删除成功或者失败的信息 |
| write       | string name              | string     | 写入文件函数，根据一个文件名字符串，写入一些内容到一个已有的文件，并返回一个字符串，表示写入成功或者失败的信息 |
| write_check | string name              | string     | 写入检查函数，根据一个文件名字符串，检查是否有其他用户正在写入该文件，并返回一个字符串，表示是否可以写入该文件 |
| help        | 无                       | string     | 帮助函数，返回一个字符串，表示各个命令的用法和功能           |
| check       | 无                       | string     | 检查函数，返回一个字符串，表示当前磁盘的使用情况和空闲情况   |

# 二、 算法分析和关键代码讲解

## 2.1 文件系统与Shell的IPC实现

### 2.1.1 IPC实现分析

为了实现多个Shell可以同时向文件系统发出指令，同时不引起任何竞争和死锁，我设计了如图所示的IPC结构。

  ![](https://github.com/liukanshan1/FileSystem/blob/master/img/1.png?raw=true)

如图所示，针对文件系统，我设置了一个信号量用于提示文件系统处理命令，单独设置了登录消息在共享内存中的位置。

针对每一个Shell进程，设置它的消息与回复区域和用于通知它文件系统进程处理完成的信号量。

针对多个Shell同时登录的场景，将对登录消息进行加锁处理。

基于以上的结构，做到了多个Shell同时发出指令，不会互相影响，文件系统将依次处理指令。

### 2.1.2 IPC登录

现在介绍Shell登录过程与文件系统的执行过程。如图所示，文件系统进程等待信号量0。Shell1向共享内存中的登录消息区域写入登录请求并设置待处理标志为True，然后唤醒文件系统进程，等待文件系统处理完成（信号量1）。文件系统进程处理登录消息并设置待处理标志为False，将回复写入Shell1的回复区域中，唤醒Shell1。

 ![](https://github.com/liukanshan1/FileSystem/blob/master/img/2.png?raw=true)

### 2.1.3 IPC消息传递

下面针对多个Shell同时对文件系统进程发送请求的场景进行分析。如图所示，文件系统进程等待信号量0。Shell1和Shell2向共享内存中的消息区域写入登录请求并设置待处理标志为True，然后唤醒文件系统进程。文件系统将依次处理请求，处理完成时唤醒对应的Shell进程。此过程不会发生任何竞争和死锁。

![](https://github.com/liukanshan1/FileSystem/blob/master/img/3.png?raw=true)

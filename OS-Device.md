# 设备管理

## 设备管理的功能
设备管理的主要任务是完成用户进程提出的I/O请求，为用户进程分配I/O设备，提高CPU和I/O设备的利用率，提高I/O操作的速度，方便用户使用设备。

* 设备分配
* 缓冲管理
* 设备处理

## 输入输出系统
输入/输出系统是实现数据输入、输出以及数据存储的系统。

### 设备的分类
  * 按数据传输率分类：高速设备、中速设备、低速设备
  * 按信息交换单元分类：块设备、字符设备
  * 按设备共享属性分类：独占设备、共享设备、虚拟设备
  * 按工作特性分类：存储设备、I/O设备

### 设备控制器
设备控制器的作用是控制和协调整个计算机的动作，通常需要程序计数器(PC)、指令寄存器（IR）、指令译码器（ID）、定时和控制电路以及脉冲源、中断等共同完成

  * 设备控制器的功能
    * 接收和识别命令  
    CPU可以向控制器发送多种不同的命令，设备控制器应能接收并识别这些命令
    * 数据交换  
    实现CPU与设备控制器之间、控制器与设备之间的数据交换
    * 地址识别  
    系统中每个设备都需要有一个地址，就像内存中的每个内存单位都有一个内存地址一样，从而可以使设备控制器能够识别它所控制的每个设备
    * 数据缓冲  
    缓和I/O设备和CPU、内存之间的速度矛盾
    * 识别和报告设备的状态  
    控制器应跟踪记录设备的状态供CPU了解。
    * 差错控制  
    设备控制器还兼管对由I/O设备传来的数据进行差错检测
  * 设备控制器的组成
    * 设备控制器与处理机的接口  
    用于实现CPU与设备管理器之间的通信，共有三类信号线:数据线、地址线和控制线
    * 设备控制器与设备的接口
    * I/O逻辑  
    IO逻辑对收到的命令进行译码

### I/O通道
  * I/O通道的引入  
  在CPU和设备控制器之间引入一个新的设备`通道`，建立独立的I/O操作，满足数据的传输能够独立于CPU，同时对I/O操作的组织、管理及结束也尽量独立，从而可以保证CPU能够有更多的时间进行数据处理。
  * 通道类型
    * 字节多路通道  
    以字节为单位传输信息。适用于管理多个低速或者中速设备
    * 数据选择通道  
    一次只执行一个通道程序，以实现内存与外设之间的成批数据传送。通常用来管理高速设备
    * 数组多路通道  
    以时分复用的方式同时执行几个通道程序。适合多台设备同时操作
  * 通道程序
    * 操作码  
    操作码定义了每条指令所执行的具体操作
    * 内存地址  
    内存地址给出了数据在内存中的起始地址
    * 计数  
    指明当前操作所要操作数据的字节数
    * 通道程序结束位P  
    用来指示通道程序是否结束，P=1表示本条指令是通道程序的最后一条指令
    * 记录结束标志R  
    R=0表示该通道指令与下一条指令所处理的数据同属一个记录，R=1表示该通道指令是处理每记录的最后一条指令

### I/O系统结构
  * 总线型I/O系统结构
  * 通道型I/O系统结构

## 输入/输出控制方式

* 程序直接控制方式
* 中断驱动控制方式
* 直接存储器存取(DMA)方式
* 通道控制方式

## 缓冲管理

### 缓冲的引入

### 缓冲的实现机制
* 单缓冲
* 双缓冲
* 循环缓冲
* 缓冲池

## I/O软件

### I/O软件的层次

### 独立于设备的软件

### 用户空间的I/O软件

## 设备驱动程序的基本概念
* 设备驱动程序的功能
* 设备驱动程序的特点
* 设备处理方式
* 设备驱动程序的处理过程
* 设备驱动程序举例

## 设备分配
* 设备标识
* 设备分配中的数据结构
* 设备分配算法
* 设备分配过程

## SPOOLing系统
* 什么是SPOOLing系统
* SPOOLing系统的组成
* 共享打印机的实现

## Linux字符设备驱动程序
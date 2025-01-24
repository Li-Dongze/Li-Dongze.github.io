---
title: 蓝牙教程00-蓝牙协议栈介绍
date: 2025-01-24
summary: nrfsdk蓝牙协议栈介绍
category: 蓝牙教程
tags: [nrf52840]
comments: true
---

## 环境及硬件介绍

- 开发板：官方nr52840-DK

  ![image-20250123154958395](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241316720.png)

- keil5编程环境：mkd的CMSIS为5.3.0，NRF52840的CMSIS为8.35.0

  ![image-20250123170139483](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241316597.png)

  环境搭建教程参考：https://www.cnblogs.com/iini/p/9043565.html

- SDK版本：17.1.0

  官网下载：https://www.nordicsemi.com/Software-and-tools/Software/nRF5-SDK/Download#infotabs
  百度网盘：https://pan.baidu.com/s/1YBe-Mp2qGf6xBMMm8rEhBw 提取码：97zh

- LED和按键原理图

  ![image-20250123192708500](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241316521.png)

  ![image-20250123192759541](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241316560.png)

## nrf sdk介绍和Softdevice概述

1. nRF5 SDK是Nordic nRF51/52系列产品软件开发环境，Softdevice是Nordic蓝牙协议栈的名称，为了方便用户的使用，每一个版本的SDK都包含了该版本支持的所有softdevice，请到SDK根目录\components\softdevice目录查看具体支持的softdevice类别和版本。SDK官网下载地址为：https://www.nordicsemi.com/Software-and-Tools/Software/nRF5-SDK/Download#infotabs。

2. nRF5 SDK版本编号，nRF51 SDK v9.0.0，nRF51 SDK v10.0.0，nRF5 SDK v11.0.0, nRF5 SDK v12.0.0 …**目前最新版是nRF5 SDK v17.1.0**。SDK9/10只支持nRF51系列芯片，SDK11/12同时支持nRF51和nRF52系列芯片，而SDK13/14/15/16/17只支持nRF52系列芯片。这里顺带提一下，如果你需要使用多个版本nRF5 SDK，那么就会同时用到多个device family pack（nRF MDK），请按照**低版本先装高版本后装**的顺序来安装（如果SDK例程报device family pack错误，请先完全卸载之前的device family pack，再按照由低到高的顺序重装一遍所有的pack）

3. **Nordic一般推荐使用最新版的SDK来开发你的应用**，因为其功能最强大，考虑最周全，可靠性最好。比如**nRF51系列，推荐使用SDK12.3.0**（12.3.0已经是nRF51能支持的最高版本SDK了），**nRF52系列推荐使用SDK17.1.0**。但是最新版的SDK占用的Flash资源和RAM资源比较多，而且新版SDK为了兼容各种情况设计得也比较复杂。为了节省资源或者让应用看起来简洁，客户也可以使用某些老版本的SDK。对于老用户来说，要不要升级SDK，是一个老生常谈的问题，我的建议是：测试为王，只要你的应用测试下来没有任何问题，你的SDK就是稳定和可靠的，**就不需要升级SDK**。当然，如果你要加入新SDK的功能和特性，那么就需要升级SDK了。请打开SDK根目录\documentation\release_notes.txt以查看最新版SDK的新功能和新特性。

4. Softdevice命名规则一。Softdevice包括两种底层协议栈：BLE和ANT，BLE包括两种角色：central（又称master）和peripheral（又称slave），为此需要给这些不同类型的协议栈进行命名区分。协议栈命名格式为**Sxyz**，其中

   - x – 表示协议栈的类型，1表示BLE协议栈，2表示ANT协议栈，3表示同时支持BLE和ANT
   - y – 表示BLE角色，1表示从设备，2表示主设备，3表示同时支持主设备和从设备
   - z – 表示芯片类型，0表示nRF51系列，2表示nRF52系列
   - 比如S110，表示只支持从设备模式的nRF51 BLE协议栈
   - 比如S130，表示既支持从设备模式又支持主设备模式的nRF51 BLE协议栈
   - 比如S132，表示既支持从设备模式又支持主设备模式的nRF52 BLE协议栈
   - 比如S212，表示nRF52 ANT协议栈
   - 比如S332，表示nRF52既支持BLE协议栈又支持ANT协议栈，而且BLE协议栈既支持从设备模式又支持主设备模式

5. Softdevice命名规则二。大体上跟命名规则1相同，但是协议栈编号最后2位跟芯片型号一样，比如S140，代表这个协议栈专门用于nRF52840。由于52840 Flash空间很大，没有必要做各种细分的协议栈，S140协议栈是一个大而全的协议栈，包含蓝牙所有功能。

6. Softdevice版本编号，从1.0.0开始编号，然后2.0.0，3.0.0，…S110最新版本是8.0.0，S130最新版本是2.0.1，S132/S140/S112/S113**最新版本是7.2.0**。

## nrf sdk目录结构

打开sdk我们看到以下几个文件，接下来对他们一一进行对照

![image-20250124171434703](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241714810.png)

### 1. components文件包

该目录包含了Nordic自己开发的SDK源代码，**切记：在产品开发过程中，不要去修改该目录下的任何文件！**

![img](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241725754.png)

### 2. config文件包

Confg 文件提供开发环境以及库函数的配置，程序里主要需要使用的是 sdk_config.h 函数，这个函数在提供一个芯片配置使用模板，文件夹中一共提供三个芯片的配置:nrf52810,nrf52811,nrf52820.nrf52832.nrf52833.nrf52840,这三个函数都可以使用 ble5.0 的协议栈。

![image-20250124172814383](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241728480.png)

### 3. documentation文件包

documentaion 文件夹打开后，里面提供一个 index.html 的网页引导文件。点开这个文件就可以打开官方对整个 SDK 支持包的说明网站,说明文档包含了各个函数定义以及官方例程的简介说明，特别是对应协议栈函数和库函数，通过在网站中进行搜索，找到其
定义，对于理解 nrf5x 系列处理器的编程很重要。

![image-20250124172952692](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241729780.png)

![image-20250124173044408](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241730380.png)

### 4. examples文件包

该目录包含了丰富的应用示例，不仅包含BLE应用示例，也包含每个外设如何使用的示例，还包含bootloader示例代码。一般来说，开发过程中碰到的大部分问题，都可以在这个目录找到示例。examples目录结构如下所示：

![img](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241732406.png)

我们开发用得最多的两个目录是：\ble_peripheral和\peripheral。\ble_peripheral目录包含了BLE作为从模式的应用示例，而\peripheral包含了所有外设应用示例。

### 5. external和external_tools文件包

exteral 文件夹打开后，如下图 2.9所示。里面包含一些第三方的驱动文件夹，比如 freertos 的支持文件包。还包含了比如 fatfs 的文件系统包，lwip 的网络支持包，segger 的rtt 支持包，等等，这些第三方的驱动包还是比较多的，方便我们编程的时候调用，大家感兴趣的可以自行了解。

![image-20250124173648205](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241736293.png)

extermal tool 工具包，放置一些外部工具包，目前该文件包里只包含了 CMSIS 的配置向导

![image-20250124173737880](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241737967.png)

### 6. integration和modules文件包

这两个文件夹提供的是硬件顶层驱动，下面详细说明下这两个文件夹:

integration 文件夹:这个文件夹提供的是老版本驱动的兼容头文件。比如你用老版本的 SDK 底层硬件外设驱动移编写的程序，现在使用新版本 SDK，需要进行移植，这时就可以通过配置这个文件夹的里的硬件底层兼容文件夹进行兼容。方便早期版本的硬件底层外设驱动迁移到版本驱动之下。

![image-20250124174058098](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241740180.png)

modules 文件夹:以 nrfx 命名一个文件夹，这个文件夹提供了多个文件包。

- doc：一些说明备注
- Driver：最新版的驱动c文件和头 h 文件
- Ha:老版本的硬件配置头文件
- mdk: keil,iar 等编译环境使用的启动文件
- Soc:处理器中断声明的文件
- Templates:处理器的配置文件文本

![image-20250124174142129](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241741224.png)

## 打开例程开启蓝牙之旅

在`examples/peripheral`文件夹下打开一个点灯例程

![image-20250124175602617](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241756711.png)

- Hex 文件夹:官方提前发的工程 hex 文件，可以直接下载。
- pca10040 文件夹:提供的是芯片 nrf52832 的芯片支持工程，需要使用的 nrf52832 芯片的外设可以打开这个工程包。
- pca10040e 文件夹:提供的是芯片nrf52810 的芯片支持工程，需要使用的 nrf52810 芯片的外设可以打开这个工程包，但目前不是所有的工程里都有 nrf52810。
- pca10056 文件夹:提供的是芯片 nrf52840 的芯片支持工程，需要使用的 nrf52840 芯片的外设可以打开这个工程包。
- pca10056e 文件夹:提供的是芯片nrf2811 的芯片支持工程，需要使用的nrf52811 芯片的外设可以打开这个工程包。
- pca10100 文件夹:提供的是芯片 nrf52833 的芯片支持工程，需要使用的 nrf52833 芯片的外设可以打开这个工程包。
- main.c文件:工程的主函数，工程主函数是几个芯片公用的，这样非常方便不同芯片之间的移植
- blinky.eww:IAR 工程。

我们的芯片使nrf52840，选择pca10056文件夹，打开blank文件夹，会出现很多工程目录：

![image-20250124180903213](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241809306.png)

- arm5_no_packs:keil5 的工程文件包
- armgcc:gcc 的编译支持文件
- config:nrf52832 的配置文件
- iar:IAR 的工程文件包
- ses:segger embedded studio的工程文件包

我们的环境为keil5，打开arm5_no_packs文件夹下的keil工程即可

![image-20250124181116284](https://picture-note-1328988318.cos.ap-nanjing.myqcloud.com/Typora/202501241811441.png)

编译通过。若编译不通过则环境搭建失败，参考这篇重新搭建：https://www.cnblogs.com/iini/p/9043565.html

---
layout: post
title: OpenWrt 编译步骤与命令详解教程
date: 2024-04-11
categories: blog
description: 文章金句。
---

# 1前言
编译 Open­Wrt 的过程就像是复读机，除了选择系统组件外，几乎每次编译都是复制粘贴相同的命令。而理解每一条命令的作用、什么时候该去执行，这样才能更好的去解决编译中遇到的问题，更顺利的编译出固件。

# 2首次编译
## 1克隆 Open­Wrt 源码

>git clone https://github.com/coolsnowwolf/lede openwrt

这里以 Lean 大佬的源码仓库为例子，毕竟很多人都在用它。命令末尾加了openwrt是指克隆代码到openwrt目录，目的是为了规范化，因为有时并不是编译这个的源码。
## 2进入源码目录

>cd openwrt

## 3下载 feeds 源中的软件包源码

>./scripts/feeds update -a

feeds 是扩展的软件包，独立于 Open­Wrt 源码之外，所以需要单独进行拉取和更新。
## 4安装 feeds 中的软件包

>./scripts/feeds install -a

## 5调整 Open­Wrt 系统组件

>make menuconfig

首次编译建议只选择架构，其它都不要动，这样编译成功率会更高。如果不打算调整组件则输入make defconfig，它会检测编译环境并生成默认的编译配置文件。
## 6预下载编译所需的软件包

>make download -j8 V=s

-j8是指使用8个线程下载，理论上是数字越大下载越快，但似乎有个上限，实测5线程以上其实速度相差不了多少，在网络好的情况下，基本在5分钟以内能下载完。
## 7检查文件完整性

>find dl -size -1024c -exec ls -l {} \;

此命令可以列出下载不完整的文件（根据我多次编译的经验得出小于1k的文件属于下载不完整），如果存在这样的文件可以使用
>find dl -size -1024c -exec rm -f {} \;

命令将它们删除，然后重新执行make download下载并反复检查，确认所有文件完整可大大提高编译成功率，避免浪费时间。
## 8开始编译

>make -j1 V=s

-j1：使用单线程编译。新手推荐单线程编译，一是因为玄学问题可能成功率高，二是方便查看错误日志。
V=s：输出详细日志，用于编译失败时找出错误。而且满屏代码在跑能装逼，一跑就是几个小时，装逼更持久。

# 3再次编译
## 1进入源码目录（如果不在此目录）

>cd openwrt
## *更新
TIPS： 短期内再次编译可忽略更新这个步骤。
## *更新系统软件包

>sudo sh -c "apt update && apt upgrade -y"

主要作用是更新在编译环境搭建时所安装的编译组件
## *拉取 Open­Wrt 源码更新

>git pull
## *更新 feeds 源中的软件包源码

>./scripts/feeds update -a
## *安装 feeds 中的软件包

>./scripts/feeds install -a
## 2文件清理
### 1清除旧的编译产物（可选）

>make clean

在源码有大规模更新或者内核更新后执行，以保证编译质量。此操作会删除/bin和/build_dir目录中的文件。
### 2清除旧的编译产物、交叉编译工具及工具链等目录（可选）

>make dirclean

更换架构编译前必须执行。此操作会删除/bin和/build_dir目录的中的文件(make clean)以及/staging_dir、/toolchain、/tmp和/logs中的文件。
### 3清除 Open­Wrt 源码以外的文件（可选）

>make distclean

除非是做开发，并打算 push 到 GitHub 这样的远程仓库，否则几乎用不到。此操作相当于make dirclean外加删除/dl、/feeds目录和.config文件。
### 4还原 Open­Wrt 源码到初始状态（可选）

>git clean -xdf

如果把源码改坏了，或者长时间没有进行编译时使用。
### 5清除临时文件

>rm -rf tmp

删除执行make menuconfig后产生的一些临时文件，包括一些软件包的检索信息，删除后会重新加载package目录下的软件包。若不删除会导致一些新加入的软件包不显示。
### 6删除编译配置文件

>rm -f .config

在不删除的情况下如果取消选择某些组件它的依赖组件不会自动取消，所以对于需要调整组件的情况下建议删除。
编译
### 7调整 Open­Wrt 系统组件

>make menuconfig

如果不打算调整组件则输入make defconfig，它会检测编译环境并根据更新自动调整编译配置文件。
### 8预下载编译所需的软件包

>make download -j8 V=s
### 9检查文件完整性

>find dl -size -1024c -exec ls -l {} \;

此命令可以列出下载不完整的文件（根据我多次编译的经验得出小于1k的文件属于下载不完整），如果存在这样的文件可以使用
>find dl -size -1024c -exec rm -f {} \;

命令将它们删除，然后重新执行make download下载并反复检查，确认所有文件完整可大大提高编译成功率，避免浪费时间。
### 10开始编译

>make -j$(nproc) || make -j1 || make -j1 V=s

多线程编译失败后自动进入单线程编译，失败则输出详细日志。
# 4尾巴
很少有人会告诉你为什么要这样做，而是会要求你必须要这样做。



本文作者：P3TERX
（本文为转载，只为自己方便查看使用。）
本文链接：https://p3terx.com/archives/openwrt-compilation-steps-and-commands.html

版权声明：本博客所有文章除特别声明外，均采用 CC BY-NC-SA 4.0 许可协议。非商业转载及引用请注明出处（作者、原文链接），商业转载请联系作者获得授权。

---
layout: post
title: openwrt挂载剩余硬盘空间！
date: 2020-04-07
categories: blog
description: 文章金句。
---


  软路由用的是60G的固态硬盘，而openwrt占用的空间很少，将剩下的空间划分出来，做可道云空间，存储办公文档，还是不错的。  
  首先，ssh连接，然后输入 fdisk /dev/sda

   ![image](https://github.com/RayTsanger/picx-images-hosting/raw/master/20260323/openwrt1.b9gvsb7fm.webp)

  继续输入w，保存分区表  
 
   ![image](https://github.com/RayTsanger/picx-images-hosting/raw/master/20260323/openwrt2.7lkk6u2sf7.webp)

  开始格式化分区，输入mkfs.ext4 /dev/sda3   
 
   ![image](https://github.com/RayTsanger/picx-images-hosting/raw/master/20260323/openwrt3.8z73avdug4.webp)

  挂载分区  
  登录路由器：系统 > 挂载点> 添加  
  启用此挂载点：勾选  
  UUID：选择要挂载的分区  
  挂载点：选择（自定义），输入 /mnt/sda3回车。    
   ![image](https://github.com/RayTsanger/picx-images-hosting/raw/master/20260323/openwrt4.8dxfokje5i.webp)









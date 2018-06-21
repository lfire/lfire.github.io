---
title: vmnet 分类 & 区别
date: 2018-06-21 14:17:19
tags:
    - vmnet
    - vmware
    - NAT
    - 桥接
    - 仅主机
categories:
    - 工具
    - vmware
---

@[TOC]

在安装使用 VMware Workstation 虚拟机时，创建的虚拟机通常我们需要配置，以使其可以上网，或是达到其他的网络通信条件，这时候我们就经常会接触到虚拟网卡。而在虚拟网中，我们又经常的看到 VMnet0、VMnet1、VMnet8 ，甚至有些还需要更多，VMnet2 ~ VMnet7 和 VMnet9 ，具体这些是什么含义，各有什么功能，又有什么区别？

<!-- more -->

## 1 VMnet 功能
在实际使用过程中，默认的情况下， VMware Workstation 中有3个虚拟交换机，分别是：
- VMnet0 使用桥接网络
- VMnet1 仅主机网络
- VMnet8 NAT 网络

通过 `编辑` -> `虚拟网络编辑器` ，调出 VMnet 编辑器，如下图：
![虚拟网络编辑器](http://pic.hqmmw.com/markdown-img-paste-20180621155909739.png)

## 2 VMnet0
VMnet0，实际是上一个虚拟的网桥，有若干个端口，一个端口用于连接宿主机 host，一个端口用于连接相应的虚拟机，其位置相应对等，谁也不是谁的网关。因此，在 Bridged 模式下，虚拟机成为了一台与宿主机相同地位的机器。
![桥接模式](http://pic.hqmmw.com/markdown-img-paste-20180621161205105.png)

## 3 VMnet1
VMnet1，是一种 Host-Only 网络模式，用于建立起一个与外界隔离的网络环境，其也是一个虚拟交换机，交换机的一个端口连接到宿主机，另一个端口连接到虚拟机的 DHCP 服务器上（实际是 VMware 的一个组件），另外剩下的端口就是连接虚拟机了。其可访问的范围即是 Host-Only 模式所指定的网段。
![Host-Only 模式](http://pic.hqmmw.com/markdown-img-paste-20180621161750445.png)

## 4 VMnet8
VMnet8，是一种 NAT 模式，最简单的组网方式了，从主机的“VMWare Virtual Ethernet Adapter for VMnet8”虚拟网卡出来，连接到vmnet8虚拟交换机，虚拟交换机的另外的口连接到虚拟的NAT服务器（这也是一个Vmware组件），还有一个口连接到虚拟 DHCP 服务器，其他的口连虚拟机，虚拟机的网关即是“VMWare Virtual Ethernet Adapter for VMnet8”网卡所在的机器。相比之下，可以看出来，NAT组网方式和Host-Only方式，区别就在于是否多了一个NAT服务。
![NAT 模式](http://pic.hqmmw.com/markdown-img-paste-20180621162323729.png)

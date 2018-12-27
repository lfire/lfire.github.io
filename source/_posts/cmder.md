---
title: 神器 cmder
date: 2017-03-02 09:12:42
tags:
- cmder
- shell
- tool
- 工具
- cmd
- windows cmd
categories:
- 工具
- shell
---
@[TOC]

<!-- more -->

当我们在 windows 环境下来进行开发编码工作时，是不是经常有一个不好的体验—— cmd 太弱了。

## 1 windows cmd 不足
> * 界面太过于死板，不美观
> * 相关的操作，以及命令支持不完善，无法达到，或是平衡 linx 平台下相关的操作习惯
> * 当前各种框架平台不断发展，相关需要在命令行下执行的操作越来越多，而cmd没有多标签等相关的切换功能
> * 窗口的大小直接受限，不能自如的调整窗口大小
> * ...

以上所列出的点不尽全，相信只要有需要使用 shell 来进行终端操作的用户就能感觉到 windows 对于用户，在这方面的不友好。而现实的开发工作序列中，无论是我们的代码管理，还是我们的环境编译，以及其他相关框架或平台的应用，如若想完全的脱离终端，这种可能性及小。
另外，从操作的效率性方面来出发，命令行下的操作，要比鼠标和键盘的来回切换操作来得高效和方便。（也许你不太认可，但这是很大一部分人公认的。）

## 2 神器登场 cmder
[cmder](http://cmder.net) 是一款绿色且开源的软件，并且已经托管在了 [github](https://github.com/cmderdev/cmder) 上，其主要的目标即是在 windows 平台上，打造类 linux 的终端操作体验。
先来一张靓照：

![靓照](http://pic.hqmmw.com/markdown-img-paste-20181227145429530.png)

从图中我们可以看到，中文支持友好，同时，终端的颜值很高，最最主要的是，本软件是高度可定制的。
只要是你愿意，你完全可以根据你自己的喜好，自定出一套你自己喜欢的主题出来。
如下图所示，你可以打开 Settings 来配置你自己的各种需要

![配置](http://pic.hqmmw.com/markdown-img-paste-20181227145453472.png)

## 3 主要特性
### 3.1 提供高度类 Linux 的终端体验
在我们平常的使用命令终端的体验过程中，如若能达到 linux 平台下相关命令的可靠性，那即是 windows 平台下开发者的福音，而 cmder 正是朝着这一方向而来的。可以支持的初略的列一下：
pwd ll ls whoami where cp rm unzip ...

![类Linux体验](http://pic.hqmmw.com/markdown-img-paste-20181227145514528.png)

### 3.2 快捷键支持丰富
* **打开设置：** 使用 `win + alt + p`
* **新建标签：** `ctrl + t`
* **关闭标签：** `ctrl + w`
* **快速新建不同类型标签：** `shift + alt + number`
  1. cmd
  2. PowerShell
* **全屏：** `alt + enter`
* **返回上级目录：** `ctrl + alt + u`
* **历史查询：** `ctrl + r`
* **选择复制文本：** `left mouse select`
* **粘贴文本：** `right click`
更多的快捷键，你可以打开 **Settings** > **Keys & Macro** 中进行设置和查看。

### 3.3 支持命令别名(Aliases)配置
这是 cmder 所提供的一个非常方便的功能，我们可以很个性化的设置我们个人喜欢的命令，来完成某些长命令的输入。
以下是我别名配置的一个片段，大家可参考：
```bash {.line-numbers}
pwd=cd
clear=cls
history=cat "%CMDER_ROOT%\config\.history"
unalias=alias /d $1
vi=vim $*
cmderr=cd /d "%CMDER_ROOT%"
e.=explorer .
gl=git log --oneline --all --graph --decorate  $*
l=ls --show-control-chars  --color $*
la=ls -aF --show-control-chars --color $*
ll=ls -alF --show-control-chars --color $*
ls=ls --show-control-chars -F --color $*
```

### 3.4 对于中文支持的处理
在平常应用过程中，我们经常会遇到中文问题的苦恼，如何很好的解决中文在各种场景下的显示问题，是很多类似产品的一大痛点。

**常见场景：**

* 中文文件或中文文件夹名的显示和操作；
* VIM 中打开包含中文内容的文件查看及编辑等；
* git 提交代码到 github 等代码仓库时，中文日志的提交及查看；

**几个关键配置要点：**

* 中文字体的选择：我们需要选择相关支持中文字符显示的字体来做为软件的展示字体。
* 字符编码的选择：在 windows 下，很多文件名，及文件的编码都是以 GBK 为编码，因此，这里我们需要很慎重的选择软件的字体编码。
* 环境变量的设置：因本软件的类 linux 的设计，所以很多命令，如 git 都有着 linux 下相似的处理逻辑，而 github 这种是全以 UTF8 为编码的平台，因此，环境变量需要对此进行特殊处理。
* VIM 的字符编码配置：因是在 windows 平台中，很多的文件的编码可能是多种多样的，不是固定的某一种，因此，在 VIM 的配置中，就必须要考虑到这种情况，配置让它可以智能的识别并转换文件内容编码。

以下贴出我所配置的部分参数：
**字体&字体编码配置**

![字体配置](http://pic.hqmmw.com/markdown-img-paste-20181227145551589.png)

其中的 **YaHei Consolas Hybrid** 是我在编程过程中，所遇到的一种字体，对于中文、英文大小写、以及数字的支持以及辨识度很高。[个人推荐使用，可以这下载安装，提取密码: tmgw](http://pan.baidu.com/s/1i4HDZE1 )
而其中因是在 windows 环境下，所以建议使用 GB2312 编码。

**环境变量配置**

![环境变量配置](http://pic.hqmmw.com/markdown-img-paste-20181227145625196.png)

为兼容 github 等使用，整体将 cmder 的环境变量中 LANG 设置为 UTF-8 ，以此实现 *git log* 等命令查看时支持中文。

**VIM配置**

![VIM配置](http://pic.hqmmw.com/markdown-img-paste-20181227145651213.png)

为实现 VIM 支持各种编码格式文件的中文查看及编辑，cmder 内部已做好了配置进行处理，上图只是将相关的配置内容贴出。

## 4 整体配置及软件包下载
为方便大家直接使用，我将当前我所使用的版本，以及配置整体打包分享出来。
[推荐大家使用，提取密码: 1aix](http://pan.baidu.com/s/1nuKTEe1)

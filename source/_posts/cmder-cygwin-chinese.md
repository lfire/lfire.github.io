---
title: cmder & cygwin 中文支持解决方案
date: 2017-03-08 09:54:46
tags:
- cmder
- cygwin
- shell中文支持
- windows shell
categories:
- 工具
- shell
---

@[TOC]

<!-- more -->

在 windows 环境下，我们因系统本身自带的 cmd 方案表现很弱，所以我们经常使用其他的解决方案来弥补。而这些方案中最为常见和好用的也就是 cmder 和 cygwin 两种。
今天对于方案中，我们常遇到的问题——中文编码乱码问题来配置使用。

## 1 使用环境 & 场景
中文环境下，最为常见的编码就是：

* GBK
* UTF-8

而在 windows 环境下，这两种编码的文件同时存在是非常常见的，而在 cmd 的终端环境下，想要一次性解决该显示问题，目前来说并不容易，因此，我们当前只能寻找一种最为全面的解决方案，以最大可能来解决该问题。
因此，目前需要来分析一下使用的场景：

* 程序员编码
* 各工具使用，如： git、svn等

而对于编码的场景下，我们目前最为推荐的编码格式仍是 utf-8 ，所以，这里也一样的不解释，建议使用 utf-8。
而对于 git & svn 等相关的版本管理工具，这里也是程序员常有遇到的场景，而在 git 序列中，github 的使用常在手边过，而 github 所支持的中文编码就是 utf-8 。
从这些相关的场景分析来看，我们所有可控的场景中，我们最好使用的编码仍然是 **utf-8** 。
因此，这里本人也强烈推荐各位，在可以自己控制的情况下，我们应该首选 **utf8**。
场景的主编码确定好后，我们就可以分别针对两种不同工具，来进行相关的支持配置。

**配置总体可以分为：**

* 软件界面
* 终端环境变量
* 相关工具配置

## 2 cmder 方案
### 2.1 界面配置
右键标题栏 > settings
![右键菜单](http://pic.hqmmw.com/markdown-img-paste-20181227145839830.png)

Main > Font charset
![字体](http://pic.hqmmw.com/markdown-img-paste-20181227145858116.png)

这里选择 GB 2312 主要是因为，windows 系统的主要编码还是：ANSI。

### 2.2 终端环境变量
Settings > Startup > Environment
![环境变量](http://pic.hqmmw.com/markdown-img-paste-20181227145911985.png)

这里的设置，需要关闭 cmder 再重新打开一次生效。
我们可以通过 locale 命令查看设置的结果：
```bash
λ locale
LANG=zh_CN.utf-8
LC_CTYPE="zh_CN.utf-8"
LC_NUMERIC="zh_CN.utf-8"
LC_TIME="zh_CN.utf-8"
LC_COLLATE="zh_CN.utf-8"
LC_MONETARY="zh_CN.utf-8"
LC_MESSAGES="zh_CN.utf-8"
LC_ALL=
```

**在新版本 V 1.3.5 中发现无法通过界面设置环境变量保存成功，下面提供另一种实现方法：**
在安装目录下找到用户配置文件路径 `config/user-profile.cmd` ，打开编辑，在其中加入环境变量配置语句：
```bash
set LANG=zh_CN.utf-8
```

### 2.3 相关工具的配置

**git 配置**

在 git 命令行下，主要是与：

* i18n.commitencoding
* i18n.logoutputencoding

两个配置参数有关，我们可以通过以下命令进行配置：

```bash
λ git config --global i18n.commitencoding utf-8
λ git config --global i18n.logoutputencoding utf-8
```

**VIM 配置**

vim 是终端下最为常见的文档编辑器，我们可以在 VIM 的配置文件中加入如下配置：

```ini
set fileencoding=cp936
set fileencodings=ucs-bom,utf-8,cp936,gb18030,big5,euc-jp,euc-kr,latin1
```

注：
查找 vim 配置文件的位置，可以通过进入 vim 后，输入
:version


## 3 cygwin 方案
### 3.1 界面配置
右键标题栏 > Options
![界面配置](http://pic.hqmmw.com/markdown-img-paste-20181227145956544.png)

Text
![文本](http://pic.hqmmw.com/markdown-img-paste-20181227150010779.png)

### 3.2 环境变量配置
修改 ~/.bashrc 文件，加入：
export LANG="zh_CN.UTF-8"
export OUTPUT_CHARSET="UTF8"

### 3.3 相关工具配置
git 和 vim 的配置与 cmder 下是一致的，可以采用同样的方式来处理。
![其他配置](http://pic.hqmmw.com/markdown-img-paste-20181227150042705.png)

## 4 综合方案
cmder 是一种终端集成器，它同样可以将 cygwin 集成到其内部窗口上。
具体的配置方式，我们可以参考 [cmder 官方手册](https://github.com/cmderdev/cmder/wiki/%5BWindows%5D-Integrating-Cygwin)
以下是我的配置截图：
![举例](http://pic.hqmmw.com/markdown-img-paste-20181227150057983.png)

配置后的运行效果图：
![运行效果](http://pic.hqmmw.com/markdown-img-paste-20181227150130958.png)

---
title: win10 升级后 cmder 别名失效修复
date: 2017-03-10 20:05:24
tags:
- cmd
- cmder
- shell tool
- cmder alias
categories:
- 工具
- shell
---
@[TOC]

<!-- more -->

近期系统接收到了 win10 的慢速升级推送，但发现，升级过后，系统原先配置好的 cmder 别名全都无法正常工作。在 cmder github 上也试图查找相关的解决方案，但很多的说法以及配置都没能解决，最后，在一个地方发现了问题的初步解决方案。

## 1 问题表现，别名全部失效
表现是相关加载都能成功，但命令本身就是不生效，无法工作，并且，相关中文也会产生叠加现象。
![image_1bas246jq19641o3ffbjncm14ea9.png-32.6kB][1]

## 2 查找答案
我通过 google 查找各种可能性，最后终于在 [github cmder issues](https://github.com/cmderdev/cmder/issues/1257) 查找到了需要的答案。
原来，
> Ok guys, found what the issue was.
I am using Windows 10 Insider build 15025 - the problem was with modification that were made by Microsoft to standard cmd.exe

> To solve this I had to "Use legacy console" option in standard cmd - which solved this issue.

是因 win10 升级，可能其内部改变了某些终端的特性，我们需要禁用新的控制台。
![image_1bas34bfun11ip51bot1am71ute9.png-36kB][2]

这样配置完成后，我们重新打开 cmder 一次，** OK，问题解决 **。


[1]: http://static.zybuluo.com/lfire/39hqqx5xswsstbpa6ayc620y/image_1bas246jq19641o3ffbjncm14ea9.png
[2]: http://static.zybuluo.com/lfire/xew1dq4a9von8309oqczgpij/image_1bas34bfun11ip51bot1am71ute9.png

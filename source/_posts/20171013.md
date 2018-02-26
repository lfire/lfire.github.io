---
title: hexo基础搭建博客系统
date: 2017-10-13 14:12:05
tags:
- hexo
- 博客
categories:
- 工具
---
@[TOC]

<!-- more -->

为了更好的使用 hexo 博客系统，对相关的配置进行一个基本的梳理或是内置默认值，其中使用 next 作为其中默认的主题样式，以此来美化 hexo 本身自带的一套主题。系统提供有独立的 github 仓库，同时通过对相关细节记录下来，以达到后期回看，或是另外环境快速搭建的目的。

## 1 介绍&安装
本系统是基于 hexo 搭建，因此，我们需要先安装好 hexo 的基础环境。
[hexo 帮助说明](https://hexo.io/zh-cn/docs/index.html)
在 hexo 的官网，有详细的说明，如何搭建 hexo 的命令环境，以及基础 node 环境的安装也都有详细说明。
### 1.1 简单的过程
```bash
## 安装 hexo
npm install -g hexo-cli
## 初始化 hexo，其中 blog 为你希望用于存储博客文件的目录名称，可以自行决定
hexo init blog
```

### 1.2 安装主题
因本次的系统，我们默认以 next 为博客的默认主题，因此我们可以通过 github 来实现 next 的安装。
```bash
git clone https://github.com/iissnan/hexo-theme-next themes/next
# 或使用SVN
svn export https://github.com/iissnan/hexo-theme-next.git/trunk/ themes/next --force
```
以上的两种情况，你可以根据你个人的喜好和环境选择一种方式即可。

### 1.3 默认配置导入
在本次的搭建过程中，hexo 本身存在有很多的相关配置，因此，经过梳理，将相关配置开了一个项目，托管到了 github，我们可以通过将这份线上的配置下载到本地博客系统中，即可实现一次性配置。
```bash
svn export https://github.com/lfire/wumiblog.git/trunk/ ./ --force
## 安装依赖
npm install
```

## 2 博客文章新建
环境搭建完成后，我们就可以开始文章的写作了。写作过程中，hexo 设计了一个【layout】的概念，从字面来理解，就是布局的意思，也可以理解为模板，如果有相关能力者，你也可以开发相应的 layout 来支持更为丰富的功能，这点上，不再展开，具体可以查看官方的相关文档。
以下记录几种常用的 layout 页的建立命令：
```bash
## 创建标签页
hexo new page "tags"
## 创建分类页
hexo new page "categories"
```
需要注意的是，在实际使用过程中，我们新建普通的博客文章，这个 layout 参数是可以省略的，如：
```bash
hexo new hello-hexo
```
执行之后，就会在对应的目录下（如目录名为 blog，则路径为：*blog/source/_posts/hello-hexo.md*）生成相关文件。

## 3 部署&发布
在配置文件中（*blog/_config.yml*），如下图，会有 github 部署位置的一个配置，本例中，使用的只是一个演示 demo，有需要的可以自已修改为对应的位置即可。
![image_1bsk41i721c8d1d9f1p761dop2159.png-12.6kB][1]
配置部署位置后，我们就可以执行相关命令来完成部署。
### 3.1 生成
```bash
## 先将 markdown 文档转化为静态 HTML 文件资源
hexo generate
## or 缩写
hexo g
```

### 3.2 部署
```bash
## 将生成的静态文件上传部署到配置位置
hexo deploy
## or 缩写
hexo d
```

### 3.3 一步到位
```bash
hexo generate --deploy
## or
hexo deploy --generate
## or 缩写
hexo g -d
hexo d -g
```

  [1]: http://static.zybuluo.com/lfire/tldkxmb2dhy0cni66z4jrsvc/image_1bsk41i721c8d1d9f1p761dop2159.png

---
title: hexo 博客升级并上传 github
date: 2018-02-27 11:08:39
tags:
- git
- github
- hexo
categories:
- 工具
- hexo
---
@[TOC]

<!-- more -->

Hexo 的主程，因为在不断的升级变化，在最初搭建博客时，使用的版本是 **3.2.0**，目前已经发展到了 **3.5.0**。另外，还有其他一些相关的插件，包括主题等都有了相应的更新，因此，近期考虑了一下将整体升级一次。
其中遇到了不少的问题，这里将这些问题记录一下。

## 1 问题有哪些
* hexo 本身就有配置文件，主题也有配置文件，多位置配置，导致升级非常麻烦
* 博客使用 github page 托管，但 markdown 源码又是使用其他的库来保存，导致管理分散
* 在写博客的过程中，没有找到合适顺手的编辑软件，很不方便

## 2 配置文件分散与升级的矛盾
hexo 的配置文件位置 *_config.yml* ，以及相应安装主题的配置文件 *themes/xxxx/_config.yml* ，这两者在一定程度上加大了程度升级的复杂度。
以我安装的 NexT 主题为例，它本身的配置项非常的多，再加上其也在不断的升级更新，如果我们的配置文件存储位置在其项目工程目录以内，就导致了主题在更新升级时，可能会存在被覆盖的风险。
这些还只是配置文件，如果再还有对页面样式、模板等内容进行过自定义，这些内容就更容易被覆盖。因此，我急需将这些内容移出，并独立于主题的工程目录。
所幸，hexo 官方本身也考虑到了这一点，支持 [数据文件夹](https://hexo.io/zh-cn/docs/data-files.html) 的配置使用，*source/_data/* 目录。

### 2.1 hexo 配置
**配置路径**
```bash
_config.yml
```

**配置内容**
为了配置项的相对集中，该配置文件中的某些相关的内容，可以移入数据文件夹，也主题 NexT 的配置相结合。必需在该路径下配置的有：
* theme
* deploy
* Directory 中的： skip_render
* symbols_count_time
其他的配置项可以保持原有值不修改，具体的配置值可以拷贝一份到数据文件夹。

### 2.2 NexT 主题配置
**配置路径**
```bash
source/_data/next.yml
```

**配置内容**
将 hexo 配置中的必须配置项以外的项目，全部拷贝一份到 next.yml ，以及将主题 next 的配置文件(themes/next/_config.yml)中全部配置文件，再对相应的配置项进行修改。
前提条件是，使用 next 的主题版本在 6.0.x，[详细的说明](https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/UPDATE-FROM-5.1.X.md)，可以参考官网帮助手册。

### 2.3 自定义样式
对于 NexT 主题，在某些情况下，个人还是希望能对其样式有所修改，同时期望不会影响到 NexT 本身的升级，因此，可以借助数据文件夹的思路，将个性化的样式表，建于此处。
**配置路径**
```bash
source/_data/styles.styl
```

## 3 github 同时托管源码与静态站点
一般的思路，并且现在大多的博客上所说明的，一般是 github pages 如何部署，结合 Hexo 的话，就是如何配置好 github 的部署参数后，再执行 *hexo d* 命令成功完成部署就基本结束了。
但我们还有一部分，我们在博客编写过程中，还有很多的 markdown 源码，这些内容，我们要如何来进行管理，难道一个仓库就只能用于托管一个静态站点吗？
显然不是的，我们的静态站点，只占用了仓库的一个分支 **master**， 也就是说，我们完成可以建立其他的分支，来用于存储我们的 markdown 源码。

### 3.1 git ignore 配置
工程目录下，因包含有很多编译输出的结果，以及 nodejs 模块目录，而这些内容，是不需要提交到仓库的。
```bash
# .gitignore
.idea
.deploy_git
node_modules
public
themes
db.json
package-lock.json
```

### 3.2 github 配置
因是升级处理该系统，因此，原本仓库中已经存在有相关内容，现在的做法是，将 Hexo 的工程源码，提交到一个新建的分支中。
```bash
# 在根目录下进行 git 初始
git init

# 配置远程仓库参数
git remote add origin https://github.com/lfire/lfire.github.io.git

# 新建一个远程分支 src
git checkout -b src

# 提交所有源码
git add .
git commit -m "init the blog source"
git push origin src

# 补充一个有用的命令
# 因误操作，创建了一个错误的分支
# 需要删除
# git push origin --delete xxxx
```
这样，分支 *src* 就将所有相关的源码进行了管理，而 *master* 分支则管理了 github pages 的站点。

## 4 博客编写编辑器
最初，一直没能找到一个得心应手的编辑器，后面选择了一款付费软件， Cmd Markdown，但对于 Hexo 的支持，只能说是到达 markdown 的支持，如果要细化到 Hexo 本身，这就有差距了。还有就是，Cmd markdown 使用的是其自建的图床，个人有点担心，还是希望可以自己掌握所有这些资源的去处，最后选择了配置七牛。
另外一个方面， vscode 和 atom 的发展速度确实惊人，这两款软件提供了大量的插件可用，与我个人使用习惯一匹配，个人最后选择了 Atom 。
![write markdown blog with atom](http://pic.hqmmw.com/8e64a09f34f780e41f907342a838491c.png)
其中有一个重要的插件，可以提一下，[Markdown Preview Enhanced](https://atom.io/packages/markdown-preview-enhanced)

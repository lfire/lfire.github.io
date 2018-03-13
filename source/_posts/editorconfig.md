---
title: EditorConfig 代码格式定义
date: 2018-03-12 12:33:01
tags:
- EditorConfig
- 插件
categories:
- 工具
- 前端工具
---

@[TOC]

<!-- more -->

在项目开发过程中，如果涉及到多人一起合作开发，以及在开发过程中，还会有代码 review 等相关操作，这时候，团队内一种统一风格的代码编写方式显得尤为重要。而目前市面上的代码编辑器又非常多，代码格式化的工具或插件等也有很多，如要达到我们的风格一致的要求，借助工具的帮助最为直接。
[**EditorConfig**][1] 即是一款定义各代码风格的工具，其在很多工具中都能得到支持，如：WebStrom、Atom、Sublime、VSCode等。

## 1 配置项说明

EditorConfig 文件使用 INI 格式，允许在分段名（section names）中使用 **and**，分段名是全局的文件路径，格式类似于 **gitignore**。斜杠（/）作为路径分隔符，# 或 ; 作为注释。注释应该单独占一行。
EditorConfig 文件使用 utf-8 格式，crlf 或 lf 作为换行符。

```ini
# 表明是最顶层配置文件，发现设为 true 时，才会停止查找 .editorconfig 文件
root = true

# 缩进样式，可以设置为 tab 或 space 两个值
indent_style = space

# 当缩进样式设置为 space 时，用来设置每次缩进相当于多少列
indent_size = 4

# 当缩进设置为 tab 时，用来设置每次缩进相当于多少列代码
tab_width = 4

# 定义行末采用什么符号来进行换行，可以选择包括：lf、cr 和 crlf
end_of_line = crlf

# 编码格式，支持： latin1、utf-8、utf-8-bom、utf-16be 和 utf-16le，不建议使用 utf-8-bom
charset = utf-8

# 是否除去代码行末的任意空白字符，true 或 false
trim_trailing_whitespace = true

# 是否以一个空白行结尾文件，true 或 false
insert_final_newline = true
```

以上即是相关配置项，及其说明。目前所有的属性名和属性值都是大小写不敏感的，编译时会自动将其转换为小写。通常，如果没有明确指定某个属性，则会使用编辑器的配置，而 EditorConfig 不会处理。
另外，并不是所有的编辑器插件都支持以上全部配置属性，项目 [Wiki][2] 上有兼容配置属性的详细参考。
推荐不要指定某些属性，如，tab_width 不需要特别指定，除非它与 indent_size 不同。同样的，当 indent_style 设置为 tab 时，不需要配置 indent_size ，这样方便阅读者使用他们习惯的缩进格式。仍没有规范化的属性，就最好不要设置它，如：end_of_line。

## 2 通配符（Wildcard Patterns）

| 通配符        | 说明                  |
| :--------- | ------------------- |
| \*         | 除路径分隔符 / 外匹配所有字符串字符 |
| \*\*       | 除路径分隔符 / 外匹配所有字符串字符 |
| ?          | 匹配所有单个字符            |
| [name]     | 匹配 name 字符          |
| [!name]    | 匹配非 name 字符         |
| {s1,s2,s3} | 匹配任意给定的字符串，通过逗号分隔   |

特殊字符需要通过转义符进行转义，使得特殊字符不会被当做通配符解析。

## 3 配置示例

```ini
root = true

[*]
charset = utf-8
insert_final_newline = true

[*.{js,html}]
indent_style = space
indent_size = 4
trim_trailing_whitespace = true

[*.{less,scss}]
indent_style = space
indent_size 2
trim_trailing_whitespace = true

[{package.json,.travis.yml}]
indent_style = space
indent_size = 2
```

[1]: https://github.com/editorconfig

[2]: https://github.com/editorconfig/editorconfig/wiki/EditorConfig-Properties

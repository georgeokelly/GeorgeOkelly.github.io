---
title: Latex 数学公式
tags: 
  - "Latex"
categories: Programming language
keywords: Latex
description: 本文主要作为RISC-V (ISA部分)的一个梳理。
cover: https://picture-1302512218.cos.ap-guangzhou.myqcloud.com/6-27/LaTeX_logo.jpg
date: 2019-01-02 18:23:09
katex: false
---

# 常用数学符号的 LaTeX 表示方法

 1. 指数和下标可以用^和_后加相应字符来实现。比如：
 ![1.png](https://github.com/georgeokelly/hello-world/blob/master/blog_images/2019-1-2/1.png?raw=true)

 2. 平方根（square root）的输入命令为：`\sqrt`，n 次方根相应地为: `\sqrt[n]`。方根符号的大小由LATEX自动加以调整。也可用`\surd` 仅给出符号。比如：
 ![2.png](https://github.com/georgeokelly/hello-world/blob/master/blog_images/2019-1-2/2.png?raw=true)

 3. 命令`\overline` 和`\underline` 在表达式的上、下方画出水平线。比如：
 ![3.png](https://github.com/georgeokelly/hello-world/blob/master/blog_images/2019-1-2/3.png?raw=true)

 4. 命令`\overbrace` 和`\underbrace` 在表达式的上、下方给出一水平的大括号。
 ![4.png](https://github.com/georgeokelly/hello-world/blob/master/blog_images/2019-1-2/4.png?raw=true)

 5. 向量（Vectors）通常用上方有小箭头（arrow symbols）的变量表示。这可由\vec 得到。另两个命令`\overrightarrow` 和`\overleftarrow`在定义从A 到B 的向量时非常有用。
 ![5.png](https://github.com/georgeokelly/hello-world/blob/master/blog_images/2019-1-2/5.png?raw=true)

 6. 分数（fraction）使用`\frac{...}{...}` 排版。一般来说，1/2 这种形式更受欢迎，因为对于少量的分式，它看起来更好些。
 ![6.png](https://github.com/georgeokelly/hello-world/blob/master/blog_images/2019-1-2/6.png?raw=true)

 7. 积分运算符（integral operator）用`\int` 来生成。求和运算符（sum operator）由`\sum` 生成。乘积运算符（product operator）由`\prod` 生成。上限和下限用^ 和_来生成，类似于上标和下标。
 ![7.png](https://github.com/georgeokelly/hello-world/blob/master/blog_images/2019-1-2/7.png?raw=true)

 8. 排版二项系数或类似的结构可以使用命令 `{... \choose ...}` 或`{... \atop ...}`。第二个命令与第一个命令的输出相同，只是没有括号。
 ![8.png](https://github.com/georgeokelly/hello-world/blob/master/blog_images/2019-1-2/8.png?raw=true)

 9. 极限
 ![9.png](https://github.com/georgeokelly/hello-world/blob/master/blog_images/2019-1-2/9.png?raw=true)

# References

[常用数学符号的 LaTeX 表示方法](http://www.mohu.org/info/symbols/symbols.htm)

[一份不太简短的LATEX2e介绍](http://www.mohu.org/info/lshort-cn.pdf)



![](https://img.shields.io/badge/last--updated-2019.1.02-blue.svg)
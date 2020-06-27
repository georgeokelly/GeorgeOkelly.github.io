---
title: Markdown语法(including HTML)
tags: 
  - Markdown
categories: Programming language
keywords: Markdown
description: 本文主要作为Markdown语法的一个简单整理。
cover: https://picture-1302512218.cos.ap-guangzhou.myqcloud.com/6-27/markdown-logo.jpg
date: 2018-12-30 10:18:27
katex: false
---
----

# Markdown 部分

## 标题

在想要设置为标题的文字前面加#来表示
`#`是一级标题，`##`是二级标题，以此类推。支持六级标题。
注：标准语法一般在#后跟个空格再写文字

效果如下：
# <span style='color:DodgerBlue ;'>这是一级标题</span>

## <span style='color:DodgerBlue ;'>这是二级标题</span>

### <span style='color:DodgerBlue ;'>这是三级标题</span>

#### <span style='color:DodgerBlue ;'>这是四级标题</span>

##### <span style='color:DodgerBlue ;'>这是五级标题</span>

###### <span style='color:DodgerBlue ;'>这是六级标题</span>


## 字体

- **加粗**
要加粗的文字左右分别用`**`号包起来
- **斜体**
要倾斜的文字左右分别用`*`号包起来
- **斜体加粗**
要倾斜和加粗的文字左右分别用`***`号包起来
- **删除线**
要加删除线的文字左右分别用`~~`号包起来

效果如下：
**这是加粗的文字**
*这是倾斜的文字*
***这是斜体加粗的文字***
~~这是加删除线的文字~~

## 引用

在引用的文字前加>即可。引用也可以嵌套，如加`>>`,`>>>`,...

效果如下：
>这是引用的内容
>>这是引用的内容
>>> .
>>>
>>> >这是引用的内容


## 分割线

三个或者三个以上的`-`或者`*`都可以。

效果如下：

-----

*****


## 图片

语法：
`![图片alt](图片地址 ''图片title'')`
图片alt就是显示在图片下面的文字，相当于对图片内容的解释。
图片title是图片的标题，当鼠标移到图片上时显示的内容。title可加可不加

示例：
`![picture](https://图片地址 "picture")`

![picture](https://github.com/georgeokelly/hello-world/blob/master/blog_images/20170714133433.jpg?raw=true "picture")

## 超链接

语法：
`[超链接名](超链接地址 "超链接title")`
`title可加可不加`

示例：
`[GitHub](http://github.com)`
`[百度](http://baidu.com)`

[GitHub](http://github.com)

[百度](http://baidu.com)

注：Markdown本身语法不支持链接在新页面中打开，如果想要在新页面中打开的话可以用html语言的a标签代替。

语法：
`<a href="超链接地址" target="_blank">超链接名</a>`

示例：
`<a href="https://github.com" target="_blank">GitHub</a>`

<a href="https://github.com" target="_blank">GitHub</a>

## 列表

**无序列表**

语法：
无序列表用 `- + *` 任何一种都可以

`- 列表内容`
`+ 列表内容`
`* 列表内容`
注意：`- + *` 跟内容之间都要有一个空格

效果如下：

- 列表内容
+ 列表内容
* 列表内容

**有序列表**
语法：
数字加点
`1. 列表内容`
`2. 列表内容`
`3. 列表内容`
注意：序号跟内容之间要有空格

效果如下：

1. 列表内容
2. 列表内容
3. 列表内容

## 表格

语法：

```markdown
表头|表头|表头
---|:--:|---:
内容|内容|内容
内容|内容|内容
```

第二行分割表头和内容。
`-`有一个就行，为了对齐，多加了几个
文字默认居左
- 两边加`:`表示文字居中
- 右边加`:`表示文字居右

注：原生的语法两边都要用` | `包起来。此处省略

示例：

```markdown
姓名|技能|排行
--|:--:|--:
刘备|哭|大哥
关羽|打|二哥
张飞|骂|三弟
```

姓名|技能|排行
--|:--:|--:
刘备|哭|大哥
关羽|打|二哥
张飞|骂|三弟

## 代码

语法：
单行代码：代码之间分别用一个反引号包起来
``代码内容``

代码块：代码之间分别用三个反引号包起来，且两边的反引号单独占一行

```
&acute;&acute;&acute;
  代码...
  代码...
  代码...
&acute;&acute;&acute;
```

注：为了防止转译，前后三个反引号用`&acute;`表示，实际是&acute;，这里只是用来演示。

示例：
单行代码
&acute;create database hero;&acute;
`create database hero;`

代码块

```
&acute;&acute;&acute;
    function fun(){
         echo "这是一句非常牛逼的代码";
    }
    fun();
&acute;&acute;&acute;
```


```
function fun(){
  echo "这是一句非常牛逼的代码";
}
fun();
```

# 补充：HTML

超文本标记语言（英语：HyperText Markup Language，简称：HTML）是一种用于创建网页的标准标记语言。

## 基本格式

开始标签-HTML元素-结束标签，如：

``` html
<html>
  <body>
    <p>
      <span>This is a paragrah.</span>
    </p>
  </body>
</html>
```

- HTML 元素以开始标签起始
- HTML 元素以结束标签终止
- 元素的内容是开始标签与结束标签之间的内容
- 某些 HTML 元素具有空内容（empty content）
- 空元素在开始标签中进行关闭（以开始标签的结束而结束）
- 大多数 HTML 元素可拥有属性
- 使用小写标签。HTML 标签对大小写不敏感：`<P>` 等同于 `<p>`。许多网站都使用大写的 HTML 标签，但在未来 (X)HTML 版本中强制使用小写。

### HTML元素

`<html> `元素定义了整个 HTML 文档。这个元素拥有一个开始标签 `<html>`，以及一个结束标签 `</html>`。元素内容是另一个 HTML 元素（body 元素）。

### body元素

`<body>` 元素定义了 HTML 文档的主体。这个元素拥有一个开始标签 `<body>`，以及一个结束标签 `</body>`。元素内容是另一个 HTML 元素（p 元素）。

### 标题元素

HTML 标题（Heading）是通过 `<h1>` ~ `<h6>` 等标签进行定义的，如：

``` html
<h1>This is a heading</h1>
<h2>This is a heading</h2>
<h3>This is a heading</h3>
```

### p元素

这个 `<p>` 元素定义了 HTML 文档中的一个段落。这个元素拥有一个开始标签 `<p>`，以及一个结束标签 `</p>`，如：This is my first paragraph。

### span元素

`<span>` 用于对文档中的行内元素进行组合。`<span>` 标签没有固定的格式表现。当对它应用样式时，它才会产生视觉上的变化。如果不对 `<span>` 应用样式，那么 `<span>` 元素中的文本与其他文本不会任何视觉上的差异。`<span>` 标签提供了一种**将文本的一部分或者文档的一部分独立出来**的方式。

### img元素

HTML 图像是通过 `<img>` 标签进行定义的，如:
`<img src="xxx.jpg" width="104" height="142" />`
注释：图像的名称和尺寸是以属性的形式提供的。

### a元素

HTML 链接是通过 `<a>` 标签进行定义的，如：

`<a href="http://georgeokelly.github.io">This is a link</a>`

### code元素

`<code>` 标签是一个短语标签，用来定义计算机代码文本。

### 注释元素

`<!-- This is a comment. -->` 注释标签用来在源文档中插入注释。注释不会在浏览器中显示。

### 空元素

没有内容的 HTML 元素被称为空元素。空元素是在开始标签中关闭的。`<br>` 就是没有关闭标签的空元素（`<br>` 标签定义**换行**）。在 XHTML、XML 以及未来版本的 HTML 中，所有元素都必须被关闭。在开始标签中添加斜杠，比如 `<br />`，是关闭空元素的正确方法，HTML、XHTML 和 XML 都接受这种方式。即使 `<br>` 在所有浏览器中都是有效的，但使用 `<br />` 其实是更长远的保障。

## HTML元素属性

HTML 标签可以拥有属性。属性提供了有关 HTML 元素的更多的信息。属性总是以名称/值对的形式出现，比如：`name="value"`。属性总是在 HTML 元素的开始标签中规定。如：

`<a href="http://georgeokelly.github.io">This is a link</a>`
``` html
<p style="font-family:xx;color:xx;font-size:xxpx;text-align:right/center/left">
  This is a paragraph.
</p>
```

# 补充：CSS

<p style="font-family:黑体;color:grey;font-size:20px;text-align:center">待更...</p>

# References

[Markdown基本语法](https://www.jianshu.com/p/191d1e21f7ed)

[HTML相关内容](http://www.w3school.com.cn/html/html_basic.asp)

[HTML字符参考手册](http://www.runoob.com/tags/ref-entities.html)

[HTML颜色名](http://www.runoob.com/html/html-colornames.html)



![](https://img.shields.io/badge/last--updated-2018.12.30-blue.svg)
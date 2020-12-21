---
title: LaTex公式总结
date: 2020-12-21 21:18:10
tags:
  - LaTex
---

本文转载自[简书](https://www.jianshu.com/p/22117d964baf)

### Markdown中的LaTeX格式

大部分情况下，我对于latex的数学公式都是内嵌于Markdown文本编辑器中的，分为两种形式

1. 行内公式排版：



```ruby
$ c = \sqrt{a^{2}+b_{0}^{2}+e^{x}} $
```

![c = \sqrt{a^{2}+b_{0}^{2}+e^{x}}](https://math.jianshu.com/math?formula=c%20%3D%20%5Csqrt%7Ba%5E%7B2%7D%2Bb_%7B0%7D%5E%7B2%7D%2Be%5E%7Bx%7D%7D)

1. 块公式排版：



```ruby
$$ c = \sqrt{a^{2}+b_{0}^{2} +e^{x}} $$
```

![c = \sqrt{a^{2}+b_{0}^{2} +e^{x}}](https://math.jianshu.com/math?formula=c%20%3D%20%5Csqrt%7Ba%5E%7B2%7D%2Bb_%7B0%7D%5E%7B2%7D%20%2Be%5E%7Bx%7D%7D)

### LaTex的数学公式基本规则

1. 转义

> 以下几个字符: # $ % & ~ _ ^ \ { }有特殊意义，需要表示这些字符时，需要转义，即在每个字符前加上\（转义字符的具体含义下面会解释）
> ![](/image/LaTex/1.jpg)

>
> 不知为何简书不支持~的表示，需要时写作\sim

> \boxed命令给公式加一个方框。\fbox具有类似功能
>  \mathbf将字体加粗
>  \boldsymbol将字体斜体且加粗



```ruby
$E = mc^2 $
$ \boxed{E=mc^2} $
$\fbox{E=mc^2}$
$\mathbf{E = mc^2}$
$\boldsymbol{E = mc^2}$
```

![E = mc^2](https://math.jianshu.com/math?formula=E%20%3D%20mc%5E2)
 ![\boxed{E=mc^2}](https://math.jianshu.com/math?formula=%5Cboxed%7BE%3Dmc%5E2%7D)
 ![\fbox{E=mc^2}](https://math.jianshu.com/math?formula=%5Cfbox%7BE%3Dmc%5E2%7D)
 ![\mathbf{E = mc^2}](https://math.jianshu.com/math?formula=%5Cmathbf%7BE%20%3D%20mc%5E2%7D)
 ![\boldsymbol{E = mc^2}](https://math.jianshu.com/math?formula=%5Cboldsymbol%7BE%20%3D%20mc%5E2%7D)

1. 希腊字母与特殊字符

   

![](/image/LaTex/2.jpg)

![](/image/LaTex/3.jpg)

![](/image/LaTex/4.jpg)


1. 上下标及常用符号

> 用^来表示上标
>  用_来表示下标
>  根号用\sqrt表示

注意：这些有特殊含义的表示中（不限于这三种内容），如果超过一个字符或符号，需要用{}括起来。

对角标的使用中，如果角标位置看起来不明显时，可以强制改变角标大小或层次，如下所示：



```ruby
$y_N, y_{_N},$
```

显示为：
 ![y_N, y_{_N},](https://math.jianshu.com/math?formula=y_N%2C%20y_%7B_N%7D%2C)

第一种输出为正常输出，但输出效果不明显；第二种是将一级角标改为二级角标，字体也自动变为二级角标字体

当需要在左右两边都有上下标，可以用 \sideset 命令



```ruby
$$ \sideset{^1_2}{^3_4}\bigotimes $$
```

![\sideset{^1_2}{^3_4}\bigotimes](https://math.jianshu.com/math?formula=%5Csideset%7B%5E1_2%7D%7B%5E3_4%7D%5Cbigotimes)

\sqrt[开方次数，默认为2]{开方公式}， 例如：



```cpp
$$ x_{k}^2\quad \sqrt{x}\quad \sqrt[3]{x+y} $$
```

![x_{k}^2\quad \sqrt{x}\quad \sqrt[3]{x+y}](https://math.jianshu.com/math?formula=x_%7Bk%7D%5E2%5Cquad%20%5Csqrt%7Bx%7D%5Cquad%20%5Csqrt%5B3%5D%7Bx%2By%7D)

> 公式中插入文本\text{}或\mbox{}，建议使用前者，它对于latex内置的\mbox{}的优势在于它可以根据当前位置自动调整文本样式（尤其是大小），而\mbox采用统一的正文样式。这一点在你的文本被用作上下标的时候非常重要。

![y = x^2 \mbox{(二次函数)}](https://math.jianshu.com/math?formula=y%20%3D%20x%5E2%20%5Cmbox%7B(%E4%BA%8C%E6%AC%A1%E5%87%BD%E6%95%B0)%7D)

> 公式中插入空格，间隔效果如下表：



![](/image/LaTex/5.jpg)

> 若需要显示更大或更小的字符，在符号前插入 \large 或 \small 命令



```ruby
$A\large  A  \small A$
```

![A\large A \small A](https://math.jianshu.com/math?formula=A%5Clarge%20A%20%5Csmall%20A)

> 省略号\dots, \cdots,\vdots \ddots表示，\cdot常表示点乘，\vots是竖直方向的，\ddots是斜线方向的



```ruby
$$ x_1, x_2, \dots, x_n\quad 1,2,\cdots,n\quad \vdots\quad \ddots $$
```

![x_1, x_2, \dots, x_n\quad 1,2,\cdots,n\quad \vdots\quad \ddots](https://math.jianshu.com/math?formula=x_1%2C%20x_2%2C%20%5Cdots%2C%20x_n%5Cquad%201%2C2%2C%5Ccdots%2Cn%5Cquad%20%5Cvdots%5Cquad%20%5Cddots)

> 关系符如下表所示

![](/image/LaTex/6.jpg)

> 其他常用符号

![](/image/LaTex/7.jpg)



1. 分数与组合数

> 分数用\frac{分子}{分母}表示，\cfrac用于连分数表示(这样相较于\frac不会产生字体自动缩小的问题)



```ruby
$x = a _ 0 + \cfrac {1} {a _ 1
          + \cfrac {1} {a _ 2
          + \cfrac {1} {a _ 3 + \cfrac {1} {a _ 4} } } } 
$
```

![x = a _ 0 + \cfrac {1} {a _ 1 + \cfrac {1} {a _ 2 + \cfrac {1} {a _ 3 + \cfrac {1} {a _ 4} } } }](https://math.jianshu.com/math?formula=x%20%3D%20a%20_%200%20%2B%20%5Ccfrac%20%7B1%7D%20%7Ba%20_%201%20%2B%20%5Ccfrac%20%7B1%7D%20%7Ba%20_%202%20%2B%20%5Ccfrac%20%7B1%7D%20%7Ba%20_%203%20%2B%20%5Ccfrac%20%7B1%7D%20%7Ba%20_%204%7D%20%7D%20%7D%20%7D)

字号工具环境设置：
 \dfrac命令把字号设置为独立公式中的大小；
 \tfrac则把字号设置为行间公式中的大小。



```ruby
$ \frac{1}{2} \dfrac{1}{2} $ 
```

![\frac{1}{2} \dfrac{1}{2}](https://math.jianshu.com/math?formula=%5Cfrac%7B1%7D%7B2%7D%20%5Cdfrac%7B1%7D%7B2%7D)



```ruby
$$ \frac{1}{2} \tfrac{1}{2} $$ 
```

![\frac{1}{2} \tfrac{1}{2}](https://math.jianshu.com/math?formula=%5Cfrac%7B1%7D%7B2%7D%20%5Ctfrac%7B1%7D%7B2%7D)

> 组合数用法与分数类似，在命令前加d和t也能达到分数字号设置同样的功能

\binom{233}{x^2}![\binom{233}{x^2}](https://math.jianshu.com/math?formula=%5Cbinom%7B233%7D%7Bx%5E2%7D)

1. 运算符

> 基本加减乘除等于 +-*/= 可以直接输入
>  特殊运算则用以下特殊命令\pm; \times; \div; \cdot; \cap; \cup; \geq; \leq; \neq; \approx; \equiv
>  ![\pm\; \times\; \div\; \cdot\; \cap\; \cup\; \geq\; \leq\; \neq\; \approx\; \equiv](https://math.jianshu.com/math?formula=%5Cpm%5C%3B%20%5Ctimes%5C%3B%20%5Cdiv%5C%3B%20%5Ccdot%5C%3B%20%5Ccap%5C%3B%20%5Ccup%5C%3B%20%5Cgeq%5C%3B%20%5Cleq%5C%3B%20%5Cneq%5C%3B%20%5Capprox%5C%3B%20%5Cequiv)
>  在此之上，如果想要使用堆积符号有以下三种形式

- \stackrel{上位符号}{基位符号} 基位符号大，上位符号小
- {上位公式\atop 下位公式} 上下符号一样大
- {上位公式\choose 下位公式} 上下符号一样大；上下符号被包括在圆弧内

![\vec{x}\stackrel{\mathrm{def}}{=}{x_1,\dots,x_n}\\ \sum_{k_0,k_1,\ldots>0 \atop k_0+k_1+\cdots=n}\\A_{k_0}A_{k_1}\cdots{n+1 \choose k}={n \choose k}+{n \choose k-1}\\](https://math.jianshu.com/math?formula=%5Cvec%7Bx%7D%5Cstackrel%7B%5Cmathrm%7Bdef%7D%7D%7B%3D%7D%7Bx_1%2C%5Cdots%2Cx_n%7D%5C%5C%20%5Csum_%7Bk_0%2Ck_1%2C%5Cldots%3E0%20%5Catop%20k_0%2Bk_1%2B%5Ccdots%3Dn%7D%5C%5CA_%7Bk_0%7DA_%7Bk_1%7D%5Ccdots%7Bn%2B1%20%5Cchoose%20k%7D%3D%7Bn%20%5Cchoose%20k%7D%2B%7Bn%20%5Cchoose%20k-1%7D%5C%5C)

> 和、积、极限、积分等运算符用\sum, \prod, \lim, \int,这些公式在行内公式被压缩，以适应行高，可以通过\limits和\nolimits命令其是否压缩。
>  ![\sum, \prod, \lim, \int](https://math.jianshu.com/math?formula=%5Csum%2C%20%5Cprod%2C%20%5Clim%2C%20%5Cint)



```ruby
$ \sum_{i=1}^n i $

$ \prod_{i=1}^n i$

$\lim_{x\to0}x^2 $

$\int_{a}^{b}x^2 dx $

$\sum\nolimits_{i=1}^n i \quad\prod\nolimits_{i=1}^n i
\quad
\lim\nolimits_{x\to0}x^2 \quad\int\limits_{a}^{b}x^2 dx 
$
```

![\sum_{i=1}^n i \quad \prod_{i=1}^n \quad\lim_{x\to0}x^2 \quad\int_{a}^{b}x^2 dx](https://math.jianshu.com/math?formula=%5Csum_%7Bi%3D1%7D%5En%20i%20%5Cquad%20%5Cprod_%7Bi%3D1%7D%5En%20%5Cquad%5Clim_%7Bx%5Cto0%7Dx%5E2%20%5Cquad%5Cint_%7Ba%7D%5E%7Bb%7Dx%5E2%20dx)
 ![\sum\nolimits_{i=1}^n i \quad\prod\nolimits_{i=1}^n i \quad \lim\nolimits_{x\to0}x^2 \quad\int\limits_{a}^{b}x^2 dx](https://math.jianshu.com/math?formula=%5Csum%5Cnolimits_%7Bi%3D1%7D%5En%20i%20%5Cquad%5Cprod%5Cnolimits_%7Bi%3D1%7D%5En%20i%20%5Cquad%20%5Clim%5Cnolimits_%7Bx%5Cto0%7Dx%5E2%20%5Cquad%5Cint%5Climits_%7Ba%7D%5E%7Bb%7Dx%5E2%20dx)

1. 积分及箭头

> 积分符号使用如下形式：\int、\iint、\iiint、\iiiint、\idotsint



```cpp
$$ \int \int \quad \int \int \int \quad 
 \int \int \int \int \quad \int \dots（表示...） \int $$

$$ \iint \quad \iiint \quad \iiiint \quad \idotsint $$
```

![\int \int \quad \int \int \int \quad \int \int \int \int \quad \int \dots \int](https://math.jianshu.com/math?formula=%5Cint%20%5Cint%20%5Cquad%20%5Cint%20%5Cint%20%5Cint%20%5Cquad%20%5Cint%20%5Cint%20%5Cint%20%5Cint%20%5Cquad%20%5Cint%20%5Cdots%20%5Cint)

![\iint \quad \iiint \quad \iiiint \quad \idotsint](https://math.jianshu.com/math?formula=%5Ciint%20%5Cquad%20%5Ciiint%20%5Cquad%20%5Ciiiint%20%5Cquad%20%5Cidotsint)

> 箭头的种类过于繁多，这里不就一一列举了，但是箭头符号的名字很有规律，一般命名规则为方向+箭头种类：

|        代码         |                             显示                             |
| :-----------------: | :----------------------------------------------------------: |
| \leftarrow 或 \gets |   ![\gets](https://math.jianshu.com/math?formula=%5Cgets)    |
| \rightarrow 或 \to  | ![\,\to](https://math.jianshu.com/math?formula=%5C%2C%5Cto)  |
|      \uparrow       | ![\uparrow](https://math.jianshu.com/math?formula=%5Cuparrow) |
|     \downarrow      | ![\downarrow](https://math.jianshu.com/math?formula=%5Cdownarrow) |

> > 四个基本方向上下左右不多说，斜着的箭头的方向部分为\ne \se \nw \sw，为东北、东南、西北、西南简写
> >  示例：\nearrow ![\nearrow](https://math.jianshu.com/math?formula=%5Cnearrow)

> > 还有左右、上下两个方向的箭头：
> >  \leftrightarrow   ![\leftrightarrow](https://math.jianshu.com/math?formula=%5Cleftrightarrow)
> >  \updownarrow  ![\updownarrow](https://math.jianshu.com/math?formula=%5Cupdownarrow)

> > 上面的箭头只有一根线，可以通过大写第一个字母变成双线：
> >  \Leftrightarrow  ![\Leftrightarrow](https://math.jianshu.com/math?formula=%5CLeftrightarrow)
> >  \Uparrow  ![\Uparrow](https://math.jianshu.com/math?formula=%5CUparrow)

> > 在前面加上long可以把箭头变长，仅适用于左右箭头，注意双线箭头大写字母的位置：
> >  \longleftarrow  ![\longleftarrow](https://math.jianshu.com/math?formula=%5Clongleftarrow)
> >  \longleftrightarrow  ![\longleftrightarrow](https://math.jianshu.com/math?formula=%5Clongleftrightarrow)
> >  \Longleftrightarrow 或 \iff ![\iff](https://math.jianshu.com/math?formula=%5Ciff)

> > 还有只有一边的箭头，名字为harpoon+up/down，表示那一边的位置，这种箭头似乎不支持上面的buff buffbuff，但拥有左右双向箭头：
> >  \leftharpoonup  ![\leftharpoonup](https://math.jianshu.com/math?formula=%5Cleftharpoonup)
> >  \rightleftharpoons  ![\rightleftharpoons](https://math.jianshu.com/math?formula=%5Crightleftharpoons)

> > 意图在箭头上下位置添加说明时，有以下两种方式



```ruby
1.\xleftarrow和\xrightarrow可根据内容自动调整（上下都可以添加内容）

2.\overset{}{\rightarrow}则是长度固定的箭头，适合单个字符，内容在上
  \overrightarrow{}则是长度变化的箭头，适合多个字符，箭头在上
注意2中的都只适用于只在箭头上或下添加内容，改变位置将over改为under

$\xleftarrow[x+y+z]{x+y+z+1}$

$\xrightarrow[x+y+z+1]{x+y+z}$

$\overset{x+y+z}{\rightarrow}$

 $\underrightarrow{x+y+z}$

$\underset{x+y+z}{\leftarrow}$

 $\overleftarrow{x+y+z}$
```

![\xleftarrow[x+y+z]{x+y+z+1}](https://math.jianshu.com/math?formula=%5Cxleftarrow%5Bx%2By%2Bz%5D%7Bx%2By%2Bz%2B1%7D)

![\xrightarrow[x+y+z+1]{x+y+z}](https://math.jianshu.com/math?formula=%5Cxrightarrow%5Bx%2By%2Bz%2B1%5D%7Bx%2By%2Bz%7D)

![\overset{x+y+z}{\rightarrow}](https://math.jianshu.com/math?formula=%5Coverset%7Bx%2By%2Bz%7D%7B%5Crightarrow%7D)

![\underrightarrow{x+y+z}](https://math.jianshu.com/math?formula=%5Cunderrightarrow%7Bx%2By%2Bz%7D)

![\underset{x+y+z}{\leftarrow}](https://math.jianshu.com/math?formula=%5Cunderset%7Bx%2By%2Bz%7D%7B%5Cleftarrow%7D)

![\overleftarrow{x+y+z}](https://math.jianshu.com/math?formula=%5Coverleftarrow%7Bx%2By%2Bz%7D)

1. 注音和标注



```ruby
字母上方横线\overline{}, \bar{}
$ \bar{x} $ 单个字符 
$\overline{xyz}$ 多个字符
```

![\bar{x}](https://math.jianshu.com/math?formula=%5Cbar%7Bx%7D)
 ![\overline{xyz}](https://math.jianshu.com/math?formula=%5Coverline%7Bxyz%7D)



```ruby
字母下方横线\underline{}
$\underline{xyz}$
```

![\underline{xyz}](https://math.jianshu.com/math?formula=%5Cunderline%7Bxyz%7D)



```ruby
\overbrace 和\underbrace 在表达式的上、下方给出一水平的大括号
$\overbrace{a\dots a}^{n个}$ 
$\underbrace{a\dots a}_{n个}$ 
```

![\overbrace{a\dots a}^{n个}](https://math.jianshu.com/math?formula=%5Coverbrace%7Ba%5Cdots%20a%7D%5E%7Bn%E4%B8%AA%7D)

![\underbrace{a\dots a}_{n个}](https://math.jianshu.com/math?formula=%5Cunderbrace%7Ba%5Cdots%20a%7D_%7Bn%E4%B8%AA%7D)



```ruby
向量表示有两种，单个字母用\vec{x}，多个字母用\overrightarrow 和\overleftarrow
$ \vec{x}$
$\overrightarrow{AB}$
$\overleftarrow{ABC}$
```

![\vec{x}](https://math.jianshu.com/math?formula=%5Cvec%7Bx%7D)
 ![\overrightarrow{AB}](https://math.jianshu.com/math?formula=%5Coverrightarrow%7BAB%7D)
 ![\overleftarrow{ABC}](https://math.jianshu.com/math?formula=%5Coverleftarrow%7BABC%7D)



```ruby
字母上方波浪线\tilde{}, \widetilde{}
$ \tilde{x}$
$ \widetilde{xyz}$
```

![\tilde{x}](https://math.jianshu.com/math?formula=%5Ctilde%7Bx%7D)
 ![\widetilde{xyz}](https://math.jianshu.com/math?formula=%5Cwidetilde%7Bxyz%7D)



```ruby
$ \dot{x}$
```

![\dot{x}](https://math.jianshu.com/math?formula=%5Cdot%7Bx%7D)



```ruby
$ \hat{x}$ 
$ \widehat{xyz}$ 
```

![\hat{x}](https://math.jianshu.com/math?formula=%5Chat%7Bx%7D)
 ![\widehat{xyz}](https://math.jianshu.com/math?formula=%5Cwidehat%7Bxyz%7D)



```ruby
$ \acute{x}$
```

![\acute{x}](https://math.jianshu.com/math?formula=%5Cacute%7Bx%7D)



```ruby
$ \grave{x} $
```

![\grave{x}](https://math.jianshu.com/math?formula=%5Cgrave%7Bx%7D)



```ruby
$ \mathring{x}$
```

![\mathring{x}](https://math.jianshu.com/math?formula=%5Cmathring%7Bx%7D)



```ruby
$ \ddot{x}$
```

![\ddot{x}](https://math.jianshu.com/math?formula=%5Cddot%7Bx%7D)



```ruby
$ \check{x} $
```

![\check{x}](https://math.jianshu.com/math?formula=%5Ccheck%7Bx%7D)



```ruby
$ \breve{x}$
```

![\breve{x}](https://math.jianshu.com/math?formula=%5Cbreve%7Bx%7D)



```ruby
$ \dddot{x} $
```

![\dddot{x}](https://math.jianshu.com/math?formula=%5Cdddot%7Bx%7D)

1. 编号

> 插入编号：
>  使用\tag指令指定公式的具体编号，并使用\label指令埋下锚点。如y=x^2 \tag{1.5a}

![y=x^2 \tag{1.5a}](https://math.jianshu.com/math?formula=y%3Dx%5E2%20%5Ctag%7B1.5a%7D)

> 引用编号：
>  在markdown中不支持编号的锚点设置 \label与\ref 的使用

1. 括号及嵌套

常用括号有以下几种



![img](https:////upload-images.jianshu.io/upload_images/9368615-9db47069b8031901.png?imageMogr2/auto-orient/strip|imageView2/2/w/959/format/webp)



> 括号的大小调整

对括号的大小，手动模式可以用\big, \Big, \bigg, \Bigg等一系列命令(从小到大，默认是最小)放在上述括号前面调整大小。



```ruby
$\Bigg( \bigg( \Big( \big((x) \big) \Big) \bigg) \Bigg)$
$\Bigg\{ \bigg\{ \Big\{ \big\{\{x\} \big\} \Big\} \bigg\} \Bigg\}$
```

![\Bigg( \bigg( \Big( \big((x) \big) \Big) \bigg) \Bigg)\quad \Bigg\{ \bigg\{ \Big\{ \big\{\{x\} \big\} \Big\} \bigg\} \Bigg\}](https://math.jianshu.com/math?formula=%5CBigg(%20%5Cbigg(%20%5CBig(%20%5Cbig((x)%20%5Cbig)%20%5CBig)%20%5Cbigg)%20%5CBigg)%5Cquad%20%5CBigg%5C%7B%20%5Cbigg%5C%7B%20%5CBig%5C%7B%20%5Cbig%5C%7B%5C%7Bx%5C%7D%20%5Cbig%5C%7D%20%5CBig%5C%7D%20%5Cbigg%5C%7D%20%5CBigg%5C%7D)
 自动模式下要用 \left 和 \right 命令后面跟上所需分隔符，用来创建自动匹配高度的 (圆括号)，[方括号] 和 {花括号} 等分隔符



```ruby
$$ f(x,y,z) = 3y^2z \left( 3+\frac{7x+5}{1+y^2} \right) $$
```

![f(x,y,z) = 3y^2z \left( 3+\frac{7x+5}{1+y^2} \right)](https://math.jianshu.com/math?formula=f(x%2Cy%2Cz)%20%3D%203y%5E2z%20%5Cleft(%203%2B%5Cfrac%7B7x%2B5%7D%7B1%2By%5E2%7D%20%5Cright))



```swift
$$
f\left(
   \left[ 
     \frac{
       1+\left\{x,y\right\}
     }{
       \left(
          \frac{x}{y}+\frac{y}{x}
       \right)
       \left(u+1\right)
     }+a
   \right]^{3/2}
\right)
\tag{1.2}
$$
```

![f\left( \left[ \frac{ 1+\left\{x,y\right\} }{ \left( \frac{x}{y}+\frac{y}{x} \right) \left(u+1\right) }+a \right]^{3/2} \right) \tag{行标}](https://math.jianshu.com/math?formula=f%5Cleft(%20%5Cleft%5B%20%5Cfrac%7B%201%2B%5Cleft%5C%7Bx%2Cy%5Cright%5C%7D%20%7D%7B%20%5Cleft(%20%5Cfrac%7Bx%7D%7By%7D%2B%5Cfrac%7By%7D%7Bx%7D%20%5Cright)%20%5Cleft(u%2B1%5Cright)%20%7D%2Ba%20%5Cright%5D%5E%7B3%2F2%7D%20%5Cright)%20%5Ctag%7B%E8%A1%8C%E6%A0%87%7D)
 如果你需要在不同的行显示对应括号，可以在每一行对应处使用 \left. 或 \right. 来放一个"影子"括号：



```ruby
$$
\begin{aligned}
a=&\left(1+2+3+  \cdots \right. \\
& \cdots+ \left. \infty-2+\infty-1+\infty\right)
\end{aligned}
$$
```

![\begin{aligned} a=&\left(1+2+3+ \cdots \right. \\ & \cdots+ \left. \infty-2+\infty-1+\infty\right) \end{aligned}](https://math.jianshu.com/math?formula=%5Cbegin%7Baligned%7D%20a%3D%26%5Cleft(1%2B2%2B3%2B%20%5Ccdots%20%5Cright.%20%5C%5C%20%26%20%5Ccdots%2B%20%5Cleft.%20%5Cinfty-2%2B%5Cinfty-1%2B%5Cinfty%5Cright)%20%5Cend%7Baligned%7D)

而如果分隔符不在左右而在中间，为了能够格式对齐，要用\middle加分隔符表示



```swift
$P=\left(A=2\middle|\frac{A^2}{B}>4\right)$
```

![P=\left(A=2\middle|\frac{A^2}{B}>4\right)](https://math.jianshu.com/math?formula=P%3D%5Cleft(A%3D2%5Cmiddle%7C%5Cfrac%7BA%5E2%7D%7BB%7D%3E4%5Cright))

1. 颜色设置

![img](https:////upload-images.jianshu.io/upload_images/9368615-2fbc10b9e7ecae71.png?imageMogr2/auto-orient/strip|imageView2/2/w/963/format/webp)



1. 原文照排

> LATEX 提供了 \verb 命令(一般用于在正文中插入较短的命令)来实现



```ruby
$\verb|x^2|\quad x^2$
```

![\verb|x^2|\quad x^2](https://math.jianshu.com/math?formula=%5Cverb%7Cx%5E2%7C%5Cquad%20x%5E2)

1. 删除线

> 在公式内使用 \require{cancel} 来允许 片段删除线 的显示。
>  声明片段删除线后，使用 \cancel{字符}、\bcancel{字符}、\xcancel{字符} 和 \cancelto{字符} 来实现各种片段删除线效果。



```ruby
\require{cancel}\begin{array}{rl}
\verb|y+\cancel{x}| & y+\cancel{x}\\
\verb|\cancel{y+x}| & \cancel{y+x}\\
\verb|y+\bcancel{x}| & y+\bcancel{x}\\
\verb|y+\xcancel{x}| & y+\xcancel{x}\\
\verb|y+\cancelto{0}{x}| & y+\cancelto{0}{x}\\
\verb+\frac{1\cancel9}{\cancel95} = \frac15+& \frac{1\cancel9}{\cancel95} = \frac15 \\
\end{array}
```

![\require{cancel}\begin{array}{rl} \verb|y+\cancel{x}| & y+\cancel{x}\\ \verb|\cancel{y+x}| & \cancel{y+x}\\ \verb|y+\bcancel{x}| & y+\bcancel{x}\\ \verb|y+\xcancel{x}| & y+\xcancel{x}\\ \verb|y+\cancelto{0}{x}| & y+\cancelto{0}{x}\\ \verb+\frac{1\cancel9}{\cancel95} = \frac15+& \frac{1\cancel9}{\cancel95} = \frac15 \\ \end{array}](https://math.jianshu.com/math?formula=%5Crequire%7Bcancel%7D%5Cbegin%7Barray%7D%7Brl%7D%20%5Cverb%7Cy%2B%5Ccancel%7Bx%7D%7C%20%26%20y%2B%5Ccancel%7Bx%7D%5C%5C%20%5Cverb%7C%5Ccancel%7By%2Bx%7D%7C%20%26%20%5Ccancel%7By%2Bx%7D%5C%5C%20%5Cverb%7Cy%2B%5Cbcancel%7Bx%7D%7C%20%26%20y%2B%5Cbcancel%7Bx%7D%5C%5C%20%5Cverb%7Cy%2B%5Cxcancel%7Bx%7D%7C%20%26%20y%2B%5Cxcancel%7Bx%7D%5C%5C%20%5Cverb%7Cy%2B%5Ccancelto%7B0%7D%7Bx%7D%7C%20%26%20y%2B%5Ccancelto%7B0%7D%7Bx%7D%5C%5C%20%5Cverb%2B%5Cfrac%7B1%5Ccancel9%7D%7B%5Ccancel95%7D%20%3D%20%5Cfrac15%2B%26%20%5Cfrac%7B1%5Ccancel9%7D%7B%5Ccancel95%7D%20%3D%20%5Cfrac15%20%5C%5C%20%5Cend%7Barray%7D)

### 数学公式高级规则

1. 公式环境与对齐
    在下文的公式环境中，形如 equation*中， 带*号的不将公式标号排出来, 而不带*的自动给每行式子编排标号
    latex的公式环境有很多种，这里只列举我常用的，更多请查阅latex官方说明

> 单行公式环境 equation
>  \begin{equation}
>  ...
>  \end{equation}
>  是最一般的公式环境，表示一个公式，默认情况下之表示一个单行的公式，但是它的功能可以通过内嵌各种其他环境进行扩展，不可以使用\\与&功能。它可以内嵌的一些关于对齐的环境将在后面介绍。



```ruby
$\begin{equation}
f(x)=3x^{2}+6(x-2)-1
\end{equation}$
```

![\begin{equation} f(x)=3x^{2}+6(x-2)-1 \end{equation}](https://math.jianshu.com/math?formula=%5Cbegin%7Bequation%7D%20f(x)%3D3x%5E%7B2%7D%2B6(x-2)-1%20%5Cend%7Bequation%7D)

> align（多个公式）
>  这是最基本的对齐环境，其他多公式环境都不同程度地依赖它。它采用“&”分割各个对齐单元，使用“\\”换行。它的每行是一个公式，都会独立编号。在排版过程中，它将&分出来的列又分成组，组间特定方式排版，具体方式在flalign环境中讨论。
>  通常情况下在公式中可以通过\tag设置标号，\label设置引用名称，但好像简书的markdown不支持\label，所以编号的意义就无所谓了。



```ruby
$\begin{align}
 f(x) &= (x+a)(x+b) \\
 &= x^2 + (a+b)x + ab \tag{1.1}
\end{align}$
```

![\begin{align} f(x) &= (x+a)(x+b) \\ &= x^2 + (a+b)x + ab \tag{1.1} \end{align}](https://math.jianshu.com/math?formula=%5Cbegin%7Balign%7D%20f(x)%20%26%3D%20(x%2Ba)(x%2Bb)%20%5C%5C%20%26%3D%20x%5E2%20%2B%20(a%2Bb)x%20%2B%20ab%20%5Ctag%7B1.1%7D%20%5Cend%7Balign%7D)



```ruby
$\begin{align}
A_{1}&=B_{1}B_{2} & A_{3} & = B_{1}\\
A_{2}&=B_{3}& A_{3}A_{4} & = B_{4}
\end{align}$
```

![\begin{align} A_{1}&=B_{1}B_{2} & A_{3} & = B_{1}\\ A_{2}&=B_{3}& A_{3}A_{4} & = B_{4} \end{align}](https://math.jianshu.com/math?formula=%5Cbegin%7Balign%7D%20A_%7B1%7D%26%3DB_%7B1%7DB_%7B2%7D%20%26%20A_%7B3%7D%20%26%20%3D%20B_%7B1%7D%5C%5C%20A_%7B2%7D%26%3DB_%7B3%7D%26%20A_%7B3%7DA_%7B4%7D%20%26%20%3D%20B_%7B4%7D%20%5Cend%7Balign%7D)

> 公式组环境flalign与align的功能基本相同，唯一区别是列对之间的距离为弹性宽度，以使公式组两端对齐。然而简书不支持。。。

> gather环境
>  它是最简单的多行公式环境，自己不提供任何对齐。其中的各行公式按照全局方式分别对齐。
>  在设置了全局左对齐之后，因为不存在内部各个公式之间对排版的干扰，这种环境非常适合写数学推导或者证明。而默认情况下，是居中对齐。



```python
\begin{gather*}
E(X)=\lambda    \qquad  D(X)=\lambda    \\
E(\bar{X})=\lambda  \\
D(\bar{X})=\frac{\lambda}{n}    \\
E(S^2)=\frac{n-1}{n}\lambda \\
\end{gather*}
```

![\begin{gather} \because E(X)=\lambda \qquad D(X)=\lambda \\ \therefore E(\bar{X})=\lambda \\ D(\bar{X})=\frac{\lambda}{n} \\ E(S^2)=\frac{n-1}{n}\lambda \\ \end{gather}](https://math.jianshu.com/math?formula=%5Cbegin%7Bgather%7D%20%5Cbecause%20E(X)%3D%5Clambda%20%5Cqquad%20D(X)%3D%5Clambda%20%5C%5C%20%5Ctherefore%20E(%5Cbar%7BX%7D)%3D%5Clambda%20%5C%5C%20D(%5Cbar%7BX%7D)%3D%5Cfrac%7B%5Clambda%7D%7Bn%7D%20%5C%5C%20E(S%5E2)%3D%5Cfrac%7Bn-1%7D%7Bn%7D%5Clambda%20%5C%5C%20%5Cend%7Bgather%7D)

> 与上文中公式组类似的，长公式也有两种multline和split两种环境，分别对应不对齐与对齐。

multline不支持“&”分列。其首行左对齐，末行右对齐，其余各行分别按照全局方式对齐。
 split也用于排版多行公式，但它与多行公式环境multline的区别主要是以下三点：

- 用&作为分列符，但至多两列；左列右对齐，右列左对齐，形成一个列对，可使多行公式关于某个符号垂直对齐。因此用它排版的多行公式更为整齐美观。如果不用分列符&,所有公式行为一列，且全都与首行公式的右端对齐。
- 必须置于除multline环境之外的其它公式环境中。
- 自身并不生成公式序号，而是由外在公式环境提供，序号垂直居中。

示例如下：



```ruby
$$\begin{multline}
x=a+b+c+{} \\
d+e+f+g
\end{multline}$$
```

![\begin{multline} x=a+b+c+{} \\ d+e+f+g \end{multline}](https://math.jianshu.com/math?formula=%5Cbegin%7Bmultline%7D%20x%3Da%2Bb%2Bc%2B%7B%7D%20%5C%5C%20d%2Be%2Bf%2Bg%20%5Cend%7Bmultline%7D)



```ruby
$$\begin{split}
x=&a+b+c+{} \\
&d+e+f+g
\end{split}$$
```

![\begin{split} x=&a+b+c+{} \\ &d+e+f+g \end{split}](https://math.jianshu.com/math?formula=%5Cbegin%7Bsplit%7D%20x%3D%26a%2Bb%2Bc%2B%7B%7D%20%5C%5C%20%26d%2Be%2Bf%2Bg%20%5Cend%7Bsplit%7D)

> /gathered，/aligned。以上几种方程组环境，无论每个公式多小，都会占满一行。而/gathered，/aligned环境，则只占据公式的实际宽度，整体作为一个特大的符号与其他符号一同处理。
>  这个结构还可以添加位置参数，以决定与其他符号的竖直对齐方式(b,t)。而且这种环境不再具有自动编号功能。



```ruby
$\begin{equation}
 \left.\begin{aligned}
        B'&=-\partial \times E,\\
        E'&=\partial \times B - 4\pi j,
       \end{aligned}
 \right\}
 \qquad \text{Maxwell's equations}
\end{equation}
$
```

![\begin{equation} \left.\begin{aligned} B'&=-\partial \times E,\\ E'&=\partial \times B - 4\pi j, \end{aligned} \right\} \qquad \text{Maxwell's equations} \end{equation}](https://math.jianshu.com/math?formula=%5Cbegin%7Bequation%7D%20%5Cleft.%5Cbegin%7Baligned%7D%20B'%26%3D-%5Cpartial%20%5Ctimes%20E%2C%5C%5C%20E'%26%3D%5Cpartial%20%5Ctimes%20B%20-%204%5Cpi%20j%2C%20%5Cend%7Baligned%7D%20%5Cright%5C%7D%20%5Cqquad%20%5Ctext%7BMaxwell's%20equations%7D%20%5Cend%7Bequation%7D)



```ruby
$\begin{equation}
\left.
\begin{aligned}
x+y &> 5 \\
y-y &> 11
\end{aligned}
\ \right\}\Rightarrow x^2 - y^2 > 55
\end{equation}$
```

![\begin{equation} \left. \begin{aligned} x+y &> 5 \\ y-y &> 11 \end{aligned} \ \right\}\Rightarrow x^2 - y^2 > 55 \end{equation}](https://math.jianshu.com/math?formula=%5Cbegin%7Bequation%7D%20%5Cleft.%20%5Cbegin%7Baligned%7D%20x%2By%20%26%3E%205%20%5C%5C%20y-y%20%26%3E%2011%20%5Cend%7Baligned%7D%20%5C%20%5Cright%5C%7D%5CRightarrow%20x%5E2%20-%20y%5E2%20%3E%2055%20%5Cend%7Bequation%7D)

> cases环境常用于分段函数，上面的环境虽然支持分段函数形式的表达，但比较繁琐



```ruby
$$
L(Y,f(X))=
\begin{cases}
1,\quad &Y\neq f(X)\\
0,\quad &Y=f(X)
\end{cases}
$$
```

![L(Y,f(X))= \begin{cases} 1,\quad &Y\neq f(X)\\ 0,\quad &Y=f(X) \end{cases}](https://math.jianshu.com/math?formula=L(Y%2Cf(X))%3D%20%5Cbegin%7Bcases%7D%201%2C%5Cquad%20%26Y%5Cneq%20f(X)%5C%5C%200%2C%5Cquad%20%26Y%3Df(X)%20%5Cend%7Bcases%7D)

> 阵列环境 array可以帮助我们输入一个数组或表格
>  通常，一个格式化后的表格比单纯的文字或排版后的文字更具有可读性。数组和表格均以 begin{array} 开头，并在其后定义列数及每一列的文本对齐属性，字母c l r 分别代表居中、左对齐及右对齐。若需要插入垂直分割线，在定义式中插入 | ，若要插入水平分割线，在下一行输入前插入 \hline 。与矩阵相似，每行元素间均须要插入 & ，每行元素以 \ 结尾，最后以 end{array} 结束数组。下面是几个示例：



```ruby
$\begin{array}{c|l|c|r}
n & \text{左对齐} & \text{居中对齐} & \text{右对齐} \\
\hline
1 & 0.24 & 1 & 125 \\
\hline
2 & -1 & 189 & -8 \\
\hline
3 & -20 & 2000 & 1+10i
\end{array}$
```

![\begin{array}{c|l|c|r} n & \text{左对齐} & \text{居中对齐} & \text{右对齐} \\ \hline 1 & 0.24 & 1 & 125 \\ \hline 2 & -1 & 189 & -8 \\ \hline 3 & -20 & 2000 & 1+10i \end{array}](https://math.jianshu.com/math?formula=%5Cbegin%7Barray%7D%7Bc%7Cl%7Cc%7Cr%7D%20n%20%26%20%5Ctext%7B%E5%B7%A6%E5%AF%B9%E9%BD%90%7D%20%26%20%5Ctext%7B%E5%B1%85%E4%B8%AD%E5%AF%B9%E9%BD%90%7D%20%26%20%5Ctext%7B%E5%8F%B3%E5%AF%B9%E9%BD%90%7D%20%5C%5C%20%5Chline%201%20%26%200.24%20%26%201%20%26%20125%20%5C%5C%20%5Chline%202%20%26%20-1%20%26%20189%20%26%20-8%20%5C%5C%20%5Chline%203%20%26%20-20%20%26%202000%20%26%201%2B10i%20%5Cend%7Barray%7D)



```ruby
$\left(\begin{array}{ccc|c}
 a11 & a12 & a13  & b1 \\
a21 & a22  & a23 & b2  \\ 
a31 & a32  & a33 & b3  \\
 \end{array}\right)$
```

![\left(\begin{array}{ccc|c} a11 & a12 & a13 & b1 \\ a21 & a22 & a23 & b2 \\ a31 & a32 & a33 & b3 \\ \end{array}\right)](https://math.jianshu.com/math?formula=%5Cleft(%5Cbegin%7Barray%7D%7Bccc%7Cc%7D%20a11%20%26%20a12%20%26%20a13%20%26%20b1%20%5C%5C%20a21%20%26%20a22%20%26%20a23%20%26%20b2%20%5C%5C%20a31%20%26%20a32%20%26%20a33%20%26%20b3%20%5C%5C%20%5Cend%7Barray%7D%5Cright))



```ruby
$$
\left\{ 
\begin{array}{c}
a_1x+b_1y+c_1z=d_1 \\ 
a_2x+b_2y+c_2z=d_2 \\ 
a_3x+b_3y+c_3z=d_3
\end{array}
\right. 
$$
```

![\left\{ \begin{array}{c} a_1x+b_1y+c_1z=d_1 \\ a_2x+b_2y+c_2z=d_2 \\ a_3x+b_3y+c_3z=d_3 \end{array} \right.](https://math.jianshu.com/math?formula=%5Cleft%5C%7B%20%5Cbegin%7Barray%7D%7Bc%7D%20a_1x%2Bb_1y%2Bc_1z%3Dd_1%20%5C%5C%20a_2x%2Bb_2y%2Bc_2z%3Dd_2%20%5C%5C%20a_3x%2Bb_3y%2Bc_3z%3Dd_3%20%5Cend%7Barray%7D%20%5Cright.)

1. 矩阵

- 第一种方式：使用matrix、pmatrix、bmatrix、Bmatrix、vmatrix或者Vmatrix，smallmatrix
   一个最基本的矩阵



```ruby
$ \begin{matrix}
0 & 1 \\
1 & 0 
\end{matrix} $   
```

![\begin{matrix} 0 & 1 \\ 1 & 0 \end{matrix}](https://math.jianshu.com/math?formula=%5Cbegin%7Bmatrix%7D%200%20%26%201%20%5C%5C%201%20%26%200%20%5Cend%7Bmatrix%7D)

可以看到矩阵中用 & 分隔列，用 \\ 分隔行，在矩阵开始和结束部分\begin和\end+矩阵类型
 下面给出这几种矩阵类型的图示

| 矩阵类型    | 矩阵图示                                                     |
| ----------- | ------------------------------------------------------------ |
| matrix      | ![\begin{matrix}0 & 1 \\1 & 0 \end{matrix}](https://math.jianshu.com/math?formula=%5Cbegin%7Bmatrix%7D0%20%26%201%20%5C%5C1%20%26%200%20%5Cend%7Bmatrix%7D) |
| pmatrix     | ![\begin{pmatrix}0 & 1 \\1 & 0 \end{pmatrix}](https://math.jianshu.com/math?formula=%5Cbegin%7Bpmatrix%7D0%20%26%201%20%5C%5C1%20%26%200%20%5Cend%7Bpmatrix%7D) |
| bmatrix     | ![\begin{bmatrix}0 & 1 \\1 & 0 \end{bmatrix}](https://math.jianshu.com/math?formula=%5Cbegin%7Bbmatrix%7D0%20%26%201%20%5C%5C1%20%26%200%20%5Cend%7Bbmatrix%7D) |
| Bmatrix     | ![\begin{Bmatrix}0 & 1 \\1 & 0 \end{Bmatrix}](https://math.jianshu.com/math?formula=%5Cbegin%7BBmatrix%7D0%20%26%201%20%5C%5C1%20%26%200%20%5Cend%7BBmatrix%7D) |
| vmatrix     | ![\begin{vmatrix}0 & 1 \\1 & 0 \end{vmatrix}](https://math.jianshu.com/math?formula=%5Cbegin%7Bvmatrix%7D0%20%26%201%20%5C%5C1%20%26%200%20%5Cend%7Bvmatrix%7D) |
| Vmatrix     | ![\begin{Vmatrix}0 & 1 \\1 & 0 \end{Vmatrix}](https://math.jianshu.com/math?formula=%5Cbegin%7BVmatrix%7D0%20%26%201%20%5C%5C1%20%26%200%20%5Cend%7BVmatrix%7D) |
| smallmatrix | ![(\begin{smallmatrix} 0 & 1 \\ 1 & 0 \end{smallmatrix})](https://math.jianshu.com/math?formula=(%5Cbegin%7Bsmallmatrix%7D%200%20%26%201%20%5C%5C%201%20%26%200%20%5Cend%7Bsmallmatrix%7D)) |

在此之上，如果我们需要对矩阵起始和结束部分的括号进行变化，可以使用诸如



```ruby
$\left\{\begin{matrix}
 x_1& x_2\\ 
 y_1& y_2
\end{matrix}\right.$    (此处.作为占位符表示不需要分隔符)
$\left.\begin{matrix}
x_1 & x_2\\ 
 y_1& y_2
\end{matrix}\right\}$  
```

![\left\{\begin{matrix} x_1& x_2\\ y_1& y_2 \end{matrix}\right.](https://math.jianshu.com/math?formula=%5Cleft%5C%7B%5Cbegin%7Bmatrix%7D%20x_1%26%20x_2%5C%5C%20y_1%26%20y_2%20%5Cend%7Bmatrix%7D%5Cright.)
 ![\left.\begin{matrix} x_1 & x_2\\ y_1& y_2 \end{matrix}\right\}](https://math.jianshu.com/math?formula=%5Cleft.%5Cbegin%7Bmatrix%7D%20x_1%20%26%20x_2%5C%5C%20y_1%26%20y_2%20%5Cend%7Bmatrix%7D%5Cright%5C%7D)

第二种方式就是使用array环境，在左右端加上所需括号即可

> 输入带省略符号的矩阵



```ruby
$$
        \begin{pmatrix}
        1 & a_1 & a_1^2 & \cdots & a_1^n \\
        1 & a_2 & a_2^2 & \cdots & a_2^n \\
        \vdots & \vdots & \vdots & \ddots & \vdots \\
        1 & a_m & a_m^2 & \cdots & a_m^n \\
        \end{pmatrix}
$$
```

![\begin{pmatrix} 1 & a_1 & a_1^2 & \cdots & a_1^n \\ 1 & a_2 & a_2^2 & \cdots & a_2^n \\ \vdots & \vdots & \vdots & \ddots & \vdots \\ 1 & a_m & a_m^2 & \cdots & a_m^n \\ \end{pmatrix}](https://math.jianshu.com/math?formula=%5Cbegin%7Bpmatrix%7D%201%20%26%20a_1%20%26%20a_1%5E2%20%26%20%5Ccdots%20%26%20a_1%5En%20%5C%5C%201%20%26%20a_2%20%26%20a_2%5E2%20%26%20%5Ccdots%20%26%20a_2%5En%20%5C%5C%20%5Cvdots%20%26%20%5Cvdots%20%26%20%5Cvdots%20%26%20%5Cddots%20%26%20%5Cvdots%20%5C%5C%201%20%26%20a_m%20%26%20a_m%5E2%20%26%20%5Ccdots%20%26%20a_m%5En%20%5C%5C%20%5Cend%7Bpmatrix%7D)

1. 交换图表

> 使用一行  \require{AMScd}  语句来允许交换图表的显示。
>  声明交换图表后，语法与矩阵相似，在开头使用 begin{CD}，在结尾使用 end{CD}，在中间插入图表元素，每个元素之间插入 & ，并在每行结尾处使用 \ 。



```ruby
$\require{AMScd}
\begin{CD}
    A @>a>> B\\
    @V b V V\# @VV c V\\
    C @>>d> D
\end{CD}$
```

![\require{AMScd} \begin{CD} A @>a>> B\\ @V b V V\# @VV c V\\ C @>>d> D \end{CD}](https://math.jianshu.com/math?formula=%5Crequire%7BAMScd%7D%20%5Cbegin%7BCD%7D%20A%20%40%3Ea%3E%3E%20B%5C%5C%20%40V%20b%20V%20V%5C%23%20%40VV%20c%20V%5C%5C%20C%20%40%3E%3Ed%3E%20D%20%5Cend%7BCD%7D)
 其中，@>>> 代表右箭头、@<<< 代表左箭头、@VVV 代表下箭头、@AAA 代表上箭头、@= 代表水平双实线、@| 代表竖直双实线、@.代表没有箭头。
 在 @>>> 的 >>> 之间任意插入文字即代表该箭头的注释文字。



```ruby
$\begin{CD}
    A @>>> B @>{\text{very long label}}>> C \\
    @. @AAA @| \\
    D @= E @<<< F
\end{CD}$
```

![\begin{CD} A @>>> B @>{\text{very long label}}>> C \\ @. @AAA @| \\ D @= E @<<< F \end{CD}](https://math.jianshu.com/math?formula=%5Cbegin%7BCD%7D%20A%20%40%3E%3E%3E%20B%20%40%3E%7B%5Ctext%7Bvery%20long%20label%7D%7D%3E%3E%20C%20%5C%5C%20%40.%20%40AAA%20%40%7C%20%5C%5C%20D%20%40%3D%20E%20%40%3C%3C%3C%20F%20%5Cend%7BCD%7D)

以上内容大致上应该能把绝大多数数学公式优美的书写下来了，更多内容还需要自行查阅官方说明~~
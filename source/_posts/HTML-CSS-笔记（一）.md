---
title: HTML-CSS-笔记（一）
date: 2020-03-01 10:47:55
tags: HTML/CSS
---



# 1.内联元素与块级元素

|   区别   |                        内联元素                        |                   块级元素                   |
| :------: | :----------------------------------------------------: | :------------------------------------------: |
| 排列方式 | 不会独占一行，相邻的内联元素排在同一行，宽度随内容变化 | 会独占一行，默认情况下宽度自动填满父元素宽度 |
| 宽高设置 |            不可以设置宽度width和高度height             |        可以设置宽度width和高度height         |
| 边距设置 |      可以设置margin, padding，但只在水平方向有效       |           可以设置margin, padding            |

## 常见内联元素：

**a – 锚点** 
abbr – 缩写 
b – 粗体(不推荐) 
big – 大字体 
**br – 换行** 
cite – 引用 
**code – 计算机代码(在引用源码的时候需要)** 
**em – 强调** 
font – 字体设定(不推荐) 
i – 斜体 
kbd – 定义键盘文本 
**label – 表格标签** 
**q – 短引用** 
**span – 常用内联容器，定义文本内区块** 
**strong – 粗体强调** 
**textarea – 多行文本输入框** 



## 常见块级元素

**address – 地址** 
**blockquote – 块引用** 
dir – 目录列表 
**div – 常用块级元素，也是CSS layout的主要标签** 
**dl – 定义列表** 
fieldset – form控制组 
**form – 交互表单** 
**h1 – h6 标题** 
**hr – 水平分隔线** 
menu – 菜单列表 
**ol – 有序表单** 
**p – 段落** 
pre – 格式化文本 
**table – 表格** 
**ul – 无序列表** 
**li – 列表项目**

## 内联块状元素inline-block

设置`display:inline-block`

img

input

button

呈现出inline对象，可以与其它行内元素并排，同时也可以设置宽高和边距。

## 内联元素与块级元素的转换

转内联元素：

`display: inline`

转块级元素：

`display: block`



# 2.position属性

## 无定位

`position: static`

该属性值为所有元素定位的默认情况。在我们不愿意元素继承父元素`position`属性时，可以使用`position:static`取消继承，即还原元素定位的默认值。元素出现在正常的文档流中。

此时 `top`、`right`、`bottom`、`left` 属性和z-`index`声明无效。

## 绝对定位

`position: absolute`

元素完全脱离文档流，可以设置`top`、`right`、`bottom`、`left` 属性和z-`index`，其参考对象时离该元素最近的`position`值不是`static`的父元素，当其父元素`position`值均为`static`或者未定义时，其参考对象应该是`document`（浏览器视窗大小的矩形）。一般与相对定位配合使用，父元素为相对定位，子元素设置绝对定位使其相对父元素偏移。

改变内联元素的特性：使内联元素在设置宽高的时候支持宽高

改变区块元素的特性：使区块元素在未设置宽度时由内容撑开宽度

## 相对定位

`position: relative`

元素不脱离文档流，可以设置`top`、`right`、`bottom`、`left` 属性和z-`index`，其参考对象为离该元素最近的父元素，相对于自身原本位置进行偏移。

不影响内联元素和区块元素特性。



# 清除浮动

默认`floar: none;`，可以设置向左或向右浮动`float: left;`或`float: right;`

当一个盒子里的元素全为浮动且盒子本身没有高度时，浮动的子元素的高不会撑开盒子，盒子变成了一条线。

## 额外标签法

给最后一个浮动元素后添加一个空标签，设置`clear: both;`

如：`<div style="clear: both"></div>`

或  `<hr class="clear"/>`

缺点：添加无意义标签，语义差

## 父级添加overflow属性

`overflow: hidden;` 或 `overflow: auto;`

IE6中还需添加`zoom: 1`或为父容器设置宽高

## after伪元素

给父元素添加`clearfix`类

```css
.clearfix:after{/*伪元素是行内元素 正常浏览器清除浮动方法*/
    content: "";
    display: block;
    height: 0;
    clear:both;
    visibility: hidden;
}
.clearfix{
    *zoom: 1;/*ie6清除浮动的方式 *号只有IE6-IE7执行，其他浏览器不执行*/
    }
```

缺点：IE6、7不支持伪元素`after`



## 固定定位

`position: fixed`

相对于浏览器窗口进行定位。可以设置`top`、`right`、`bottom`、`left` 属性和`z-index`。

兼容性问题：IE6不支持。



# 边框

`border: none` 表示无边框样式，浏览器并不会对边框进行渲染，也没有实际的宽度。

`border: 0` 设置边框的宽度为0，浏览器对边框进行渲染。

`border-style`应在`border-color`之前设置



# 回流reflow和重绘repaint

`dispaly: none` 不为被隐藏的对象保留其物理空间。会触发`reflow`回流。

`visibility: hidden` 所占据的空间位置仍然存在，仅为视觉上的完全透明。会触发`repaint`重绘。

## reflow

某个子元素样式发生改变，直接影响到了其父元素以及往上追溯很多祖先元素（包括兄弟元素），这个时候浏览器要重新去渲染这个子元素相关联的所有元素的过程称为回流。

触发`reflow`的情况

1：改变窗口大小`resize`或`scroll`页面

2：改变文字大小或更换字体

3：内容的改变，如用户在输入框中敲字

4：激活伪类，如:`hover`

5：CSS3 动画(`animation`)和过渡(`transition`)，动画的每一`frame`都会触发`reflow`；

6：JS脚本操作`DOM`元素。即添加(`appendChild`)、删除(`removeChild`)`DOM`元素或者改变`DOM`元素的可见性(`display:none`)

7：修改`width`/`height`/`border`/`margin`/`padding`和读取`offsetWidth`/`Height`、`offsetLeft`/`Top`、`scrollTop`/`Left`/`Width`/`Height`、`clientTop`/`Left`/`Width`/`Height`等属性时。因为有些元素依赖这些属性去计算。

8：设置`style`属性（所以最好用`class`而不是直接设置`style`）

## repaint

改变某个元素的背景色、文字颜色、边框颜色等等不影响它周围或内部布局的属性，将只会引起浏览器 `repaint`（重绘）。`repaint` 的速度明显快于 `reflow`。

只涉及视觉效果，不涉及布局。

触发`repaint`的情况

1. `color`的修改
2. `text-align`的修改，如 `text-align：center；`
3. `a:hover`也会造成重绘；
4. `:hover`引起的颜色等不导致页面回流的`style`变动；等等。

## 优化

1. 尽量通过`class`来设计元素样式，不用`style`
2. 尽可能修改层级较低的`DOM`结点
3. 不要把 `DOM` 节点的属性值放在一个循环里当成循环里的变量。不然这会导致大量地读写这个结点的属性。
4. 把 `DOM` 离线后修改。如：
   `a>` 使用 `documentFragment` 对象在内存里操作 `DOM`。
   `b>` 先把 `DOM` 给 `display:none` (有一次 `repaint`)，然后你想怎么改就怎么改。比如修改 `100` 次，然后再把他显示出来。
   `c>` `clone` 一个 `DOM` 节点到内存里，然后想怎么改就怎么改，改完后，和在线的那个的交换一下。
5. 为有动画的元素使用`fixed`或`absolute`的`position`，使动画元素从`DOM`中独立出来，从而减少对其他元素的影响，那么修改他们的`CSS`可以减少`reflow`
6. 不要使用`table`布局。table的每个元素的大小以及内容的改动，都会导致整个`table`进行重新计算，造成大幅度的`repaint`或者`reflow`。改用`div`则可以进行针对性的`repaint`和避免不必要的`reflow`
7. `CSS`中避免使用计算表达式



# 默认边距

|          | margin            | padding              |
| -------- | ----------------- | -------------------- |
| h1~h6    | 有                | 无                   |
| dl       | 有                | 无                   |
| dd       | 有（margin-left） | 无                   |
| ol, ul   | 有                | 有（padding-left）   |
| table    | 无                | 无                   |
| th, td   | 无                | 有                   |
| form     | 有（除IE8）       | 无                   |
| p        | 有                | 无                   |
| textarea | 有                | 有                   |
| select   | 无                | 有（chrome, safari） |
| img      | 有                | 无                   |
| body     | 有                | 无                   |




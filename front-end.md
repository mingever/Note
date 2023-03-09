# HTML

## 简介

HyperText Markup Language

HTML 不是一种编程语言，而是一种标记语言，使用标记来描述网页

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Demo</title>
</head>
<body>
 
<h1>我的第一个标题</h1>
 
<p>我的第一个段落。</p>
 
</body>
</html>
```

<img src="https://oss.mingever.com/note/CSS/HtmlIntroduce.jpg" style="zoom: 50%;" />

## 文本

**文本控制标记**

- `<body>`

	- bgcolor：更改背景颜色，可以是16进制色彩代码，如：bgcolor=”#000000”
	- background：添加背景图片，如：background=”images/bg.gif” 注意相对路径还是绝对路径，相对路径是 /
	- bgproperties=fixed：使背景图片成水印效果，即图片不随滚动条的滚动而滚动

- `<p>` 段落标记

	align

- `<br>` 换行标记

- `<hr>` 水平线划线标记
	- width，align，color，size等属性

	- size等属性可以设置成px像素，也可以设成%

- `<hn>` 标题标记
	- n为标题等级，共6个，n越小，字体越大

	- align属性：对其属性，left居左，center居中，right居右。

- `<font>` 文本样式标记
	- 是对文本而言的，因此不能控制对齐方式
	- face属性：设置字体
	- size,color等属性


**字体风格标记**

`<strong>`，`<b>`以粗体方向显示

`<em>`，`<i>`以斜体方式显示

`<del>`以删除线显示

`<ins>`加下滑线显示

`<small`>缩小字号，可嵌套

`<big>`放大字号

`<sup>`上标

`<sub>`下标

**格式标记**

`<code>` 指出是一组代码，通常和`<pre>`联用，`<pre>`较常用，即保持原版排版方式

`<nobr>` 取消文字换行

`<bloquote>` 向右缩进标记

`<center>` 居中对齐标记

**特殊标记**

空格，© 版权号等

## 超链接

`<a>` 标签

- ==href==属性：来指定路径地址，可以是相对路径，可以是绝对路径，也可以是一个网站的地址

	如\<a href="www,baidu.com">超链接\</a>

- ==Target==属性：定义被链接的文档在何处显示

	- _blak：新开窗口
	- _self:在同一窗口打开
	- _top：在浏览器的整个窗口打开
	- _parent 父框架，在上一级窗口打开

- 锚点的使用，使用id定位，来实现页内跳转或者其他页面的某个区域
	通过添加name属性，如

	``` html
	<a href=”#add”>跳转到</a>
	<a name=”add”></a>
	```

## 图像

`<img>`标签，单标签

- 属性

	- ==src==属性：来添加图像的地址
	- alight:设置对齐方式，值：left right top middle bottom texttop absmiddle baseline absbottom 
	- ==height==和==width==设置宽和高
	- alt 图像不能显示时替换文本
	- title 鼠标悬停时显示内容
	- ==border== 设置图像边框宽度
	- ==hapace== 图像水平间距
	- ==vspace== 图像垂直间距

- 设置图像超链接
	与`<a>`标签联用，最好把borter属性设置成0，如

	```html
	<a href=”链接地址” target=”目标窗口打开方式”>
	<img src =”图像地址” border=”0”>
	</a>
	```

## 头部head

`<head>` 元素包含了所有的头部标签元素。在` <head>`元素中你可以插入脚本（scripts）, 样式文件（CSS），及各种meta信息

可以添加在头部区域的元素标签为: `<title>`, `<style>`, `<meta>`, `<link>`, `<script>`, `<noscript>` 和 `<base>`

- `<title>`：定义了不同文档的标题

- `<base>`：描述了基本的链接地址/链接目标，该标签作为HTML文档中所有的链接标签的默认链接

- `<link>`：定义了文档与外部资源之间的关系，通常用于链接到样式表
- `<style>`：定义了HTML文档的样式文件引用地址，在`<style>`元素中你也可以直接添加样式来渲染 HTML 文档
- `<meta>`：描述了一些基本的元数据，标签提供了元数据，元数据也不显示在页面上，但会被浏览器解析，通常用于指定网页的描述，关键词，文件的最后修改时间，作者，和其他元数据
- `<script>`：用于加载脚本文件，如JavaScript

## 列表

- 无序列表`<ul>`

	``` html
	<ul>
		<li></li>
		<li></li>
	</ul>
	```

	type属性：默认是圆点 取值：disc 圆，circle 圆圈，square 方块

- 有序列表`<ol>`

	```html
	<ol>
		<li></li>
		<li></li>
	</ol>
	```

	type属性：取值 1，a，A，i，I,

	start属性：来调整起始位置 

- 定义列表`<dl>`

	```html
	<dl>
		<dt>名词</dt><dd>解释</dd>
		<dt>名词</dt><dd>解释</dd>
	</dl>
	```

## 表格

`<table>`来表示表格` <tr>`控制行` <td>`控制列

表格的基本构成：table，tr，td 不能缺少任何一个，且只能配对使用
设置表格的标题：caption
表格的表头：使用th 来定义特殊的单元格，文字是默认加粗的

```html
<table border=”1”>  
    <tr>
        <td>文字</td>
    </tr>

    <tr>
        <td>文字</td>
    </tr>
</table>
```

- 表格的基本属性：包括大小和对齐属性，即通过width， heigth， align来控制
- 表格的边框属性：`<table>`属性
	- ==border==:边框的宽度，可以是px，也可以是%
	- ==bordercolor==:边控颜色
	- ==cellspacing==:内框宽度，即表格内部各个单元格直间的宽度
	- ==cellpadding==:文字与边框间距
- 单元格属性：`<tr>`  `<td>`属性
	- ==colspan==:水平方向上跨列
	- ==rowspan==:垂直方向跨行
	- ==bgcolor==:指定表格或者是单元格背景颜色
	- ==background==: 指定表格或者是单元格背景图像，值是图片地址
	- width ，height， align ，valign
	- top，bottom:顶端，底端对齐
	- middle:剧中对齐
	- baseline:相对于基线对齐
	- bordercolorlight:单元格亮边框
	- bordercolodark:单元格暗边框

## 表单

- `<form>`：来创建表单

	- action属性：规定当提交表单时向何处发送表单数据
	- name属性：给表单命名
	- method：规定用于发送表单数据的 HTTP 方法
	- enctype：规定在向服务器发送表单数据之前如何对其进行编码
	- target：规定在何处打开 action URL ，_blank _self _paren _top

- `<input>`：输入类控件标记（单标记）

	- ==type==属性来决定类型，即`<input type="">`，可以取值是：

		text 文字字段      password 密码域

		radio 单选按钮     checkbox 复选框

		button 普通按钮   submit  提交按钮   reset重置按钮

		image 图像域     select 下拉列表

		hidden 隐藏域

		file 文件域 

		optgroup 定义选项组

		email  month number range time等html新标记

	- ==name==属性：指定表单控件的名称 

	- ==value== 属性：指定 `<input> `元素 value 的值

	- size属性：规定以字符数计的 `<input>` 元素的可见宽度

	- checked属性：checked 属性规定在页面加载时应该被预先选定的 `<input>` 元素 (只针对type="checkbox" 或者 type="radio")

	- ==required==属性：规定必需在提交表单之前填写输入字段

	- ==maxlength==属性：规定 `<input>` 元素中允许的最大字符数

## Demo

```html
盒模型作业
<!DOCTYPE html>
<html lang="en">

<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style type="text/css">
		table td {
			border: 1px solid;
		}

		table {
			border-collapse: collapse;
			margin: auto;
		}

		span {
			color: red;
		}

		.c1 {
			text-align: center;
			background: blue;
		}

		.c2 {
			text-align: center;
		}

		.c {
			text-align: right;
		}
	</style>
</head>

<body>
	<table>
		<caption>请认真填写以下内容</caption>
		<tr>
			<td colspan="2" class="c1">用户注册（<span>*</span>为必填内容）</td>
		</tr>
		<tr>
			<td class="c">用户名</td>
			<td>
				<form>
					<input type="text" name="n1"><span>*</span>（不得大于20个中文字）
				</form>
			</td>
		</tr>
		<tr>
			<td class="c">密码</td>
			<td>
				<form>
					<input type="passwoed" name="n2"><span>*</span>
				</form>
			</td>
		</tr>
		<tr>
			<td class="c">重置密码</td>
			<td>
				<form>
					<input type="passwoed" name="n2"><span>*</span>
				</form>
			</td>
		</tr>
		<tr>
			<td class="c">性别</td>
			<td>
				<form>
					<input type="radio" name="n3">男
					<input type="radio" name="n3">女
				</form>
			</td>
		</tr>
		<tr>
			<td class="c">年龄</td>
			<td>
				<form>
					<select>
						<option>5</option>
						<option>6</option>
						<option>7</option>
						<option>8</option>
						<option>9</option>
						<option>10</option>
					</select>
				</form>
			</td>
		</tr>
		<tr>
			<td colspan="2" class="c2">
				<form>
					<input type="submit">
					<input type="reset">
				</form>
			</td>
		</tr>

	</table>
</body>
</html>

晨光
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>线上授权</title>
    <style type="text/css">
        /*可以简单方便的一次性重置所有HTML网页元素的浏览器样式*/
        * {
            margin: 0px;
            padding: 0px;
        }

        span {
            color: red;
        }

        /*核心表单盒子*/
        .boxtable {
            width: 780px;
            height: 1000px;
            margin: auto;
        }

        .p5 {
            font-size: 17px;
            font-weight: bold;
            color: #444444;
            text-align: center;
        }

        table {
            width: 770px;
            height: 860px;
            font-size: 15px;
            margin: auto;
        }

        table tr {
            line-height: 40px;
        }

        table tr td {
            width: 60px;
        }

        .inp1 {
            width: 260px;
            height: 23px;
        }

        .inp2 {
            width: 60px;
            height: 23px;
        }

        .inp3 {
            width: 140px;
            height: 23px;
        }

        .sub {
            display: block;
            width: 100px;
            height: 24px;
            background: red;
            font-size: 15px;
            font-weight: bold;
            line-height: 24px;
            text-align: center;

        }

        .sub2 {
            padding: 30px 300px;
        }
    </style>
</head>

<body>
    <div class="boxtable">
        <table>
            <tr>
                <td colspan="2">
                    <p class="p5">基本信息</p>
                </td>
            </tr>
            <tr>
                <td><span>*</span>联系人:</td>
                <td><input type="text" class="inp1" name="" placeholder="联系人"></td>
            </tr>
            <tr>
                <td><span>*</span>联系电话:</td>
                <td><input type="text" class="inp1" name="" placeholder="请填写联系电话"></td>
            </tr>
            <tr>
                <td>短信验证:</td>
                <td><input type="text" class="inp1" name="" placeholder="请填写短息验证码"></td>
            </tr>
            <tr>
                <td><span>*</span>现状、现况:</td>
                <td><input type="radio" name="na1">已开店<input type="radio" name="na1">待开店</td>
            </tr>
            <tr>
                <td><span>*</span>申请授权平台:</td>
                <td><input type="radio" name="na2">天猫<input type="radio" name="na2">淘宝<input type="radio"
                        name="na2">亚马逊<input type="radio" name="na2">京东<input type="radio" name="na2">一号店<input
                        type="radio" name="na2">其他</td>
            </tr>
            <tr>
                <td><span>*</span>授权店铺类型:</td>
                <td><input type="radio" name="na3">专卖店(唯一经营“晨光”系列产品的授权店铺)<br><input type="radio"
                        name="na3">专营店(不仅经营“晨光”系列产品的授权店铺)</td>
            </tr>
            <tr>
                <td><span>*</span>在线店铺名:</td>
                <td><input type="text" class="inp1" name="" placeholder="请填写在线店铺名称"></td>
            </tr>
            <tr>
                <td><span>*</span>在线店铺链接:</td>
                <td><input type="text" class="inp1" name="" placeholder="请填写在线店铺链接"></td>
            </tr>
            <tr>
                <td><span>*</span>店铺负责人:</td>
                <td><input type="text" class="inp1" name="" placeholder="请填写店铺负责人"></td>
            </tr>
            <tr>
                <td><span>*</span>公司/企业名称:</td>
                <td><input type="text" class="inp1" name="" placeholder="请填写公司/企业名称"></td>
            </tr>
            <tr>
                <td><span>*</span>负责人身份证号码:</td>
                <td><input type="text" class="inp1" name="" placeholder="请填写负责人身份证号码"></td>
            </tr>
            <tr>
                <td><span>*</span>经营所在地:</td>
                <td>
                    <select>
                        <option>省/直辖市</option>
                        <option>烟台</option>
                        <option>莱山</option>
                    </select>
                    <select>
                        <option>城市</option>
                        <option>莱山区</option>
                        <option>芝罘区</option>
                        <option>福山区</option>
                    </select>
                    <input type="text" name="" placeholder="请填写详细经营所在地">
                </td>
                </td>
            </tr>
            <tr>
                <td>固定电话:</td>
                <td><input type="text" class="inp2" name="" placeholder="区号">——<input type="text" class="inp3" name=""
                        placeholder="请填写固定电话号码">
                </td>
            </tr>
            <tr>
                <td><span>*</span>QQ号码:</td>
                <td><input type="text" class="inp1" name="" placeholder="请填写QQ号码"></td>
            </tr>
            <tr>
                <td><span>*</span>阿里巴巴ID:</td>
                <td><input type="text" class="inp1" name="" placeholder="请填写阿里旺旺ID"></td>
            </tr>
            <tr>
                <td><span>*</span>邮箱:</td>
                <td><input type="text" class="inp1" name="" placeholder="请填写联系邮箱"></td>
            </tr>
            <tr class="sub1">
                <td class="sub2" colspan="2"><a class="sub" href="#">提交申请</a></td>
            </tr>
        </table>
    </div>
</html>

下拉菜单
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<style type="text/css">
		*{
			margin: 0px;
			padding: 0px;
		}
		.menu{
			/*border:1px blue solid;*/
			width:445px; 
			height:30px;
			margin: 0px auto;
			background:orange;		
		}
		.menu ul{
			/*border:1px blue solid;*/
			width:445px; 
			height:30px;
			list-style: none;	
		}
		.menu ul li{
			/*border:1px blue solid;*/
			width:80px; 
			height:30px;
			float: left;
			margin: 0px 3px;	
		}
		.menu ul li a{
			/*border:1px blue solid;*/
			text-decoration: none;
			display: block;
			text-align: center;
			line-height: 30px;
		}
		.menu ul li a:hover{
			background:yellow;
		}
		.menu ul li:hover ul{
			display: block;
		}
		.menu ul li ul{
			display: none;
			left: 1px;
		}
		.menu ul li ul li{
			float: none;
			background: orange;
		}
		.menu ul li ul li a{
			display: block;
		}
		.menu ul li ul li a:hover{
			background: yellow;
		}
	</style>
</head>
<body>
	<div class="menu">
		<ul>
			<li><a href="#">首页</a></li>
			<li><a href="#">html</a>
				<ul>
					<li><a href="#">标签</a></li>
					<li><a href="#">属性</a></li>
					<li><a href="#">点击开始</a></li>
				</ul>
			</li>
			<li><a href="#">css</a></li>
			<li><a href="#">javasprit</a>
				<ul>
					<li><a href="#">语法</a></li>
					<li><a href="#">算法</a></li>
					<li><a href="#">点击开始</a></li>
				</ul>
			</li>
			<li><a href="#">免费教学</a></li>
		</ul>
	</div>
</body>
</html>

二级菜单
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<script type="text/javascript">
		var cityList = new Array();
cityList['北京市'] = ['朝阳区', '西城区', '海淀区', '宣武区', '丰台区'];
cityList['上海市'] = ['丰贤区', '虹口区', '黄浦区', '青浦区'];
cityList['威海市'] = ['环翠区', '高区', '文登市'];
function long(){
	var pro = document.getElementById("m1");
	for (var i in cityList){
		pro.add(new Option(i));
	}
}
function City(){
	var m0 = document.getElementById("m1").value;
	var city = document.getElementById("city");	
	city.options.length=0;

	for (var i in cityList){
		if (i==m0){
			for (var j in cityList[i]){								
				city.add(new Option(cityList[i][j]),null);									
			}
		}
	}	
}
window.onload = long;
	</script>
</head>
<body>
        <dd><select id="m1" onclick="City()" style="width:120px;">
                <option>请选择省/城市</option>
            </select>
        </dd>
        <dd><select id="city"  style="width:130px;">
                <option>请选择城市/地区</option>
            </select>
        </dd>
</dl>
</body>
</html>
```



# CSS

## 简介

层叠样式表，样式定义如何显示 HTML 元素，样式通常存储在样式表中，为了解决内容与表现分离的问题

外部样式表通常存储在 CSS 文件中，多个样式定义可层叠为一个

### 语法

由两个主要的部分构成：选择器，以及一条或多条声明

<img src="https://oss.mingever.com/note/CSS/CSSgrammar.jpg" style="zoom: 67%;" />

- 选择器

	通常是需要改变样式的 HTML 元素

	- id选择器，以 `#` 来定义，可以为标有特定 id 的 HTML 元素指定特定的样式

		```css
		#para1
		{
		    text-align:center;
		    color:red;
		} 
		
		<p id="para1">Hello World!</p>
		```

	- class选择器：用于描述一组元素的样式

		- class 选择器在HTML中以class属性表示, 在 CSS 中，类选择器以一个点"."号显示

		- class 选择器有别于id选择器，class可以在多个元素中使用

			```CSS
			.center
			{
			    text-align:center;
			}
			
			<h1 class="center">标题居中</h1>
			<p class="center">段落居中。</p> 
			```

		- 可以指定特定的HTML元素使用class

			```CSS
			p.center
			{
			    text-align:center;
			}
			
			<h1 class="center">这个标题不受影响</h1>
			<p class="center">这个段落居中对齐。</p> 
			```

- 声明

	- 每条声明由一个属性和一个值组成
	- CSS声明总是以分号 `;` 结束，声明总以大括号 `{}` 括起来
	- 属性是你希望设置的样式属性。每个属性有一个值。属性和值被冒号分开

### 创建

- 外部样式表：当样式需要应用于很多页面时。每个页面使用 `<link>` 标签链接到样式表，`<link>` 标签在文档头部

	```html
	<head>
		<link rel="stylesheet" type="text/css" href="mystyle.css">
	</head>
	```

- 内部样式表：当单个文档需要特殊的样式时

	```HTML
	<head>
	<style type="text/css">
	    hr {color:sienna;}
	    p {margin-left:20px;}
	    body {background-image:url("images/back40.gif");}
	</style>
	</head>
	```

- 内联样式

	- 由于要将表现和内容混杂在一起，内联样式会损失掉样式表的许多优势。请慎用这种方法，例如当样式仅需要在一个元素上应用一次时

	- 要使用内联样式，你需要在相关的标签内使用样式`style`属性。Style 属性可以包含任何 CSS 属性

		```Html
		<p style="color:sienna;margin-left:20px">这是一个段落。</p>
		```

## backgrounds(背景)

CSS 背景属性用于定义HTML元素的背景

- background-color
- background-image
- background-repeat：设置背景图像是否及如何重复
- background-attachment：背景图像是否固定或者随着页面的其余部分滚动
- background-position：背景图像的起始位置

## 文本

**文本**

- 文本颜色：color
- 对齐方式：text-align：center，right

- 文本修饰：text-decoration
- 文本转换：text-transform
- 文本缩进：text-indent

**字体**

- 设置文本的字体系列：font-family
- 字体样式：font-style：normal，italic，oblique
- 字体大小：font-size：px，%，em
- 字体粗细：font-weight

**链接**

a:link - 正常，未访问过的链接
a:visited - 用户已访问过的链接
a:hover - 当用户鼠标放在链接上时
a:active - 链接被点击的那一刻

- 字体

	```CSS
	a:link {color:#000000;}      /* 未访问链接*/
	a:visited {color:#00FF00;}  /* 已访问链接 */
	a:hover {color:#FF00FF;}  /* 鼠标移动到链接上 */
	a:active {color:#0000FF;}  /* 鼠标点击时 */
	```

- 背景

	```CSS
	a:link {background-color:#B2FF99;}    /* 未访问链接 */
	a:visited {background-color:#FFFF85;} /* 已访问链接 */
	a:hover {background-color:#FF704D;}   /* 鼠标移动到链接上 */
	a:active {background-color:#FF704D;}  /* 鼠标点击时 */
	```

## 表格

**边框**

- 表格边框

	```CSS
	table, th, td
	{
	    border: 1px solid black;
	}
	```

- 折叠边框

	border-collapse ：属性设置表格的边框是否被折叠成一个单一的边框或隔开

**表格宽度和高度**

```CSS
table 
{
    width:100%;
}
th
{
    height:50px;
}
```

**表格文字对齐**

- 水平对齐方式：text-align
- 垂直对齐方式：vertical-align

## 盒模型

所有HTML元素都可以看作盒子

CSS盒模型本质上是一个盒子，封装周围的HTML元素，它包括：边距，边框，填充，和实际内容

盒模型允许我们在其它元素和周围元素边框之间的空间放置元素

<img src="https://oss.mingever.com/note/CSS/box-model.gif" style="zoom: 80%;" />

​	Margin(外边距) - 清除边框外的区域，外边距是透明的。

​	Border(边框) - 围绕在内边距和内容外的边框。

​	Padding(内边距) - 清除内容周围的区域，内边距是透明的。

​	Content(内容) - 盒子的内容，显示文本和图像。

**元素的宽度和高度**

当指定一个 CSS 元素的宽度和高度属性时，只是设置内容区域的宽度和高度。要知道，完整大小的元素，你还必须添加内边距，边框和外边距

```CSS
div {
    width: 300px;
    border: 25px solid green;
    padding: 25px;
    margin: 25px;
}
```

**边框 border**

- border-style

	- none：默认无边框
	- dashed: 定义一个虚线边框
	- solid: 定义实线边框

- border-width：边框指定宽度

- border-color属性用于设置边框的颜色

	- name - 指定颜色的名称，如 "red"
	- RGB - 指定 RGB 值, 如 "rgb(255,0,0)"
	- Hex - 指定16进制值, 如 "#ff0000"

- 单独设置各边

	```CSS
	border-top-style:dotted;
	border-right-style:solid;
	border-bottom-style:dotted;
	border-left-style:solid;
	```

- 简写属性，例 `border:5px solid red;`

**轮廓 outline**

轮廓是绘制于元素周围的一条线，位于边框边缘的外围，可起到突出元素的作用。

轮廓属性指定元素轮廓的样式、颜色和宽度

**外边距 margin**

margin属性定义元素周围的空间

margin 清除周围的（外边框）元素区域。margin 没有背景颜色，是完全透明的。

margin 可以单独改变元素的上，下，左，右边距，也可以一次改变所有的属性

<img src="https://oss.mingever.com/note/CSS/margin-mod.png" style="zoom:80%;" />

- 值

	auto：设置浏览器边距。这样做的结果会依赖于浏览器

	length：定义一个固定的margin（使用像素，pt，em等）

	%：定义一个使用百分比的边距

	```CSS
	margin-top:100px;
	margin-bottom:100px;
	margin-right:50px;
	margin-left:50px;
	```

- 简写

	```CSS
	margin:25px 50px 75px 100px;
	上边距为25px
	右边距为50px
	下边距为75px
	左边距为100px
	
	margin:25px 50px 75px;
	上边距为25px
	左右边距为50px
	下边距为75px
	
	margin:25px 50px;
	上下边距为25px
	左右边距为50px
	
	margin:25px;
	所有的4个边距都是25px
	```

**填充Padding**

padding（填充）是一个简写属性，定义元素边框与元素内容之间的空间，即上下左右的内边距

当元素的padding内边距被清除时，所释放的区域将会受到元素背景颜色的填充。

单独使用padding属性可以改变上下左右的填充。

- 值

	```CSS
	padding-top:25px;
	padding-bottom:25px;
	padding-right:50px;
	padding-left:50px;
	```

- 简写：和margin一样

## 分组和嵌套选择器

- 为了减少代码，可以使用分组选择器

	```css
	h1,h2,p
	{
	    color:green;
	}
	```

- 嵌套选择器

	p{ }: 为所有 p 元素指定一个样式。

	.marked{ }: 为所有 class="marked" 的元素指定一个样式。

	.marked p{ }: 为所有 class="marked" 元素内的 p 元素指定一个样式。

	p.marked{ }: 为所有 class="marked" 的 p 元素指定一个样式。

	```css
	p
	{
	    color:blue;
	    text-align:center;
	}
	.marked
	{
	    background-color:red;
	}
	.marked p
	{
	    color:white;
	}
	p.marked{
	    text-decoration:underline;
	}
	```

## Dimension(尺寸)

- 设置元素的高度

	```CSS
	<style>
	img.normal
	{
	    height:auto;
	}
	img.big
	{
	    height:120px;
	}
	p.ex
	{
	    height:100px;
	    width:100px;
	}
	</style>
	
	
	<body>
	<img class="normal" src="logocss.gif" width="95" height="84" /><br>
	<img class="big" src="logocss.gif" width="95" height="84" />
	<p class="ex">这个段落的高和宽是 100px.</p>
	<p>这是段落中的一些文本。这是段落中的一些文本。
	这是段落中的一些文本。这是段落中的一些文本。
	这是段落中的一些文本。这是段落中的一些文本.</p>
	</body>
	```

- 使用百分比设置图像的高度

	```CSS
	<style>
	html {height:100%;}
	body {height:100%;}
	img.normal {height:auto;}
	img.big {height:50%;}
	img.small {height:10%;}
	</style>
	</head>
	
	<body>
	<img class="normal" src="logocss.gif" width="95" height="84" /><br>
	<img class="big" src="logocss.gif" width="95" height="84" /><br>
	<img class="small" src="logocss.gif" width="95" height="84" />
	```

## Display(显示)

- Display(显示) 与 Visibility（可见性）

	display属性设置一个元素应如何显示，visibility属性指定一个元素应可见还是隐藏

	`display:none`：可以隐藏某个元素，且隐藏的元素不会占用任何空间

	`visibility:hidden`：可以隐藏某个元素，但隐藏的元素仍需占用与未隐藏之前一样的空间

- 内联元素和块元素

	块元素是一个元素，占用了全部宽度，在前后都是换行符。
	内联元素只需要必要的宽度，不强制换行。和相邻的内联元素在同一行

	- 内联元素：`display:inline;`
	- 块元素：`display:block;`
	- `display:inline-block` ：显示为内联块元素，表现为同行显示并可修改宽高内外边距等属性我们常将所有`<li>`元素加上display:inline-block样式，原本垂直的列表就可以水平显示了。

> 每一个元素都有默认的display属性值。比如div元素，它的默认display属性值为block。span元素的默认display属性值为inline
>
> 块级元素：占据一定矩形空间，可以通过设置高度、宽度、内外边距等属性，来调整的这个矩形
> 行内元素 ：自己的独立空间，它是依附于其他块级元素存在的，因此，对行内元素设置高度、宽度、内外边距等属性，都是无效的
>
> ``` html
> 常用的块状元素有：
> <div>、<p>、<h1>...<h6>、<ol>、<ul>、<dl>、<table>、<address>、<form>
> 常用的内联元素有：
> <a>、<span>、<br>、<i>、<em>、<strong>、<label>、<q>、<var>、<cite>、<code>
> 常用的内联块状元素有：
> <img>、<input>
> ```
>
> 设置display:block就是将元素显示为块级元素，从而使其具有块状元素特点：
>
> - 每个块级元素都从新的一行开始，并且其后的元素也另起一行
> - 元素的高度、宽度、行高以及顶和底边距都可设置
> - 元素宽度在不设置的情况下，是它本身父容器的100%（和父元素的宽度一致），除非设定一个宽度
>
> 块状元素也可以通过代码display:inline将元素设置为内联元素，从而使其具有内联元素特点：
>
> - 和其他元素都在一行上
> - 元素的高度、宽度及顶部和底部边距不可设置
> - 元素的宽度就是它包含的文字或图片的宽度，不可改变

## Position(定位)

position 属性指定了元素的定位类型

position 属性的五个值：static，relative，fixed，absolute，sticky

**static 定位**

HTML 元素的默认值，即没有定位，遵循正常的文档流对象。

静态定位的元素不会受到 top, bottom, left, right影响。

**fixed 定位**

元素的位置相对于浏览器窗口是固定位置

即使窗口是滚动的它也不会移动

```CSS
p.pos_fixed
{
    position:fixed;
    top:30px;
    right:5px;
}
```

**relative 定位**

相对定位元素的定位是相对其正常位置

**absolute 定位**

绝对定位的元素的位置相对于最近的已定位父元素，如果元素没有已定位的父元素，那么它的位置相对于`<html>`

```CSS
h2
{
    position:absolute;
    left:100px;
    top:150px;
}

<body>
<h2>这是一个绝对定位了的标题</h2>
<p>用绝对定位,一个元素可以放在页面上的任何位置。标题下面放置距离左边的页面100 px和距离页面的顶部150 px的元素。.</p>
</body>
```

**sticky 定位**

基于用户的滚动位置来定位

粘性定位的元素是依赖于用户的滚动，在 `position:relative` 与 `position:fixed` 定位之间切换。

它的行为就像 `position:relative;` 而当页面滚动超出目标区域时，它的表现就像 `position:fixed;` 它会固定在目标位置。

## Float(浮动)

Float会使元素向左或向右移动，其周围的元素也会重新排列

Float往往是用于图像，但它在布局时一样非常有用

1. 元素怎样浮动

	元素的水平方向浮动，意味着元素只能左右移动而不能上下移动。
	一个浮动元素会尽量向左或向右移动，直到它的外边缘碰到包含框或另一个浮动框的边框为止。
	浮动元素之后的元素将围绕它。
	浮动元素之前的元素将不会受到影响。
	如果图像是右浮动，下面的文本流将环绕在它左边

	```CSS
	img
	{
	    float:right;
	}
	```

2. 彼此相邻的浮动元素

	如果你把几个浮动的元素放到一起，如果有空间的话，它们将彼此相邻

	```CSS
	.thumbnail 
	{
	    float:left;
	    width:110px;
	    height:90px;
	    margin:5px;
	}
	
	<h3>图片库</h3>
	<p>试着调整窗口,看看当图片没有足够的空间会发生什么。</p>
	<img class="thumbnail" src="1.jpg" width="107" height="90">
	<img class="thumbnail" src="1.jpg" width="107" height="80">
	<img class="thumbnail" src="1.jpg" width="116" height="90">
	<img class="thumbnail" src="1.jpg" width="120" height="90">
	<img class="thumbnail" src="1.jpg" width="107" height="90">
	<img class="thumbnail" src="1.jpg" width="107" height="80">
	<img class="thumbnail" src="1.jpg" width="116" height="90">
	```

3. 清除浮动：使用 `clear`

	元素浮动之后，周围的元素会重新排列，为了避免这种情况，使用 clear 属性。

	clear 属性指定元素两侧不能出现浮动元素

	```CSS
	.text_line
	{
	    clear:both;
	}
	```

## 对齐

**1）元素居中对齐**

要水平居中对齐一个元素(如 \<div>) 可以使用 `margin: auto;`

设置到元素的宽度将防止它溢出到容器的边缘

元素通过指定宽度，并将两边的空外边距平均分配

```CSS
.center {
    margin: auto;
    width: 50%;
    border: 3px solid green;
    padding: 10px;
}
```

**2）文本居中对齐**

如果仅仅是为了文本在元素内居中对齐，可以使用 `text-align: center;`

```CSS
.center {
    text-align: center;
    border: 3px solid green;
}
```

**3）图片居中对齐**

要让图片居中对齐, 可以使用 `margin: auto;` 并将它放到块元素中

```CSS
img {
    display: block;
    margin: auto;
    width: 40%;
}
```

**4）左右对齐 - 使用定位方式**

可以使用 `position: absolute; `属性来对齐元素

当使用 position 来对齐元素时, 通常 \<body> 元素会设置 margin 和 padding 。 这样可以避免在不同的浏览器中出现可见的差异

```CSS
.right {
    position: absolute;
    right: 0px;
    width: 300px;
    border: 3px solid #73AD21;
    padding: 10px;
}
```

**5）左右对齐 - 使用 float 方式**

也可以使用 float 属性来对齐元素

当像这样对齐元素时，对 \<body> 元素的外边距和内边距进行预定义是一个好主意。这样可以避免在不同的浏览器中出现可见的差异

如，使用 float 属性来实现右对齐：

```CSS
.right {
    float: right;
    width: 300px;
    border: 3px solid #73AD21;
    padding: 10px;
}
```

**6）垂直居中对齐 - 使用 padding**

有很多方式可以实现垂直居中对齐。 一个简单的方式就是头部顶部使用 `padding`

```CSS
.center {
    padding: 70px 0;
    border: 3px solid green;
}
```

如果要水平和垂直都居中，可以使用 `padding` 和 `text-align: center`

```CSS\
.center {
    padding: 70px 0;
    border: 3px solid green;
    text-align: center;
}
```

**7）垂直居中 - 使用 line-height**

`line-height`：行高，指一行文字的高度，用来指定行间的距离，具体来说是指两行文字间基线之间的最小距离\

```CSS
.center {
    line-height: 200px;
    height: 200px;
    border: 3px solid green;
    text-align: center;
}

.center p {
    line-height: 1.5;
    display: inline-block;
    vertical-align: middle;
}

<h2>居中</h2>
<p>以下实例中，我们让 line-height 属性值和 height 属性值相等来设置 div 元素居中：</p>
<div class="center">
  <p>我是垂直居中的。</p>
</div>
```

**8）垂直居中 - 使用 position 和 transform**

除了使用 padding 和 line-height 属性外,我们还可以使用 transform 属性来设置垂直居中

```CSS
.center { 
    height: 200px;
    position: relative;
    border: 3px solid green; 
}

.center p {
    margin: 0;
    position: absolute;
    top: 50%;
    left: 50%;
    -ms-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
}

<h2>居中</h2>
<p>以下实例中，我们使用了 positioning 和 transform 属性来设置水平和垂直居中：</p>
<div class="center">
  <p>我是水平和垂直居中的。</p>
</div>
```

# JavaScript

## 简介

JavaScript 是 Web 的编程语言，是互联网上最流行的脚本语言

HTML 定义了网页的内容。CSS 描述了网页的布局。JavaScript 控制了网页的行为

HTML 中的脚本必须位于 `<script> ` 标签之间

脚本可被放置在 HTML 页面的 `<body>` 和 `<head>` 部分中

**1）`<script>` 标签**

\<script> 和 \</script> 会告诉 JavaScript 在何处开始和结束，\<script> 和\</script> 之间的代码行包含了 JavaScript

在 HTML5 中，JavaScript 是 HTML5 以及所有现代浏览器中的默认脚本语言，不用再type="text/javascript"了

**2）`<body>` 中的 JavaScript**

```html
例：JavaScript 会在页面加载时向 HTML 的 <body> 写文本
<body>
    <script>
    	document.write("<h1>这是一个标题</h1>");
    	document.write("<p>这是一个段落</p>");
    </script>
</body>
```

**3）JavaScript 函数和事件**

如果我们把 JavaScript 代码放入函数中，就可以在事件发生时调用该函数

**4）在 `<head>` 或者 `<body>` 的JavaScript**

可以在 HTML 文档中放入不限数量的脚本

脚本可位于 HTML 的 \<body> 或 \<head> 部分中，或者同时存在于两个部分中

通常的做法是把函数放入 \<head> 部分中，或者放在页面底部

**5）外部的 JavaScript**

可以把脚本保存到外部文件中。外部文件通常包含被多个网页使用的代码

外部 JavaScript 文件的文件扩展名是 .js

如需使用外部文件，请在 `<script>` 标签的 `src` 属性中设置该 .js 文件
`<script src="myScript.js"></script>`

**6）输出**

- 使用 `window.alert()` 弹出警告框

	可以输出警告框，window可省略，具有弹框效果。

	``` html
	<script>window.alert(5 + 6);</script>
	```

- 使用 `innerHTML` 写入到 HTML 元素

	如需从 JavaScript 访问某个 HTML 元素，可以使用 document.getElementById(id) 方法

	使用 "id" 属性来标识 HTML 元素，并 innerHTML 来获取或插入元素内容

	```html
	<p id="demo">我的第一个段落</p>
	
	<script>
	    document.getElementById("demo").innerHTML = "段落已修改。";
	</script>
	```

- 使用`document.write()`写入到 HTML中

	可以将内容直接写到html文档中，在页面上输出

	请使用 document.write() 仅仅向文档输出写内容，如果在文档已完成加载后执行 document.write，整个 HTML 页面将被覆盖

	```html
	<script>
	    document.write(Date());
	</script>
	```

- 使用 `console.log()` 写入到浏览器的控制台

	console系列语句，可以把需要输出的内容写入到浏览器的控制台，在控制台里输出内容，开发时会经常使用。

	```html
	<script>
	    console.log('控制台.日志()');
	    console.warn('控制台.警告()');
	    console.error('控制台.错误()');
	</script>
	```

**7）字面量**

在编程语言中，一般固定值称为字面量

- ==数字==（Number）字面量 可以是整数或者是小数，或者是科学计数

	3.14 1001 123e5

- ==字符串==（String）字面量 可以使用单引号或双引号

	"John Doe"

	'John Doe'

- 表达式字面量 用于计算

	5 + 6

	5 * 10

- ==数组==（Array）字面量 定义一个数组

	[40, 100, 1, 5, 25, 10]

- ==对象==（Object）字面量 定义一个对象

	{firstName:"John", lastName:"Doe", age:50, eyeColor:"blue"}

- ==函数==（Function）字面量 定义一个函数

	function myFunction(a, b) { return a * b;}

**8）变量**

JavaScript 变量均为对象。当声明一个变量时，就创建了一个新的对象。

- JavaScript 使用关键字 var 来定义变量， 使用等号来为变量赋值

	``` javascript
	var x, length
	x = 5
	length = 6
	```

- 未使用值来声明的变量，其值实际上是 undefined

	```javascript
	var carname; 
	//此时变量 carname 的值是 undefined
	```

**9）运算符**

- 算数运算符：+  -  *      /  %  ++      --

	​	两个数字相加，返回数字相加的和
	​	如果数字与字符串相加，返回字符串

- 赋值运算符：=  +=  -=      *=  /+  %/ 

- 比较运算符：==  \===      !=  !==  >      <  >=  <=

- 逻辑运算符：&&  ||      !

- 条件运算符：即三目运算符

**10）关键字**

|          |            |            |              |
| -------- | ---------- | ---------- | ------------ |
| abstract | else       |instanceof  | super        |
| boolean  | enum       | int        | switch       |
| break    | export     | interface  | synchronized |
| byte     | extends    | let        | this         |
| case     | false      | long       | throw        |
| catch    | final      | native     | throws       |
| char     | finally    | new        | transient    |
| class    | float      | null       | true         |
| const    | for        | package    | try          |
| continue | function   | private    | typeof       |
| debugger | goto       | protected  | var          |
| default  | if         | public     | void         |
| delete   | implements | return     | volatile     |
| do       | import     | short      | while        |
| double   | in         | static     | with         |

**11）语句**

- JavaScript 语句向浏览器发出的命令。语句的作用是告诉浏览器该做什么

- 语句用分号分隔

- 对代码行进行折行

	JavaScript 是脚本语言，浏览器会在读取代码时，逐行地执行脚本代码。而对于传统编程来说，会在执行前对所有代码进行编译

	```javascript
	//可以在文本字符串中使用反斜杠对代码行进行换行
	    document.write("你好 \
	    世界!");
	//但是不能这样
	    document.write \ 
	    ("你好世界!");
	```

| **语句**     | **描述**                                                     |
| ------------ | ------------------------------------------------------------ |
| break        | 用于跳出循环。                                               |
| catch        | 语句块，在 try 语句块执行出错时执行 catch 语句块。           |
| continue     | 跳过循环中的一个迭代。                                       |
| do ... while | 执行一个语句块，在条件语句为 true 时继续执行该语句块。       |
| for          | 在条件语句为 true 时，可以将代码块执行指定的次数。           |
| for ... in   | 用于遍历数组或者对象的属性（对数组或者对象的属性进行循环操作）。 |
| function     | 定义一个函数                                                 |
| if ... else  | 用于基于不同的条件来执行不同的动作。                         |
| return       | 退出函数                                                     |
| switch       | 用于基于不同的条件来执行不同的动作。                         |
| throw        | 抛出（生成）错误 。                                          |
| try          | 实现错误处理，与 catch 一同使用。                            |
| var          | 声明一个变量。                                               |
| while        | 当条件语句为 true 时，执行语句块。                           |

**12）其它**

- 注释：使用双斜杠 // 多行注释/* */
- js是对大小写是敏感的
- js使用Unicode字符集
- 和Java差不多，也有Data，Math，等对象

## 数据类型

**1）动态类型**

JavaScript 拥有动态类型，这意味着相同的变量可用作不同的类型，js有多重数据类型

- 值类型(基本类型)：字符串（String）、数字(Number)、布尔(Boolean)、对空（Null）、未定义（Undefined）、Symbol(表示独一无二的值)。

	- javaScript中有五种空值和假值，分别为false，null，undefined，“”，0。从广义上来说，这五个值都是对应数据类型的无效值或空值
	- null表示的是一个对象的值，而并不是一个字符串。例如声明一个对象的引用，String a = null ;
	- ""表示的是一个空字符串，也就是说它的长度为0。例如声明一个字符串String str = "" 

- 引用数据类型：对象(Object)、数组(Array)、函数(Function)

	```javascript
	var length = 16;                                  // Number 通过数字字面量赋值
	var points = x * 10;                              // Number 通过表达式字面量赋值
	var lastName = "Johnson";                         // String 通过字符串字面量赋值
	var cars = ["Saab", "Volvo", "BMW"];              // Array  通过数组字面量赋值
	var person = {firstName:"John", lastName:"Doe"};  // Object 通过对象字面量赋值
	```

**2）字符串**

字符串是存储字符的变量，可以是引号中的任意文本，可以使用单引号或双引号

- 可以使用索引位置来访问字符串中的每个字符，字符串的索引从 0 开始

	```javascript
	var carname = "Volvo XC60";
	var character = carname[7];
	```

- 可以使用内置属性 length 来计算字符串的长度

	`var sln = carname.length;`

- 转义字符

	```
	\'	单引号
	\"	双引号
	\\	反斜杠
	\n	换行
	\r	回车
	\t	tab(制表符)
	\b	退格符
	\f	换页符
	```

- 字符串方法

	```javascript
	charAt()	                  返回指定索引位置的字符
	charCodeAt()	              返回指定索引位置字符的 Unicode 值
	concat()	                  连接两个或多个字符串，返回连接后的字符串
	fromCharCode()	              将 Unicode 转换为字符串
	indexOf()	                  返回字符串中检索指定字符第一次出现的位置
	lastIndexOf()	              返回字符串中检索指定字符最后一次出现的位置
	localeCompare()	              用本地特定的顺序来比较两个字符串
	match()	                      找到一个或多个正则表达式的匹配
	replace()	                  替换与正则表达式匹配的子串
	search()	                  检索与正则表达式相匹配的值
	slice()	                      提取字符串的片断，并在新的字符串中返回被提取的部分
	split()	                      把字符串分割为子字符串数组
	substr()	                  从起始索引号提取字符串中指定数目的字符
	substring()	                  提取字符串中两个指定的索引号之间的字符
	toLocaleLowerCase()	          根据主机的语言环境把字符串转换为小写，只有几种语言（如土耳其语）具有地方特有的大小写映射
	toLocaleUpperCase()	          根据主机的语言环境把字符串转换为大写，只有几种语言（如土耳其语）具有地方特有的大小写映射
	toLowerCase()	              把字符串转换为小写
	toString()	                  返回字符串对象值
	toUpperCase()	              把字符串转换为大写
	trim()	                      移除字符串首尾空白
	valueOf()	                  返回某个字符串对象的原始值
	```

**3）数字**

js 只有一种数字类型。数字可以带小数点，也可以不带

- NaN：非数字值

	- NaN 属性是代表非数字值的特殊值。
	- 该属性用于指示某个值不是数字，可以把 Number 对象设置为该值，来指示其不是数字值

- 数字可以是数字或者对象

	```javascript
	var x = 123;
	var y = new Number(123);
	typeof(x) // 返回 Number
	typeof(y) // 返回 Object
	```

- Number属性

	```javascript
	Number.MAX_VALUE	最大值
	Number.MIN_VALUE	最小值
	Number.NaN	        非数字
	```

- Number方法 

	```javascript
	Number.parseFloat()	 将字符串转换成浮点数，和全局方法 parseFloat() 作用一致。
	Number.parseInt()	 将字符串转换成整型数字，和全局方法 parseInt() 作用一致。
	
	Number.isFinite()	 判断传递的参数是否为有限数字。
	Number.isInteger()	 判断传递的参数是否为整数。
	Number.isNaN()	     判断传递的参数是否为 isNaN()。
	```

**4）布尔值**

只能有两个值：true 或 false

```javascript
var x=true;
var y=false;
```

**5）数组**

- 在一个数组中你可以有不同的对象，所有的js变量都是对象，数组是对象，函数是对象

- 下标从0开始

	```javascript
	var cars=new Array();
	cars[0]="Saab";
	cars[1]="Volvo";
	cars[2]="BMW";
	//或者 
	var cars=new Array("Saab","Volvo","BMW");
	//或者
	var cars=["Saab","Volvo","BMW"];
	```

- 数组方法和属性

	```javascript
	var x=myCars.length             // myCars 中元素的数量
	var y=myCars.indexOf("Volvo")   // "Volvo" 值的索引值
	```

**6）对象**

JavaScript 对象是拥有属性和方法的数据。在js中，几乎所有的事物都是对象。

对象由==花括号分隔==。在括号内部，对象的属性以名称和值对的形式` (name : value) `来定义。属性由逗号分隔

`var person={firstname:"John", lastname:"Doe", id:5566};`

- 创建对象

	- 使用 Object 定义并创建对象的实例：js中，几乎所有的对象都是 Object 类型的实例

		```javascript
		person=new Object();
		person.firstname="John";
		person.lastname="Doe";
		person.age=50;
		person.eyecolor="blue";
		//也可以使用对象字面量来创建对象
		person={firstname:"John",lastname:"Doe",age:50,eyecolor:"blue"};
		```

	- 使用对象构造器

		在js中，this通常指向的是我们正在执行的函数本身，或者是指向该函数所属的对象（运行时）

		```javascript
		function person(firstname,lastname,age,eyecolor)
		{
		    this.firstname=firstname;
		    this.lastname=lastname;
		    this.age=age;
		    this.eyecolor=eyecolor;
		}
		myFather=new person("John","Doe",50,"blue")
		```

- 对象属性

	对象属性有两种寻址方式

	```javascript
	name=person.lastname;
	name=person["lastname"];
	```

- 对象方法

	方法只不过是附加在对象上的函数，并作为对象的属性存储

	- 创建对象方法

		`methodName : function() { //代码 }`

		```javascript
		var person = {
		    firstName: "John",
		    lastName : "Doe",
		    id : 5566,
		    fullName : function() 
		    {
		       return this.firstName + " " + this.lastName;
		    }
		};
		```

	- 在构造器函数内部定义对象的方法

		```javascript
		changeName() 函数 name 的值赋给了person 的 lastname 属性
		function person(firstname,lastname,age,eyecolor)
		{
		    this.firstname=firstname;
		    this.lastname=lastname;
		    this.age=age;
		    this.eyecolor=eyecolor;
		
		    this.changeName=changeName;
		    function changeName(name)
		    {
		        this.lastname=name;
		    }
		}
		```

	- 访问对象方法

		`objectName.methodName()`

		如果你要访问 person 对象的 fullName 属性，它将作为一个定义函数的字符串返回
		即，通常 fullName() 是作为 person 对象的一个方法， fullName 是作为一个属性。
		如果使用 fullName 属性，不添加 (), 它会返回函数的定义   

**7）Undefined 和 Null**

Undefined 这个值表示变量不含有值。

可以通过将变量的值设置为 null 来清空变量

**8）声明变量类型**

```javascript
var carname=new String;
var x=new Number;
var y=new Boolean;
var cars=new Array;
var person=new Object;
```

**9）如果把值赋给尚未声明的变量，该变量将被自动作为 window 的一个属性**

```javascript
carname="Volvo";  
//如果carname没有声明，那么将声明 window 的一个属性carname
```

## 函数

函数是由事件驱动的或者当它被调用时执行的可重复使用的代码块

```html
<head>
<meta charset="utf-8">
<title>测试实例</title>
<script>
    function myFunction()
    {
        alert("Hello World!");
    }
</script>
</head>

<body>
    <button onclick="myFunction()">点我</button>
</body>
```

**1）语法**

函数就是包裹在花括号中的代码块，前面使用了关键词 function

当调用该函数时，会执行函数内的代码。可以在某事件发生时直接调用函数（比如当用户点击按钮时），并且可由 JavaScript 在任何位置进行调用

**2）带参数的函数**

JavaScript 函数对参数的值没有进行任何的检查

函数显式参数在函数定义时列出
函数隐式参数在函数调用时传递给函数真正的值

- 参数规则

	- 函数定义显式参数时没有指定数据类型
	- 函数对隐式参数没有进行类型检测
	- 函数对隐式参数的个数没有进行检测

- ES6 函数可以自带参数

	ES6 支持函数带有默认参数

	```javascript
	function myFunction(x, y = 10) {
	    // y is 10 if not passed or undefined
	    return x + y;
	}
	myFunction(0, 2) // 输出 2
	myFunction(5); // 输出 15, y 参数的默认值
	```

**3）带有返回值的函数**

使用 return 语句

```javascript
function myFunction()
{
    var x=5;
    return x;
}
```

**4）变量的作用域**

- 局部变量

	在函数内部声明的变量是局部变量，所以只能在函数内部访问它，即该变量的作用域是局部的。

	可以在不同的函数中使用名称相同的局部变量。只要函数运行完毕，本地变量就会被删除。

- 全局变量

	在函数外声明的变量是全局变量，网页上的所有脚本和函数都能访问它

- 变量的生命周期

	javaScript 变量的生命期从它们被声明的时间开始

	局部变量会在函数运行以后被删除。

	全局变量会在页面关闭后被删除。

- 块级作用域

	ES6新加入了let 和 const

	- let：声明的变量只在 let 命令所在的代码块内有效

		```javascript
		{ 
		    let x = 2;
		}
		// 这里不能使用 x 变量
		
		//eg2:
		var x = 10;
		// 这里输出 x 为 10
		{ 
		    let x = 2;
		    // 这里输出 x 为 2
		}
		// 这里输出 x 为 10
		```

	- const：用于声明一个或多个常量，声明时必须进行初始化，且初始化后值不可再修改

		```javascript
		const PI = 3.141592653589793;
		PI = 3.14;      // 报错
		PI = PI + 10;   // 报错
		```

	- const定义常量与使用let 定义的变量相似

		- 二者都是块级作用域

		- 都不能和它所在作用域内的其他变量或函数拥有相同的名称

	- const与let区别

		- const声明的常量必须初始化，而let声明的变量不用
		- const 定义常量的值不能通过再赋值修改，也不能再次声明。而 let 定义的变量值可以修改

- 循环作用域

	java，c，js都不一样。java只有局部变量，成员变量，静态变量

	```javascript
	//eg：在代码块内使用了 var 关键字，它声明的变量是全局的，包括循环体内与循环体外
	var i = 5;
	for (var i = 0; i < 10; i++) {
	    // 一些代码...
	}
	// 这里输出 i 为 10
	
	//eg2：在代码块内使用 let 关键字， 它声明的变量作用域只在循环体内，循环体外的变量不受影响
	let i = 5;
	for (let i = 0; i < 10; i++) {
	    // 一些代码...
	}
	// 这里输出 i 为 5
	```

- 在 HTML 中, 全局作用域是针对 window 对象，使用 var 关键字声明的全局作用域变量属于 window 对象

	使用 let 关键字声明的全局作用域变量不属于 window 对象

	```javascript
	var carName = "Volvo";
	// 可以使用 window.carName 访问变量
	let carName = "Volvo";
	// 不能使用 window.carName 访问变量
	```

**5）this**

面向对象语言中this表示当前对象的一个引用。

但在 JavaScript 中this不是固定不变的，它会随着执行环境的改变而改变

在方法中，this 表示该方法所属的对象。
如果单独使用，this 表示全局对象。
在函数中，this 表示全局对象。
在函数中，在严格模式下，this 是未定义的(undefined)。
在事件中，this 表示接收事件的元素。
类似 call() 和 apply() 方法可以将 this 引用到任何对象。

```javascript
//eg：this 指向了 person 对象，因为 person 对象是 fullName 方法的所有者
var person = {
  firstName: "John",
  lastName : "Doe",
  id     : 5566,
  fullName : function() {
    return this.firstName + " " + this.lastName;
  }
};
```

**6）void**

指定要计算一个表达式但是不返回值

```javascript
void func()
javascript:void func()
```

**7）函数调用**

有 4 种调用方式，每种方式的不同在于 this 的初始化

- 作为一个函数调用

	以下函数不属于任何对象。但是在 JavaScript 中它始终是默认的全局对象。

	在 HTML 中默认的全局对象是 HTML 页面本身，所以函数是属于 HTML 页面
	在浏览器中的页面对象是浏览器窗口(window 对象)。以上函数会自动变为 window 对象的函数

	```javascript
	//myFunction() 和 window.myFunction() 是一样的：
	function myFunction(a, b) {
	    return a * b;
	}
	myFunction(10, 2);
	```

- 全局对象

	当函数没有被自身的对象调用时 this 的值就会变成全局对象

	在 web 浏览器中全局对象是浏览器窗口（window 对象）

	```javascript
	//以下例子返回 this 的值是 window 对象
	function myFunction() {
	    return this;
	}
	myFunction();                // 返回 window 对象
	```

- 函数作为方法调用

	```javascript
	//可以将函数定义为对象的方法
	var myObject = {
	    firstName:"John",
	    lastName: "Doe",
	    fullName: function () {
	        return this.firstName + " " + this.lastName;
	    }
	}
	myObject.fullName();         // 返回 "John Doe"
	
	//函数作为对象方法调用，会使得 this 的值成为对象本身
	var myObject = {
	    firstName:"John",
	    lastName: "Doe",
	    fullName: function () {
	        return this;
	    }
	}
	myObject.fullName();          // 返回 [object Object] (所有者对象)
	```

- 使用构造函数调用函数

	如果函数调用前使用了 new 关键字, 则是调用了构造函数

	```javascript
	// 构造函数:
	function myFunction(arg1, arg2) {
	    this.firstName = arg1;
	    this.lastName  = arg2;
	}
	
	// This    creates a new object
	var x = new myFunction("John","Doe");
	x.firstName;                             // 返回 "John"
	```

- 作为函数方法调用函数

	在 JavaScript中, 函数是对象。JavaScript函数有它的属性和方法。

	call()和apply() 是预定义的函数方法。两个方法可用于调用函数，两个方法的第一个参数必须是对象本身

	``` javascript
	function myFunction(a, b) {
	    return a * b;
	}
	myObject = myFunction.call(myObject, 10, 2);     // 返回 20
	
	function myFunction(a, b) {
	    return a * b;
	}
	myArray = [10, 2];
	myObject = myFunction.apply(myObject, myArray);  // 返回 20
	
	//两个方法都使用了对象本身作为第一个参数。 两者的区别在于第二个参数： 
	//apply传入的是一个参数数组，也就是将多个参数组合成为一个数组传入
	//而call则作为call的参数传入（从第二个参数开始）。
	```

## 语句

和java差不多

**1）条件语句**

**2）Switch语句**

**3）for循环**

**4）for/in循环****

循环遍历对象的属性

```javascript
var person={fname:"Bill",lname:"Gates",age:56}; 

for (x in person)  // x 为属性名
{
    txt=txt + person[x];
}
```

**5）while循环**

**6）break和continue**

break 语句用于跳出循环。

continue 用于跳过循环中的一个迭代

**7）typeof，null和undefined**

null 和 undefined 的值相等，但类型不等

- 使用 typeof 操作符来检测变量的数据类型

	数组是一种特殊的对象类型，算是object

	```javascript
	typeof "John"                // 返回 string
	typeof 3.14                  // 返回 number
	typeof false                 // 返回 boolean
	typeof [1,2,3,4]             // 返回 object
	typeof {name:'John', age:34} // 返回 object
	```

- null

	null是一个只有一个值的特殊类型，表示一个空对象引用。可以设置为 null 来清空对象

	用 typeof 检测 null 返回是object

- undefined

	是一个没有设置值的变量

	typeof 一个没有值的变量会返回 undefined

## 类型转换

**1）数据类型**

- JS中有 6 种不同的数据类型

	string  number  boolean

	object  function  symbol

- 3 种对象类型

	Object  Date  Array

- 2 个不包含任何值的数据类型

	null  undefined

**2）全局方法 String() **

可以将数字，布尔值，日期 转换为字符串。toString() 也是有同样的效果

```javascript
String(123)      
String(100 + 23) 
(123).toString()

String(false) 
String(true)

false.toString() 
true.toString()  

String(new Date())
```

**3）全局方法 Number() **

可以将字符串，布尔值，日期等转换为数字

```javascript
Number("3.14")    // 返回 3.14，字符串包含数字(如 "3.14") 转换为数字 (如 3.14).
Number(" ")       // 返回 0，空字符串转换为 0
Number("")        // 返回 0
Number("99 88")   // 返回 NaN，其他的字符串会转换为 NaN (不是个数字)

Number(false)     // 返回 0
Number(true)      // 返回 1
```

**4）自动转换类型**

当 JavaScript 尝试操作一个 "错误" 的数据类型时，会自动转换为 "正确" 的数据类型。

``` javascript
5 + null    // 返回 5         null 转换为 0
"5" + null  // 返回"5null"   null 转换为 "null"
"5" + 1     // 返回 "51"      1 转换为 "1" 
"5" - 1     // 返回 4         "5" 转换为 5
```

**5）自动转换为字符串**

你尝试输出一个对象或一个变量时 JavaScript 会自动调用变量的 toString() 方法

## 异常处理

和java差不多

 

try 语句测试代码块的错误。

catch 语句处理错误。

throw 语句创建自定义错误。

finally 语句在 try 和 catch 语句之后，无论是否有触发异常，该语句都会执行

## 表单验证

## JSON

## DOM

## BOM

## JS模块

## jQuery

## Ajax









# Vue3

## Node.js

1. 下载安装

	https://nodejs.org/en/

	http://nodejs.cn/download/

2. 全局配置

	配置npm安装的全局模块所在的路径，以及缓存cache的路径，不然执行`npm install XXX -g` global全局安装时，会安装到C:\Users\用户名\AppData\Roaming\npm中

	``` bash
	#在node.js安装文件夹下，创建node_global和node_cache文件夹，然后执行下面的
	npm config set prefix "D:\install\nodejs\node_global"
	npm config set cache "D:\install\nodejs\node_cache"
	```

	> ==配置完成后会在用户目录下生成.npmrc文件，不要删除==

3. 配置环境变量

	将`D:\install\nodejs\node_global`和`D:\install\nodejs\node_global\node_modules` 添加到path中

4. npm

	node.js自带了软件安装包管理工具npm

	``` bash
	#在管理员权限下
	
	#查看nodejs版本
	node -v
	#查看npm版本
	npm -v
	
	#升级 npm
	npm install -g npm
	#使用淘宝的镜像及其命令cnpm
	#升级或安装 cnpm
	npm install cnpm -g
	
	#查看全局已安装
	npm ls -g
	#查看当前项目已安装包（项目跟目录必须有 package.json 文件）
	npm ls
	#卸载全局包
	npm uninstall 
	```



## 起步

构建（以管理员启动）

- 直接通过idea执行`npx create-vue` 即可构建一个基于vite的vue3项目
- 在命令行`npm init vue@latest`  即会安装并执行`create-vue`，它是Vue官方的项目脚手架工具（vue cli已停止更新）
	- pinia：类似于vuex的状态管理库
	- ESLint：代码检测工具，解决代码质量问题
	- Prettier：代码格式化工具，解决代码风格问题
	- package.json：它是项目的配置文件，常见的配置有配置项目启动、打包命令，声明依赖包等。它不会自动生成，需要我们使用命令创建，npm 为我们提供了创建 package.json 文件的命令 npm init。
	- package-lock.json：是自动生成的，我们使用 npm install 安装包后就会自动生成。package.json里面的包版本不是一个具体的版本，而是一个最优版本，而package-lock.json里面定义的是某个包的具体版本，以及包之间的层叠关系。

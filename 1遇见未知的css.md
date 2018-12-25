# 遇见未知的css
## 1.1 在css中会遇到什么问题
#### 1.在css中为什么要有层叠
在css中可能会有多个样式表同时影响同一个元素的某个属性，设计这个功能的主要原因有2个，解决模块化和作者、用户、用户代理样式冲突。
<br/>
(1)  模块化 <br/>
一个页面中的样式可以拆分成多个样式表，代码如下：
```
    @import url(style/base.css);
    @import url(style/layer.css);
```
但是，如果对某个元素的同一个属性设置样式，到底是应用谁的呢？
<br/> 
(2) 作者/用户/用户代理<br/>
    当作者（写代码的人）和用户（浏览页面的人），以及用户代理（一般指浏览器）都能更改样式表时，也会产生同样的问题：究竟用谁的设置的样式，因此，css层叠机制就显得格外重要。
#### 2. 为什么“@import”指令需要写在样式表的开头
<br/> 代码如下：<br/>
```
    @import url(style/layer.css)
    body{
        background-color: red;
    }
```
<br/>
"@import"指令写在开头是为了可以使后面的样式能更好地层叠导入进来的样式.如果想更改导入进来的其中一个样式，可在`<style>`元素中，添加相应的元素的样式。
#### 3. 当Css值为0时，为什么可以省略单位？
> 答：因为当css值为0时，任何单位的结果都是一样的，就像数学中的0乘以任何数都等于0
#### 4. margin垂直外边距折叠的意义是什么？
> 答： margin垂直外边距折叠的特性主要来自于排版。举个例子，代码如下：
```
    <style>
    body,ul,li{
        margin:0;
        padding:0;
    }
    ul{
        list-style:none;
    }
    ul>li{
        margin: 20px 0;
    }
    </style>
    <ul>
        <li> 1111111 </li>
        <li> 2222222 </li>
        <li> 3333333 </li>
    </ul>
```
**[外边距合并](http://www.w3school.com.cn/css/css_margin_collapsing.asp)指的是，当两个垂直外边距相遇时，它们将形成一个外边距。合并后的外边距的告诉等于两个发生合并的外边距的高度中的较大者**
 
 * 当一个元素（元素有内容）出现在另一个元素上面时，第一个元素的下边距与第二个元素的上边距会发生合并。
 * 当一个元素包含在另一个元素中时（假设没有内边距或边框把外边距分隔开），它们的上和/或下外边距也会发生合并。
 * 另外还有一种情况，就是外边距可以与自身发生合并
 * 若有个空元素，它有外边距，但是没有边框或填充。在这情况下，上外边距与下外边距就碰到了一起，它们会发生合并：
 * 如果这个外边距遇到另外一个元素的外边距，它还会发生合并：这也是一系列的段落元素占用空间会非常小的原因，因为它们的所有外边距都合并到了一起，形成了一个小的外边距。

*PS： 只有普通文档流中块框的垂直外边距才会发生外边距合并。行内框、浮动框或绝对定位之间的外边距不会合并；<br/>
浮动元素，inline-block元素，绝对定位元素和创建了块级格式化上下文的元素，不和它的子元素发生margin折叠*
##### 1.1.1 css层叠规则
在css中一个样式可能来自不同的地方，分别是作者、用户、用户代理。如果在这几个样式中，它们对同一个属性做了不同的操作，这就考虑到层叠样式中的权重大小问题。<br/>
	在层叠顺序中，以下权重值从小到大。
	<ol>
		<li>用户代理样式；</li>
		<li>用户一般样式；</li>
		<li>作者一般样式；</li>
		<li>作者重要样式；(!import)</li>
		<li>用户重要样式；(!import)</li>
		<li>如果是两个样式来自相同的代码，如都来自作者（代码），并且它们的声明同样重要，则根据特异度来计算，特异度高的会覆盖特异度低的；</li>
		<li>如果特异度也相同，则越往后的样式的优先级越高。</li>
	</ol>
###### ！import声明规则
!import 声明的样式比一般声明优先级高，并且用户设置的！import比作者设置的！import优先级高。
###### 选择器特异度的计算
 * 如果一个声明出现在元素的style属性中，则a计为1；
 * b等于选择器重所有id选择器加起来的数量和；
 * c等于选择器重所有class选择器和属性选择器，以及伪类选择器加起来的数量和；
 * d等于选择器中所有标签选择器和伪元素选择器加起来的数量和；
 <br/>
将a、b、c、d这4个数字连接起来就组成了选择器的特异度。它们的比较顺序是从a开始比较，之后和b、c、d的值比较，谁的数大则优先级就越高。<br/>
	在使用css选择器时，注意：
	* 继承的优先级越低，没有特异度
	* 结合符(如+、>等）及通用选择符（*）特异度为0
	
##### 1.1.2 css的命令： 根据元素的class用意命名
---
#### 1.2 css的一些技巧
##### 1.2.1 使用pointer-events控制鼠标事件--控制元素什么时候响应鼠标事件
妙用： 将一个遮盖层的属性设置为pointer-events:none; 这样就可以点击遮盖层后面的内容<br/>
*注意：pointer-events:none;只是禁用鼠标事件，通过其他方式绑定的事件还是会触发的，比如键盘事件等。另外，如果讲一个元素的子元素pointer-events设置成其他值，如auto，那么当单击子元素时，还是会通过事件冒泡的形式触发父元素的事件。*
<br/>
例子：获取验证码-当用户点击‘获取验证码’按钮后，需等60s才能再次点击‘重新发验证码’按钮
> // pointer-events属性中有一个none值，将pointer-events的值设置成none就不会响应鼠标事件了。
```
	<style>
		a{color:red;}
		.disable{pointer-events:none;color:#666;}
	</style>
	<a href='javascript:;' id='btn'>发送验证码</a>
	<script>
		var oBtn = document.getElementById('btn');
		oBtn.onclick = function(){
			oBtn.classList.add('disable');
			setTimeout(function(){
				oBtn.classList.remove('disable');
			},3000);
		}
	</script>
```
###### 1.2.2 玩转css选择器
**案例**<br/>
> // 当只有一个子元素时，可以让它剧中显示，如果有多个子元素时，可以让它水平排列：

```
	<style>
		.box div{
			width:100px;
			height:100px;
			border:1px solid red;
			margin: 0 auto;	
		}
		.box div:not(:only-child){
			float: left;
			margin-left: 20px;	
		}
	</style>
	<div class="box">
		<div></div>
		<div></div>
		<div></div>
	</div>
```
<br/>
###### 1.2.3 利用padding实现元素等比例缩放

> padding和margin有一个奇怪的特点,它们的上下外边距的百分比是根据父元素的宽度计算的。

```
	<style>
		.box{
			width:30%;
			height:10px;
		}
		.box div{
			position: relative;
			overflow:hidden;
			background-color: red;
		}
		.box div:after{
			content:'';
			display:block;
			padding-top:100%;
		}
		.box div span{
			position:absolute;
			left:0;
			top:0;
		}
	</style>
	<div class="box">
		<div>
			<span>图片</span>
		</div>
	</div>
```

demo2 : 图片等比例缩放
```
	.photo a{
		position: relative;
		float:left;
		outline: 1px solid #dedede;
		width: calc(33.33%-1.6rem);
		margin: 1.2rem 0 0 1.2rem;
	}
	.photo a::before{
		content:'';
		display:block;
		padding-top:100%;
	}	
	.photo a img{
		position:absolute;
		left:0;
		right:0;
		width:100%;
		height:100%;
	}
```
另外做法：直接对a标签设置高度，单位使用vw。vw单位是相对于视口（屏幕）宽度的。但兼容性不好。代码如下：
```
	.photo a{
		float:left;
		width: calc(33.33%-1.6rem);
		height: calc(33.33vw-1.6rem);
		margin: 1.2rem 0 0 1.2rem;
		outline: 1px solid #dedede;
	}
	.photo a img{
		display:block;
		width:100%;
		height:100%;
	}
```

###### 1.2.4 calc函数：计算函数
注意：运算符前后都需要保留一个空格
**场景1：**
```
	<style>
		.box img{
			width:50%;
			float:left;
		}
		.box p{
			margin-left: calc(50% + 10px);
		}
	</style>
	<div class="box">
		<img src="" alt="" />
		<p>.......</p>
	</div>
```
<br/>
**场景2：保持同一行**
```
	<style>
		.box img{
			width:calc(25%-40px);
			float:left;
			margin:20px;
		}
	</style>
```
<br/>
**场景3：媒体查询**
```
	<style>
		.box img{
			width:calc(100%/4-40px);
			float:left;
			margin:20px;
		}
		@media (max-width:600px){
			.box img{
				width:calc(100%/2-40px);
				float:left;
				margin:20px;
			}
		}
	</style>
```
<br/>
这段代码表示在屏幕不小于600px时，一行最多可以放4张图片，如果屏幕小于或等于600px时，一行最多只能放2张

---
##### 1.3 隐藏元素
内容存在<br/>
1. 通过设置width:0;或height:0;隐藏一个元素。<br/>
2. 将元素透明度opacity设置为0;<br/>
3. 通过定位将元素移出屏幕范围：`div{position:absolute;left:-9999px}`<br/>
4. 通过text-indent实现隐藏文字效果：`div{text-indent:-999999px}`，但如果想要被搜索引擎搜索到，但又不想显示，则需要添加这段文字<br/>
5. 通过z-index隐藏一个元素：`div .item{position: absolute;z-index:-1;}`,但文字还是会被透出来，因为背景是透明的，可以给添加背景颜色<br/>
6. 通过给元素设置`overflow：hidden；`来隐藏元素；<br/>
7. 通过`visibility:hidden;`将元素设置为不可见，但还占位置。<br/>
8. 将背景设为透明，文字大小为0：`div{font-size:0;background-color:transparent;}`<br/>
9. 将元素的max-width或max-height设置为0,但是文字会溢出。<br/>
解决方法:文字大小设为0；使用代码`overflow:hidden;`对英文来说，还需要加个换行属性`word-break:break-all`，不然英文只会显示在同一行。
```
	<style> 
		h2{ max-width:0; word-break: break-all; }
	</style> 
	<h2>享受一片宁静的天空AAA</h2>
```
10. 通过transform的translate函数隐藏一个元素：`div{transform:translate(-99999px);}`,与`left:-99999px`原理一样<br/>
11. 将元素的缩放设置成0：`transform:scale(0);` <br/>
12. 让元素重叠：`div{transform: skew(90deg);}`,元素重叠了，类似width为0<br/>
13. 设置margin为负值：`margin-left:-99999px;`<br/>
14. 将元素裁剪：`-webkit-clip-path:polygon(0 0, 0 0, 0 0, 0 0);`<br/>
内容不存在<br/>
15. 通过display将元素彻底隐藏：`div{display:none;}`
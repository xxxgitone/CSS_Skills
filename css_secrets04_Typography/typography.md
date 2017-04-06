# 字体排版

### 1. 连字符断行
处理文字两端对齐，单独使用`text-align:justify`，能够对齐，但是不美观。

![enter description here][1]

结合`hyphens`属性可以实现连字符断行，会自动添加断行符（手动实现为&shy;），从而达到两端对齐，且美观的效果

``` css
text-align: justify;
hyphens: auto;
```

![enter description here][2]

### 2. 插入换行
比如制作定义列表

``` html
<dl>
	<dt>Name:</dt>
	<dd>Lea Verou</dd>
	
	<dt>Email:</dt>
	<dd>lea@verou.me</dd>
	
	<dt>Location:</dt>
	<dd>Earth</dd>
</dl>
```

```css
dd {
	margin: 0;
	font-weight: bold;
}
```

不过由于`dt`和`dd`都是块级元素，得到的结果是

![enter description here][3]

给他们指定`display:inline`后又是这样

![enter description here][4]

可以在所有关闭标签</dd>前添加`<br>`换行，但是会污染结构层代码，也不易维护。可以使用css中的换行符号，简写为‘\A’,用它来作为伪元素内容，将其添在每个dd元素后面。

``` css
dd::after {
		content: "\A";
	}
```

但是光这样设置并不会奏效，这段代码所做的其实只相当于在HTML代码中的所有关闭标签</dd>之前添加换行符。默认情况下，这些换行符会与相邻的其他空白符进行合并。空白符合并通常是一件好事，否则我们就得把整个HTML文档的源代码整理成一行里面，不过有时候我们希望保留源代码中的这些空白符合换行，代码块就是最好的例子。

![enter description here][5]

``` css
dt, dd {
	margin: 0;
	display: inline;
}
dd {
	margin: 0;
	font-weight: bold;
}

dd::after {
	content: "\A";
	white-space: pre;
}
```
用到了属性`white-space: pre;`，表示连续的空白符会被保留。在遇到换行符或者`<br>`元素时才会换行。

不过并不健壮，比如加一个dd内容

``` html
<dd>lea@verou.me</dd>
 <dd>leaverou@mit.edu</dd>
```

![enter description here][6]

由于我们每个`dd`后面都加了一个换行符，所以每个值都会被分到单独一行。如果不想单独分到一行，只想针对`dt`之前最后一个`dd`插入换行，可以考虑将换行符放在`dt`前面。

``` css
dt::before {
	content: "\A";
	white-space: pre;
}
```

但是会导致第一行变成空行，可以使用选择符来处理。

``` css
dd + dt::before {
	content: "\A";
	white-space: pre;
}

dd + dd::before {
	content: ', ';
	font-weight: normal;
}
```
使用兄弟选择器来处理，第二个表示相邻的`dd`之间添加逗号

![enter description here][7]

如果你的代码在多个连续的dd之间包含了（未加注释的）空白符，那么逗号的前面会有一个空格，使用负边距修复。

``` css
dd + dd::before {
	content: ', ';
	font-weight: normal;
	margin: -.25em;
}
```
### 3.文本行的斑马线
> 背景知识：css渐变，background-size，条纹背景，灵活的背景定位

制作表格的斑马线效果很简单，使用`:nth-child()/:nth-of-type()`即可

``` css
tr:nth-child(even) {
	background: rgba(0, 0, 0, .2);
}
```

但是把这种效果运用到文本的时候，使用伪元素的方式力不从心。

我们可以考虑给整个文本使用背景，使用css渐变直接生成背景图像，而且可以用em单位来设置背景尺寸，这样背景就可以自动生成使用`font-size`的变化。

代码文本

``` html
<pre><code>while (true) {
        var d = new Date();
        if (d.getDate()==1 &amp;&amp;
            d.getMonth()==3) {
            alert("TROLOLOL");
        }
    }</code></pre>
```
> `pre` 元素可定义预格式化的文本。被包围在 pre 元素中的文本通常会保留空格和换行符。而文本也会呈现为等宽字体。

运用条纹背景知识创建出水平条纹背景，`background-size`需要设置为`line-height`的两倍，因为每个背景单元需要覆盖两行代码。

``` css
pre {
	padding: .5em;
	line-height: 1.5;
	background: hsl(20, 50%, 95%);
	background-image: linear-gradient(rgba(120,0,0,.1) 50%, transparent 0);
	background-size: auto 3em;
	font-family: Consolas, Monaco, monospace;
}
```

![enter description here][8]

发现错位了，原因在于使用了.5em的内边距。可以使用`background-position`把条纹向底部移动`.5em`，但是不够灵活，如果决定调整内边距，还需要相应的修改背景定位值。可以使用`background-origin:content-box(之前有介绍)`来处理。

``` css
pre {
	padding: .5em;
	line-height: 1.5;
	background: hsl(20, 50%, 95%);
	background-image: linear-gradient(rgba(120,0,0,.1) 50%, transparent 0);
	background-size: auto 3em;
	background-origin: content-box;
	font-family: Consolas, Monaco, monospace;
}
```

![enter description here][9]

### 4.调整tab的宽度
在写文档或教程的时候，我们会用`pre`和`code`元素来显示代码，默认处理样式一般是

``` css
pre {
   padding: .5em;
   line-height: 1.5;
   background: hsl(20, 50%, 95%);
   font-family: Courier New, Courier, monospace;
}

code {
   font: inherit;
}
```

![enter description here][10]

可以看到代码的缩进非常那看，但是直接使用tab来控制的话也不是一件好事情，因为浏览器会把其宽度显示为8个字符。

我们可以使用CSS3中的一个属性`tab-size(IE11和edge不支持)`属性来解决，直接设置数字值就行

``` css
pre {
	tab-size:2;
}
```

![enter description here][11]

### 5.自定义下划线
css中给文本添加下划线的属性是`text-decoration:underline;`,但是不同的浏览器可能会显示不一样的效果

一般用法是通过边框来模拟

``` css
border-bottom: 1px solid gray;
text-decoration: none;
```
![enter description here][12]

这样可以实现自定义，可以根据自己的需要修改颜色、线宽等，但是这些下划线跟文本之间的间隙很大，位置甚至比字形的降部还要低。

``` css
display:inline-block;
border-bottom: 1px solid gray;
text-decoration: none;
line-height: .9;
```
添加几行代码进行修复，添加一个稍小的`line-height`。

![enter description here][13]

但是换行的时候会出问题，不会跟随换行

最终解决方案，使用`background-image`及其相关属性，利用之前所学的控制背景知识。

``` css
background: linear-gradient(gray, gray) no-repeat;
background-size: 100% 1px;
background-position: 0 1em;
```
![enter description here][14]

这里会发现，下划线会穿过某些字母（比如p和y）的降部，假如背景色是实色，就可以设置两层与背景色相同的`text-shadow`来模拟这种效果。

``` css
a {
	background: linear-gradient(gray, gray) no-repeat;
	background-size: 100% 1px;
	background-position: 0 1em;
	text-shadow: .05em 0 white, -.05em 0 white;
}
```
![enter description here][15]

使用此方法生成下划线的好处在于非常灵活。比如生成一条虚线

``` css
background: linear-gradient(90deg, grey 66%, transparent 0) repeat-x;
background-size: .2em 2px;
background-position: 0 1em;
```

![enter description here][16]

波浪线

``` css
background: linear-gradient(-45deg, transparent 40%, red 0, red 60%, transparent 0) 0 1em,
	linear-gradient(45deg, transparent 40%, red 0, red 60%, transparent 0) .1em 1em;
background-repeat: repeat-x;
background-size: .2em .1em;
text-shadow: .05em 0 white, -.05em 0 white;
```


![enter description here][17]


### 6.现实中的文字效果
#### 6.1 凸版印刷效果

> 实现原理:出现在底部的浅色投影（或者出现在顶部的暗色投影）会让人产生物体是凹进去平面内的感觉。同理，出现在底部的暗色投影（或者出现在顶部的浅色投影）会让人产生物体从平面上凸起的错觉。

当浅颜色背景上使用深色文字时，在底部加上浅色投影效果最佳。

``` css
p {
	padding: .8em 1em;
	background: hsl(210, 13%, 60%);
	color: hsl(210, 13%, 30%);   /*比背景颜色深*/
	text-shadow: 0 1px 1px hsla(0, 0%, 100%, .8);
}
```
![enter description here][18]

字体和背景的颜色互换后，背景颜色更深，最佳方案是在顶部使用深色投影

``` css
background: hsl(210, 13%, 30%);
color: hsl(210, 13%, 60%);
text-shadow: 0 -1px 1px black;
```

![enter description here][19]

#### 6.2 空心字效果
实现空心字很简单，通过`text-shadow`属性，为投影加上不同方向上的少量偏移

``` css
background: deeppink;
color: white;
text-shadow: 1px 1px black, -1px -1px black,
                1px -1px black, -1px 1px black;
```

![enter description here][20]

除此之外还可以重叠多层轻微模糊的投影来模拟描边，这种方法不需要设置偏移量

``` css
 text-shadow: 0 0 1px black,0 0 1px black,
               0 0 1px black,0 0 1px black,
               0 0 1px black,0 0 1px black;
```

![enter description here][21]

但是如果需要的描边越粗，效果越差，下面是一个偏移量为3px的效果。

![enter description here][22]

使用SVG效果最佳

``` html
<h1><svg width="2em" height="1.2em">
	<use xlink:href="#css"></use>
	<text id="css" y="1em">CSS</text>
</svg></h1>
```

样式
``` css
h1 text { fill: currentColor; }

h1 svg { overflow: visible;}

h1 use {
	stroke: black; 
	stroke-width: 6;
	stroke-linejoin: round;
}
```

![enter description here][23]

#### 6.3文字外发光效果
实现很简单，通过`text-shadow`属性，重叠几次即可，不需要考虑偏移量，颜色跟文本一致

``` css
a {
	text-decoration: none;
	color: #ffc;
	text-shadow: 0 0 .1em, 0 0 .3em;
}
```

![enter description here][24]

可以为鼠标悬停添加效果

``` css
 a {
	text-decoration: none;
	color: #ffc;
	transition: 1s;
}

a:hover {
	text-shadow: 0 0 .1em, 0 0 .3em;
}
```

还可以同时让字体变透明，更加炫酷

``` css
a:hover {
	color: transparent;
	text-shadow: 0 0 .1em white, 0 0 .3em white;
}
```

#### 6.4 文字凸起效果
一种常用思路是：使用`text-shadow`属性，设置一连串累加投影，不设置模糊并以1px的跨度逐渐错开，使颜色逐渐变暗，然后再底部加一层强烈模糊的暗投影，从而模拟完整的立体效果。

``` css
 background: #58a;
color: white;
text-shadow: 0 1px hsl(0, 0%, 85%),
	0 2px hsl(0, 0%, 80%),
	0 3px hsl(0, 0%, 75%),
	0 4px hsl(0, 0%, 70%),
	0 5px hsl(0, 0%, 65%),
	0 5px 10px black;
```

![enter description here][25]

使用预处理器来使代码简化

``` scss
 @mixin text-3d($color:white, $depth:5) {
		$shadows: ();
		$shadow-color: $color;

		@for $i from 1 through $depth {
			$shadow-color: darken($shadow-color, 10%);
			$shadows:append($shadows, 0 ($i * 1px) $shadow-color, comma);
		}

		color:$color;
		text-shadow: append($shadows, 0 (depth* 1px) 10px black, comma)
	}
```

这种效果可以多变，比如把所有的投影设为黑色，并且去掉最底层的投影。

``` css
text-shadow: 1px 1px black, 2px 2px black,
                3px 3px black,4px 4px black,
                5px 5px black,6px 6px black,
                7px 7px black,8px 8px black;
```

![enter description here][26]

### 7. 环形文字
借助内联SVG来解决这个难题。

>  让文本按照路径排列的基本方法就是用一个`<textPath>`元素来包裹住这段文本，再把它们装进一个`<text>`元素中。这个`<textPath>`元素还需要在他的ID属性中引用一个`<path>`元素，然后就可以用这个`<path>`元素来定义我们想要的路径。

```html
<div class="circular">
      <svg viewBox="0 0 100 100">
          <path d="M 0,50 a 50,50 0 1,1 0,1 z" id="circular"></path>
          <text><textPath xlink:href="#circular">
            circular reasoning works because      
          </textPath></text>
      </svg>
  </div>
```

样式

``` css
.circular path {fill: none;}

.circular {
	width: 30em;
	height: 30em;
	margin: 3em auto;
}

.circular svg {
	display: block;
	overflow: visible;
	transition: 10s linear transform;
}

.circular svg:hover {
	transform: rotate(-2turn);
}

.circular text { fill: currentColor }
```
![enter description here][27]


  [1]: ./images/01-1.png "01-1.png"
  [2]: ./images/01-2.png "01-2.png"
  [3]: ./images/02-1.png "02-1.png"
  [4]: ./images/02-2.png "02-2.png"
  [5]: ./images/02-3.png "02-3.png"
  [6]: ./images/02-4.png "02-4.png"
  [7]: ./images/02-5.png "02-5.png"
  [8]: ./images/03-1.png "03-1.png"
  [9]: ./images/03-2.png "03-2.png"
  [10]: ./images/04-1.png "04-1.png"
  [11]: ./images/04-2.png "04-2.png"
  [12]: ./images/05-1.png "05-1.png"
  [13]: ./images/05-2.png "05-2.png"
  [14]: ./images/05-3.png "05-3.png"
  [15]: ./images/05-4.png "05-4.png"
  [16]: ./images/05-5.png "05-5.png"
  [17]: ./images/05-6.png "05-6.png"
  [18]: ./images/06-1.png "06-1.png"
  [19]: ./images/06-2.png "06-2.png"
  [20]: ./images/06-3.png "06-3.png"
  [21]: ./images/06-4.png "06-4.png"
  [22]: ./images/06-5.png "06-5.png"
  [23]: ./images/06-6.png "06-6.png"
  [24]: ./images/06-7.png "06-7.png"
  [25]: ./images/06-8.png "06-8.png"
  [26]: ./images/06-9.png "06-9.png"
  [27]: ./images/07-1.png "07-1.png"
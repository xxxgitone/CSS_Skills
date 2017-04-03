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
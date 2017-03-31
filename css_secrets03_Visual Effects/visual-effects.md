# 视觉效果

### 1. 单侧投影

#### 1.1 单侧投影

大多数人使用box-shadow的方法是，制定三个长度和一个颜色值。

``` css
box-shadow: 2px 3px 4px rgba(0, 0, 0, .5);
```

![enter description here][1]

> box-shadow第一个长度表示水平方向投影，第二个为垂直方向投影，第三个为模糊半径。

投影绘制步骤：
* 以该元素的尺寸和位置画一个`rgba（0,0,0，.5）`的矩形
* 把它向右移`2px`，向下移3px
* 使用高斯模糊算法将它进行4px的模糊处理。这在本质上表示在阴影边缘发生阴影色和纯透明色之间的颜色过度长度近似模糊半径的两倍（这里为8px）
* 接下来，模糊后的矩形与原始元素的交集部分会被剪切掉，因此看起来像是在该元素后面。所以这里跟我们所理解的元素叠在模糊后矩形上层不同。所以说如果我们给元素设置一层半透明的背景，就看不到任何投影

使用`4px`的模糊半径意味着投影的尺寸会比元素本身的尺寸大约8px，因此投影的最外圈会从元素的四面向外显露出来。

* 设置偏移量，就可以把投影的顶部和左边隐藏起来，只要这两个方向上的偏移量不小于4px就行，不够会导致外漏的投影过于浓重

* 最终解决方案是使用`box-shadow`的第四个长度参数，排在模糊半径参数之后，称作扩张半径。这个参数会根据你指定的值去扩张或缩小（负值）投影的尺寸。比如一个-5px的扩张半径会把投影宽度和高度各减少10px（每边5px）

所以我们使用一个负的扩张半径，而他的值正好等于模糊半径

``` css
box-shadow: 0 5px 4px -4px black;
```

![enter description here][2]

现在只有底部才有一道投影了。

#### 1.2 邻边投影
可以使用之前说的设置偏移量，要么使用设置第四个长度参数

* 我们不应该把阴影缩的太小，而是把阴影藏进一侧，另一侧自然露出就好，因此，扩张半径不应该设为模糊半径的相反值，而是这个相反值的一半
* 需要指定两个偏移量，因为希望投影在水平和垂直方向上同时移动。它们的值需要大于或等于模糊半径的一半

``` css
box-shadow: 3px 3px 6px -3px black;
```

![enter description here][3]

#### 1.3 双侧投影
用上面的单侧投影（每边个一块）来达到目的。基本上就是把"单侧投影"中的技巧运用两次

``` css
box-shadow: 5px 0 5px -5px black,
                -5px 0 5px -5px black;
```

![enter description here][4]


### 2. 不规则投影

当元素添加了伪元素或半透明背景的时候，box-shadow会变得力不从心，出现一些问题，有些原理之前已经讲到过。

对下面三种情况的元素都使用了`box-shadow: .1em .1em .3em rgba(0, 0, 0, .5);`

比如，用伪元素生成气泡的小尾巴后，再对气泡使用投影，投影效果不会在小尾巴上显现。

![enter description here][5]

对元素使用了透明背景，投影会忽视透明部分。

![enter description here][6]

切角效果

![enter description here][7]

等等。

使用filter属性来解决，目前为止IE11还不支持，其他基本支持。filter实行从SVG借鉴过来的。我们使用其中一个函数`drop-down()`，跟`box-shadow`很相像，但不包括扩张半径和`inset`值。

可以这样改写

``` css
filter: drop-shadow(.1em .1em .1em rgba(0,0,0,.5));
```

最终效果

![enter description here][8]

### 3. 染色效果

使用`<canvas>`也可以实现，这里探讨css方法的实现。

#### 3.1 基于滤镜的方案
由于没有一种现成的滤镜是专门为这个效果而设计的，所以需要使用多个滤镜组合起来。

第一个滤镜是`sepia()`,它会给图片增加一种饱和度的橙黄色染色效果，几乎所有的色相值都被收敛在35~40之间。

``` css
filter: sepia();
```

![enter description here][9]

如果想要主色调的饱和度比这个高，可以用`staturate()`l滤镜来给每个像素提升饱和度。具体多少可以根据实际情况。

``` css
filter: sepia() saturate(4);
```

![enter description here][10]

但是我们想要一种亮粉色的，则还需要再添加一个`hue-rotate()`滤镜，把每个像素的色相以指定的度数进行偏移。为了把原来的色相值40提升至335，需要增大（335 - 40）= 295度

``` css
filter: sepia() saturate(4) hue-rotate(295deg);
```

![enter description here][11]

添加一个hover来触发切换

``` css
img {
	max-width: 250px;
	filter: sepia() saturate(4) hue-rotate(295deg);
	transition: 1s filter;
}

img:hover,img:focus {
	filter:none
}
```

#### 3.2 基于混合模式的方案

当两个元素叠加时，“混合模式”控制了上层元素的颜色与下层颜色进行混合的方式。用它来实现染色效果，需要用到的混合模式是`luminosity`，这种模式会保留上层元素的HSL亮度信息，并从它的下层吸取色相的饱和度信息。所以如果我们将主色调放在下层，待处理的图片放在上层，就相当于进行染色了。当然IE11肯定是不支持的

对一个元素设置混合模式，有两个属性可以用：`mix-blend-mode`可以为整个元素设置混合模式，`background-blend-mode`可以为每层背景单独指定混合模式。所以有两种选择

* 需要把图片包裹在一个容器中，并把容器的背景设置为我们想要的主色调
* 不用图片元素，而用div元素，把这个元素的第一层背景设置为要染色的图片第二层的背景设置为我们想要的主色调

``` html
 <a href="#">
	<img src="dog.jpg">
</a>
```

几行声明就可以解决

``` css
a {
		display: block;
		max-width: 250px;
		background: hsl(335, 100%, 50%);
	}

	img {
		width: 100%;
		mix-blend-mode: luminosity;
	}
```

`mix-blend-mode`是把整个元素向下进行混合，而不管下面是什么。因此只要把这个属性设置为`luminosity`混合模式，那图片总会跟某些东西进行混合。此外，使用`background-blend-mode`属性则可以让每层背景跟它的下层背景进行混合，但不关心元素之外是什么情况。所以，当我们只有一个背景图像以及一个透明的背景色时，就不会出现任何混合效果。利用此特点可以制作动画

``` html
<div class="dog" style="background-image: url('dog.jpg')">
    </div>
```

```css
.dog {
		width: 640px; height: 440px;
		background-size: cover;
		background-color: hsl(335, 100%, 50%);
		background-blend-mode: luminosity;
		transition: .5s background-color;
	}

	.dog:hover {
		background-color: transparent;
	}
```

两种方法都不够理想

* 图片的尺寸需要在css中写死
* 在语义上，这个元素不是一张图片

### 4. 毛玻璃效果

使用半透明颜色作为背景，然后将其放在图片或花哨的背景之上，会产生不错的效果。不过也可能导致文字很难阅读。

``` html
<main>
	<blockquote>
		“The only way to get rid of a temptation is to yield to it. Resist it, and your soul grows sick with longing for the things it has forbidden to itself, with desire for what its monstrous laws have made monstrous and unlawful.”
		<footer>- 
			<cite>Oscar Wilde, The Picture of Dorian Gray</cite>
		</footer>
	</blockquote>
</main>
```

关键css代码可能是

``` css
body {
		background: url("http://csssecrets.io/images/tiger.jpg") 0 / cover fixed;
	}

main {
	background: hsla(0,0%,100%,.25) border-box;
}
```

最后效果

![enter description here][12]

上面的`fixed`表示`background-attachment:fixed`,设置背景图像固定，不会随着页面的其余部分滚动。

效果图文字有些难以看清，背景图过于花哨。可以通过提升背景色的不透明度来则增加文本的可读性，不过效果就那么生动。下面的图是将不透明度调整为.5，一点都不好看。

![enter description here][13]

解决这个问题的方案通常是将覆盖的那部分图片区做模糊处理。在css中可以使用滤镜，blur()对元素进行模糊处理。

不过直接处理也不行，会导致文字无法阅读。需要另找办法

![enter description here][14]

由于不能直接对元素本生进行模糊处理，可以选择对一个伪元素进行处理，然乎将它定位到元素下层。

下面是主要代码

``` css
main {
	position: relative;
	
	/*其他样式*/
}

body,main::before {
	background: url("http://csssecrets.io/images/tiger.jpg") 0 / cover fixed;
}
main::before {
		content: '';
		position: absolute;
		top: 0;right: 0;bottom: 0;left: 0; /*设置全部偏移量为0，可以将它完整覆盖在宿主元素上*/
		z-index: -1;
		filter: blur(20px);
	}
```

![enter description here][15]

将伪元素的背景图设置成body的一致，`z-index: -1`是将它置于`main`元素之下，然后对伪元素惊醒模糊处理。

大部分效果可以出来了，不过仔细可以看出来，模糊效果在接近边缘处会逐渐消退。因为模糊效果会削减实色像素所能覆盖的范围，削减的半径正是模糊半径的长度。可以对伪元素设置成红色背景，方便看清真像。

![enter description here][16]

可以让伪元素相对其宿主元素的尺寸再向外扩大至少`20px`(即模糊半径)，可以通过`-20px`的外变局来实现。保险起见，用一个更大的绝对值`（-30px）`.

![enter description here][17]

但是周边又出现一圈模糊效果，因为超出了容器，使用`hidden`即可

``` css
 main {
	 position: relative；
	overflow: hidden;
	background: hsla(0,0%,100%,.3) border-box;
}

body,main::before {
	background: url("http://csssecrets.io/images/tiger.jpg") 0 / cover fixed;
}

main::before {
	content: '';
	position: absolute;
	top: 0;right: 0;bottom: 0;left: 0;
	z-index: -1;
	filter: blur(20px);
	margin: -30px;
}
```

![enter description here][18]

现在毛玻璃效果完美了。

### 5. 折角效果

> 背景知识：css变形，css渐变、切角效果

#### 5.1 45°折角

第一步实现一个切角效果

``` css
div {
		background: #58a;
		background: linear-gradient(-135deg, transparent 2em, #58a 0);
	}
```

![enter description here][19]

然后增加一个暗色的三角形来实现翻折效果。实现方法就是增加一层渐变来生成这个三角形并将其定位在右上角，可以通过`background-size`来控制折角的大小。折页部分三角形放在切角渐变之上。

``` css
div {
	background: #58a;
				background: linear-gradient(to left bottom, 
					transparent 50%, rgba(0, 0%, 0, .4) 0) no-repeat 100% 0 / 2em 2em,
				   linear-gradient(-135deg, transparent 2em, #58a 0);
}
```

![enter description here][20]

并没有达到我们想要的效果，原因跟（二）中切角效果一样，第二层渐变中的`2em`折角尺寸是写在色标中的，因此是沿着渐变轴进行度量的，是对角线尺寸。然而在`background-size`中的`2em`长度是背景贴片的宽度和高，是在水平和垂直方向上度量的。

有两种方式调整：

* 保留对角线的`2em`长度，将`background-size`乘以`根号2`
* 保留水平和垂直方向上的`2em`，就要切角渐变的角标位置值除以`根号2`

但是推荐使用第二种方式，绝大多数的css度量都不是在对角线上的。所以色标的位置值为`2`除以`根号2`等于`根号2`，约等于`1.414`。

``` css
div {
	background: #58a;
				background: linear-gradient(to left bottom, 
					transparent 50%, rgba(0, 0%, 0, .4) 0) no-repeat 100% 0 / 2em 2em,
				   linear-gradient(-135deg, transparent 1.5em, #58a 0);
}
```

![enter description here][21]

#### 5.2 其他角度
45度角的折角不是很常用，可以改变折角角度，比如-150deg可以产生30度的切角，但也意味着计算折角三角形的边长也要复杂点了。

使用三角函数计算边长，折角三角形为30-60-90的直角三角形，所以

![enter description here][22]

``` css
background: #58a;
background: linear-gradient(to left bottom, 
	transparent 50%, rgba(0, 0, 0, .4) 0) no-repeat 100% 0 / 3em 1.73em,
   linear-gradient(-150deg, transparent 1.414em, #58a 0);
```

![enter description here][23]

这个折角并不真实，拿纸折一下就可以看出来了。

这个折角三角形需要旋转，但是背景不能旋转，可以使用伪元素。

``` css
div {
	position: relative;
	background: #58a;
	background: linear-gradient(-150deg, transparent 1.414em, #58a 0);

}

div::before {
	content: '';
	position: absolute;
	top: 0;right: 0;
	background: linear-gradient(to left bottom, 
		transparent 50%, rgba(0, 0, 0, .4) 0) no-repeat 100% 0 / 3em 1.73em;
	width: 3em;
	height: 1.7em;
}
```

实现了跟上面相同的效果，然后旋转

下一步将把折页三角形的宽高对调，以此改变它 的方向，然后再逆时针30来旋转这个折页三角形。

``` css
height: 1.73em;
width: 3em;
transform: rotate(-30deg);
```

![enter description here][24]

为了平移简单，将`transform-origin`设置为`bottom right`即三角形的右下角成为旋转的中心

![enter description here][25]

通过图和上面数据可知，垂直偏量为x - y = 3 - √3 = 1.3.

``` css
div::before {
		/*其他样式*/
		transform:  translateY(-1.3em) rotate(-30deg);
		transform-origin: bottom right;
	}
```

![enter description here][26]

为了更真实一点，增加圆角，渐变以及投影。

``` css
   div {
		position: relative;
		background: #58a;
		background: linear-gradient(-150deg, transparent 1.414em, #58a 0);
		border-radius: .5em;
	}

	div::before {
		content: '';
		position: absolute;
		top: 0;right: 0;
		background: linear-gradient(to left bottom, 
			transparent 50%, rgba(0, 0, 0, .2) 0, rgba(0, 0, 0, .4)) no-repeat 100% 0;
		width: 1.73em;
		height: 3em;
		transform:  translateY(-1.3em) rotate(-30deg);
		transform-origin: bottom right;
		border-bottom-left-radius: inherit;
		box-shadow: -.2em .2em .3em -.1em rgba(0, 0, 0, .15);
	}
```

![enter description here][27]

为了方便复，可以使用scss

``` scss
@mixin folded-corner($bg, $size, $angle:30deg){

	position: relative;
	background: $bg;
	background: linear-gradient($angle - 180deg, transparent $size, $bg 0);
	border-radius: .5em;

	$x: $size / sin($angle);
	$y: $size / cos($angle);

	&::before {
			content: '';
			position: absolute;
			top: 0;right: 0;
			background: linear-gradient(to left bottom, 
				transparent 50%, rgba(0, 0, 0, .2) 0, rgba(0, 0, 0, .4)) no-repeat 100% 0;
			width: $y
			height: $x;
			transform:  translateY($y - $x) rotate(2*$angle - 90deg);
			transform-origin: bottom right;
			border-bottom-left-radius: inherit;
			box-shadow: -.2em .2em .3em -.1em rgba(0, 0, 0, .2);
		}
	}
```













	
	


  [1]: ./images/01-1.png "01-1.png"
  [2]: ./images/01-2.png "01-2.png"
  [3]: ./images/01-3.png "01-3.png"
  [4]: ./images/01-4.png "01-4.png"
  [5]: ./images/02-1.png "02-1.png"
  [6]: ./images/02-2.png "02-2.png"
  [7]: ./images/02-3.png "02-3.png"
  [8]: ./images/02-4.png "02-4.png"
  [9]: ./images/03-1.png "03-1.png"
  [10]: ./images/03-2.png "03-2.png"
  [11]: ./images/03-3.png "03-3.png"
  [12]: ./images/04-1.png "04-1.png"
  [13]: ./images/04-2.png "04-2.png"
  [14]: ./images/04-3.png "04-3.png"
  [15]: ./images/04-4.png "04-4.png"
  [16]: ./images/04-5.png "04-5.png"
  [17]: ./images/04-6.png "04-6.png"
  [18]: ./images/04-7.png "04-7.png"
  [19]: ./images/05-1.png "05-1.png"
  [20]: ./images/05-2.png "05-2.png"
  [21]: ./images/05-3.png "05-3.png"
  [22]: ./images/05-4.png "05-4.png"
  [23]: ./images/05-5.png "05-5.png"
  [24]: ./images/05-6.png "05-6.png"
  [25]: ./images/05-7.png "05-7.png"
  [26]: ./images/05-8.png "05-8.png"
  [27]: ./images/05-9.png "05-9.png"
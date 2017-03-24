## 背景与边框
[TOC]
### 1.半透明边框
>  背景知识：`rgba/hsla`，在`rgb/hsl`的基础长增加了Alpha通道,用于设置颜色的不透明度（就是能透过多少背景）
*	`rgba`接受四个数值，即`rgba(red, green, blue,a)`,`rgb`可以为数值（0 ~ 255），也可以为百分比（0 ~ 100%），a表示不透明度(0~1),1为完全不透明，0表示完全透明。
	`rgba(0,255,0,1)` 表示纯绿色，完全不透明
	
	`rgba(100%,0,0，0.5)`表示纯红色，半透明
	
*  hsla(色相， 饱和度%， 亮度%，透明度)，格式为hsla(0, 0%,0%,0)
	hsla的第一个值表示色相，也就是一个实际的颜色，比如红色或者绿色。所有的颜色绕色相环（色轮）一周，色相值以圆周上的度数表示。
	
	![enter description here][1]
	
	红色是0或360，青色是180.以下是彩虹七色在色轮中的大致色相值。
	红：0；橙：35；黄：60；绿：125；蓝：230；靛：280；紫：305；
	
*	饱和度设定有多少颜色，灰色的饱和度低，而强烈的色彩饱和度高
*	亮度设定颜色的明暗，0%就是黑色，100%就是白色

> 难题

设计一个带有半透明边框的容器，可能很多人会这样写

	border: 10px solid hsla(0, 0%, 100%, 0.5);
	background: white;
	
但是实际情况，半透明的边框并不会显现出来。在边框中使用半透明并没有想象中的那么容易

![enter description here][2]

> 解决方案

出现这种情况是，背景会默认延伸到边框所在的区域下面。可以使用`background-clip`属性来处理。

background-clip  设置元素的背景（背景图片或颜色）是否延伸到边框下面，有三个可选值，默认为`border-box`

* `border-box`
	背景延伸到边框外沿（但是在边框之下）。

* `padding-box`
	边框下面没有背景，即背景延伸到内边距外沿。
	
* `content-box`
	背景裁剪到内容区 (content-box) 外沿。
	
有了这个属性可以很快解决这个问题了
	
	border: 10px solid hsla(0, 0%, 100%, 0.5);
    background: white;
    background-clip: padding-box;
	

![enter description here][3]

### 2. 多重边框
> 背景知识：`box-shadow` 描述阴影效果

#### 2.1 使用box-shadow来制作多重边框

	 background: yellowgreen;
	 box-shadow: 0 0 0 10px #655,
				0 0 0 15px deeppink,
				0 2px 5px 15px rgba(0, 0, 0, .6)
				
				
* 第一个length参数设置水平偏移量，如果是负值则阴影位于元素左边；
* 第二个设置垂直偏移量，如果是负值则阴影位于元素上面。
* 第三个值越大，模糊面积越大，阴影就越大越淡。 不能为负值。
* 第四个取正值时，阴影扩大；取负值时，阴影.收缩
* 第五个参数为颜色
* 还有一个参数用来设置阴影位置，默认在边框外，在最前面设置inset让阴影在边框内

![enter description here][4]

用border同样可以达到此效果，只不过更麻烦。用box-shadow通过逗号分隔可以创建任意数量的投影。

#### 2.2 通过outline制作

使用下面代码同样可以实现

	background: yellowgreen;
	border: 10px solid #655;
	outline: 5px solid deeppink;
	
outline还可以有一个好处就是可以制作虚线边框，还可以通过outline-offset属性来控制它跟元素边缘之间的距离，接受负值。

	background: black;
	outline: 1px dashed white;
	outline-offset: -10px;

![enter description here][5]

### 3. 灵活的背景定位
以往针对容器某个角对背景图片做偏移定位的时候，一般是这样写

``` css
background-position:bottom right
```
或者使用百分比数值代替bottom和right，但是还有更好的方案

#### 3.1 background-position的扩展语法方案
允许我们指定背景图片距离任意角的偏移量，只要在偏移量前面指定关键字

``` css
background: url(http://csssecrets.io/images/code-pirate.svg)
					no-repeat bottom right #58a;
background-position: right 20px bottom 10px;
```

上面的代码表示背景图片跟右边缘保持20px,同时跟底部保持10px；background属性里面的设置用于回退。

![enter description here][6]

#### 3.2 background-origin方案

此方案用于更好的解决背景图片偏移量和容器的内边距一样时的问题。

* border-box
	背景将会延伸到延伸到外边界的边框
	
* padding-box
	背景描绘在padding盒子，边框里不会有背景出现。同样，背景将会延伸到最外边界的padding.
	
* content-box
  背景描绘在内容区范围.

将`background-origin`设置成`content-box`即可

``` css
padding: 10px;
background: url(http://csssecrets.io/images/code-pirate.svg)
                    no-repeat bottom right #58a;
background-origin: content-box;
```

#### 3.3 calc()方案

``` scss
background-position: calc(100% - 20px) calc(100% - 10px)
```

### 4.边框内圆角

这个效果按自己的思路都可以实现。我的第一反应也是使用两个元素实现。

``` html
<div class="something-meaningful">
      <div>I have a nice subtle inner rounding, don’t I look pretty?</div>
 </div>
```
```css
.something-meaningful {
	background: #655;
	padding: .8em;
}

.something-meaningful > div {
	background: tan;
	border-radius: .8em;
	padding: 1em;
}
```

![enter description here][7]

当然这本书上肯定要讲述比较独特的方法实现。利用outline和box-shadow属性，只需写一个html属性即可

``` css
outline: .6em solid #655;
box-shadow: 0 0 0 .4em #655;
border-radius: .8em;
padding: 1em;
background: tan;
```

![enter description here][8]

分析其原理：
*	outline描边并不会跟着圆角走，当只是用outline的时候，圆角跟直角处有空隙

![enter description here][9]

* box-shadow会跟着圆角走，当只是用box-shadow的时候没有直角


![enter description here][10]

于是正好可以使用box-shadow来填充那部分空隙

### 5. 条纹图案
> 背景知识: 线性渐变`linear-gradient`、`background-size`
* `background-size` 设置背景图片大小，有多种写法
	* 使用关键字：`background-size： cover`，表示缩放背景图片以完全覆盖背景区，可能背景图片部分看不见。还有`background-size: contain`，缩放背景图片以完全装入背景区，可能背景区部分空白。
	* 使用一个值：`background-size: 3em`；这个值指定图片的宽度，图片的高度隐式的为auto，还可以使用百分比。
	* 两个值：`background-size: 3em 25%`，第一个值指定图片的宽度，第二个值指定图片的高度。可为数值，可为百分比。
	* 逗号分隔的多个值：设置多重背景

#### 5.1垂直条纹

尝试一个基本线性渐变

``` css
background: linear-gradient(#fb3, #58a);
```
最终效果是从上到下，第一个颜色渐变到第二个颜色

![enter description here][11]

添加数值，指定被填充的区域

```css
background: linear-gradient(#fb3 20%, #58a 80%);
```

![enter description here][12]

可以看到渐变的区域变小了

把区域设为50%

```css
background: linear-gradient(#fb3 50%, #58a 50%)
```

![enter description here][13]

发现已经看不到渐变区域了，如果多个色标具有相同的位置，它们会产生一个无限小的过渡区域，过度的起止变化分别是第一个和最后一个指定值，从效果上看，颜色会在那突然变化，而不是一个平滑的过度。

那么知道这个就可以配合`background-size`了

``` css
 background: linear-gradient(#fb3 50%, #58a 50%);
 background-size: 100% 30px;
```

![enter description here][14]

我们把两条条纹的高度设置成15px，由于背景默认是平铺的，整个容器也被填满了。

还可以创建不等宽的条纹

``` css
background: linear-gradient(#fb3 30%, #58a 30%);
background-size: 100% 30px;
```

![enter description here][15]

如果某个色标的位置值比整个列表中在它之前的色标的位置的值都要小，则该色标的位置值会被设置为它前面所有色标位置的最大值。所以利用这个特性，可以不用每次都修改两个数

``` css
background: linear-gradient(#fb3 30%, #58a 0);
background-size: 100% 30px;
```

创建超过两种颜色的条纹

![enter description here][16]

``` css
 background: linear-gradient(#fb3 33.3%, #58a 0, #58a 66.6%, yellowgreen 0);
 background-size: 45px;
```

#### 5.2垂直条纹
原理一样，稍微调整即可

``` css
background: linear-gradient(to right, #fb3 50%, #58a 0);
background-size: 30px 100%;
```

![enter description here][17]

通过 `to right` 改变渐变的起始位置，也可以写成角度，这里可以写`90deg`。
另外`background-size`的数值也要调换

#### 5.3 斜向条纹

使用`repeating-linear-gradient`快速构建

``` css
background: repeating-linear-gradient(45deg, #fb3, #58a 30px);
```
![enter description here][18]

可以很方便的变换想要的图形

``` css
background: repeating-linear-gradient(60deg, #fb3, #fb3 15px, #58a 0, #58a 30px);
```


![enter description here][19]

#### 5.4 灵活的同色系条纹
很多情况下，我们想要的条纹图案是由同一系色组成，只是在明暗方面有些差异。

``` css
background: repeating-linear-gradient(30deg, #79b, #79b 15px, #58a 0, #58a 30px);
```

![enter description here][20]

但是上面写法想要修改的时候需要修改四处颜色，改进的办法

``` css
background: #58a;
background-image: repeating-linear-gradient(30deg,
	  hsla(0,0%,100%,.1),
	  hsla(0,0%,100%,.1),15px,
	  transparent 0, transparent 30px);
```

这里把最深的颜色作为背景色，同时把半透明白色的条纹叠加在背景之上来得到浅色条纹。这样每次只要修改背景色就可以了。

### 6.复杂的背景图案
> 背景知识：css渐变, "条纹背景"

#### 6.1 网格
把多个渐变图案组合起来，让它们透过彼此的透明区域显现时，就会得到意想不到的图案。

``` css
background: white;
background-image: linear-gradient(90deg, rgba(200,0,0,.5), 50%, transparent 0),
                linear-gradient(rgba(200,0,0,.5) 50%, transparent 0);
 background-size: 30px 30px;
```

![enter description here][21]

有些时候我们希望网格中每个格子的大小可以调整，而网格线条的粗细同时保持固定。下面展示了使用长度而不是百分比作为色标的场景

``` css
 background: #58a;
background-image: linear-gradient(white 1px, transparent 0),
		  linear-gradient(90deg, white 1px, transparent 0);
background-size: 30px 30px;
```

![enter description here][22]

还可以把两幅不同线宽、不同颜色的网格图案叠加起来

``` css
background: #58a;
background-image: linear-gradient(white 2px, transparent 0),
	linear-gradient(90deg, white 2px, transparent 0),
	linear-gradient(hsla(0,0%,100%,.3) 1px, transparent 0),
	linear-gradient(90deg,hsla(0,0%,100%,.3) 1px, transparent 0);
background-size: 75px 75px, 75px 75px,15px 15px, 15px 15px;
```
![enter description here][23]

#### 6.2 波点
波点是利用径向渐变知识。下面是一个简单的径向渐变

``` css
background: #655;
background-image: radial-gradient(tan 30%, transparent 0);
background-size: 30px 30px;
```

![enter description here][24]

这里的径向渐变由每个小方块的中心向四周渐变，也就是`background-size`指定的大小。

下面是一种更加使用的图案：

``` css
background: #655;
background-image: radial-gradient(tan 30%, transparent 0),
	radial-gradient(tan 30%, transparent 0);
background-size: 30px 30px;
background-position: 0 0, 15px 15px;
```
使用`background-position`属性将两个背景位置错开，第一个值表示水平位置，第二个表示垂直位置，用逗号分隔设置多重。

![enter description here][25]

因为为了达到效果，第二层背景的偏移定位值必须是平铺图案中的每个基本单元宽高的一半，每次修改要修改多处尺寸，可以使用`scss`

``` scss
@mixin polka($size, $dot, $base, $accent) {
	  background: $base;
	  background-image: radial-gradient($accent $dot, transparent 0),
		  radial-gradient($accent $dot, transparent 0);
	  background-size: $size $size;
	  background-position: 0 0, $size/2 $size/2;
	}
```
```scss
@include polka(30px, 30%, #655, tan)
```

#### 6.3 棋盘
一步一步制作棋盘，首先回想一下制作三角形图案。要制作类似下面这张图案

![enter description here][26]

``` css
background: #eee;
background-image: linear-gradient(45deg, #bbb 50%, transparent 0);
background-size: 30px 30px;
```
![enter description here][27]

每个三角形占据每个单元的一般，修改色标位置为25%。

![enter description here][28]

我们把色标的顺序反转，便可以创建相反方向的三角形了

``` css
background: #eee;
background-image: linear-gradient(45deg, transparent 75%, #bbb 0);
background-size: 30px 30px;
```
![enter description here][29]

组合起来的图形

``` css
background: #eee;
background-image: linear-gradient(45deg, transparent 75%, #bbb 0),
	  linear-gradient(45deg, #bbb 25%, transparent 0);
background-size: 30px 30px;
```

![enter description here][30]

现在只需要把第二层渐变在水平和垂直方向均移动每个单元的一半，就可以拼接起来。

``` css
background: #eee;
background-image: linear-gradient(45deg, #bbb 25%, transparent 0),
		 linear-gradient(45deg, transparent 75%, #bbb 0);
background-size: 30px 30px;
background-position: 0 0, 15px 15px;
```
![enter description here][31]

产生出了一个正方形，本质上是棋盘的一半，复制一份再创建出另一组正方形。

``` css
background: #eee;
background-image: linear-gradient(45deg, #bbb 25%, transparent 0),
		 linear-gradient(45deg, transparent 75%, #bbb 0),
		 linear-gradient(45deg, #bbb 25%, transparent 0),
		 linear-gradient(45deg, transparent 75%, #bbb 0);
background-size: 30px 30px;
background-position: 0 0, 15px 15px,15px 15px, 30px 30px;
```

![enter description here][32]

代码进行优化，可以把这些处在单元顶角的三角形两两组合起来（即把第一组和第二组并为一层渐变，第三组和第四组为一层渐变）

``` css
background: #eee;
background-image: linear-gradient(45deg, rgba(0,0,0,.25) 25%, transparent 0,
		 transparent 75%, rgba(0,0,0,.25) 0),
		 linear-gradient(45deg, rgba(0,0,0,.25) 25%, transparent 0,
		 transparent 75%, rgba(0,0,0,.25) 0);
background-position: 0 0, 15px 15px;
background-size: 30px 30px;
```

使用scss

``` scss
@mixin checkerboard($size, $base, $accent: rgba(0,0,0,.25)) {
	  background: $base;
	  background-image: linear-gradient(45deg, $accent 25%, transparent 0,
				transparent 75%, $accent 0),
				linear-gradient(45deg, $accent 25%, transparent 0,
				transparent 75%, $accent 0);
	  background-position: 0 0, $size $size;
	  background-size: $size*2 $size*2;
	}
```
```scss
@include checkerboard(15px, #58a, tan);
```

### 7. 伪随机背景
重复平铺的几何图案很美观，但是看起来和你呆板。用css实现图案的随机平铺。

``` css
background: linear-gradient(90deg, #fb3 15%, #655 0, #655 40%, 
            #ab4 0, #ab4 65%, hsl(20, 40%, 90%) 0);
background-size: 80px 100%;
```
![enter description here][33]

上面通过`background-size`来控制图案每隔80px重复一次。规律比较明显，并不是真实的随机平铺。

接下来，把这组条纹从一个平面拆散为多个层面：一种颜色作为底色，另外三种颜色作为条纹，然后再让条纹以不同的间隔进行重复平铺。在色标中定好边框的宽度，用`background-siz`e来控制条纹的间距

``` css
background: hsl(20, 40%, 90%);
background-image:
	linear-gradient(90deg, #fb3 10px, transparent 0),
	linear-gradient(90deg, #ab4 20px, transparent 0),
	linear-gradient(90deg, #655 20px, transparent 0);
background-size: 80px 100%, 60px 100%, 40px 100%;
```

![enter description here][34]

但是这里还有一个问题，每隔240px还是会重复一次，红色箭头处。这里每次重复单元正好是`background-size`的最小公倍数，而40、60、80的最小公倍数就是240。

根据这个逻辑，可以把每个重复单元的尺寸最大化，也就是最小公倍数最大化。为了让最小公倍数最大化，这些数字最好是相对质数（比如，10和27不是质数，但他们是相对质数，最小公倍数为10乘以27）。要达成相对质数，尽量选择质数，于是下面代码

``` css
background: hsl(20, 40%, 90%);
background-image:
	linear-gradient(90deg, #fb3 11px, transparent 0),
	linear-gradient(90deg, #ab4 23px, transparent 0),
	linear-gradient(90deg, #655 41px, transparent 0);
background-size: 41px 100%, 61px 100%, 83px 100%;
```
![enter description here][35]

再想让它重复，早就超出屏幕的分辨率了。

这个技巧被Alex Walker定名为“蝉原则”，通过质数来增加随机真实性。还可以用于其他涉及有规律重复的情况

* 在图片库中，为每幅图片用细微的伪随机旋转效果时，可以使用`:neth-child(a)`选择符，且让`a`是质数。
* 如果要生成一个动画，而且让它看起来不是按照明显的规律在循环时，可以应用多个时长为质数的动画。


### 8. 连续的图像边框
> 背景知识：css渐变、border-image、条纹背景、基本的css动画
* `border-image`：指定作为元素周围边框的图像。基本原理就是九宫格伸缩法，把图片切割成九块，然后把它们应用到边框相应的边和角。结合实例继续说明


有时候我们想把一幅图案或图片应用为边框，而不是背景。一个元素有一圈装饰性的边框，基本上就是一张图片被裁剪进了边框所在的方环区域，不仅如此，还希望这个元素的尺寸在扩大或缩小的时候，这幅图片都可以自动延伸并覆盖完整的边框区域。

最简单的办法就是使用连个HTML元素,一个元素用来把图片设为背景，另一个元素存放内容

``` html
<div class="something">
	<div>I have a nice stone art border,
		don't I look pertty?
	</div>
</div>
```

```css
.something {
        background: url('http://csssecrets.io/images/stone-art.jpg');
        background-size: cover;
        padding: 1em;
  }

  .something > div {

	background: white;
	padding: 1em;
  }
  ```
  
  ![enter description here][36]
  
  
  如何改进，使用一个元素？
  思路：在背景图片之上，再叠加一层纯白的实色背景，为了让下层的图片背景透过边框区域显现出来，需要给两层背景指定不同的`background-clip`值，最后一个要点在于，我们只能在多重背景的最底层设置背景色，因此需要一道从白色过渡到白色的css渐变来模拟纯白实色背景的效果
  

``` css
padding: 1em;
border: 1em solid  transparent;
background: linear-gradient(white, white),
	   url('http://csssecrets.io/images/stone-art.jpg');
background-size: cover;
background-clip: padding-box, border-box;
```

![enter description here][37]

发现效果很接近，但是边框的图片有一种怪异的拼接效果。原因是`background-origin`的默认值是`paddin-box`，因此，图片的显示尺寸不仅取决于padding box的尺寸，而且被放置在了padding box的原点（左上角）。我们看到的实际上就是背景图片以平铺的方式蔓延到border box区域的效果。修改即可 

``` css
padding: 1em;
border: 1em solid  transparent;
background: linear-gradient(white, white),
	   url('http://csssecrets.io/images/stone-art.jpg');
background-size: cover;
background-clip: padding-box, border-box;
background-origin:border-box;
```

简写属性
```css
background: 
            linear-gradient(white, white) padding-box,
            url('http://csssecrets.io/images/stone-art.jpg') border-box 0 / cover;
```

![enter description here][38]

这个技巧还可以运用到渐图案上，比如制作一个老式信封

``` css
padding: 1em;
border: 1em solid transparent;
background: linear-gradient(white,white) padding-box,
	repeating-linear-gradient(-45deg, red 0, red 12.5%, transparent 0,
	  transparent 25%, #58a 0, #58a 37.5%, transparent 0, transparent 50%) 0 / 5em 5em;
```
![enter description here][39]

上面例子可以很容易的通过`background-size`属性来改变条纹的宽度，通过border属性来改变整个边框的厚度。还可以用`border-image`来实现。

``` css
padding: 1em;
border: 16px solid transparent;
border-image: 16 repeating-linear-gradient(-45deg, red 0, red 1em, transparent 0,
	  transparent 2em, #58a 0, #58a 3em, transparent 0, transparent 4em);
```

`border-image`也是一个复合属性，包含下面属性

* `border-image-source`：用在边框的图片的路径
* `border-image-slice`：图片边框向内偏移
* `border-image-width`：图片边框的宽度
* `border-image-outset`：边框图像区域超出边框的量
* `border-image-repeat`：图像边框是否应平铺(repeated)、铺满(rounded)或拉伸(stretched)

上面例子使用border-image实现，存在一些问题

* 每当我们改变`border-image-slice`时，都要同时修改`border-width`来让它们匹配
* 由于不能在`border-image-slice`属性中使用em单位，只能把边框厚度指定为像素单位
* 条纹的宽度需要在色标的位置信息中写好。


制作蚂蚁行军边框

将边框的宽度减少至1px，修改条纹颜色，便可以出现虚线边框，然后`background-size`改为某个合适的值。添加动画

``` css
@keyframes ants { to {background-position: 100%} }
```

```css
padding: 1em;
border: 1px solid transparent;
background: 
	linear-gradient(white, white) padding-box,
	repeating-linear-gradient(-45deg, black 0, black 25%, white 0, white 50%) 0 /.6em .6em;
animation: ants 12s linear infinite;
```
![enter description here][40]

`border-image`也有它强大之处，尤其是在搭配渐变图案时。比如我们需要一个顶部边框被裁切的效果，就像一般的脚注一样。我们所需要的就是在`border-image`属性再加上一条由渐变生成的垂直条纹，并把要裁剪的长度在渐变中写好，边框的粗细由`border-width`来控制。

``` css
border-top: .15em solid transparent;
border-image: 100% 0 0 linear-gradient(90deg, currentColor 4em, transparent 0);
padding-top: .5em;
```

![enter description here][41]

使用了`currentColor`属性，会根据color属性的变化而自动适应（假设我们希望这条边框跟文字保持相同颜色）

> correntColor:这是css3中一个颜色的关键字，这个关键字并没有绑定到一个固定的颜色，而是一直被解析为color。

假如我们想要所有水平割线自动与文本颜色保持一致。可以这么写

``` css
hr {
	height: .5em;
	background: currentColor;
}
```

很多已有的属性也具有类似的行为。如果你没有给边框指定颜色，它会自动从文本颜色那里得到颜色。









  
  
  
  
















































		















	


  


  [1]: ./images/color.jpg "色轮"
  [2]: ./images/1.png "1.png"
  [3]: ./images/2.png "2.png"
  [4]: ./images/02-1.png "02-1.png"
  [5]: ./images/02-2.png "02-2.png"
  [6]: ./images/03-1.png "03-1.png"
  [7]: ./images/04-1.png "04-1.png"
  [8]: ./images/04-2.png "04-2.png"
  [9]: ./images/04-3.png "04-3.png"
  [10]: ./images/04-4.png "04-4.png"
  [11]: ./images/05-1.png "05-1.png"
  [12]: ./images/05-2.png "05-2.png"
  [13]: ./images/05-3.png "05-3.png"
  [14]: ./images/05-4.png "05-4.png"
  [15]: ./images/05-5.png "05-5.png"
  [16]: ./images/05-6.png "05-6.png"
  [17]: ./images/05-7.png "05-7.png"
  [18]: ./images/05-8.png "05-8.png"
  [19]: ./images/05-9.png "05-9.png"
  [20]: ./images/05-10.png "05-10.png"
  [21]: ./images/06-1.png "06-1.png"
  [22]: ./images/06-2.png "06-2.png"
  [23]: ./images/06-3.png "06-3.png"
  [24]: ./images/06-4.png "06-4.png"
  [25]: ./images/06-5.png "06-5.png"
  [26]: ./images/06-11.png "棋盘"
  [27]: ./images/06-6.png "06-6.png"
  [28]: ./images/06-7.png "06-7.png"
  [29]: ./images/06-8.png "06-8.png"
  [30]: ./images/06-9.png "06-9.png"
  [31]: ./images/06-10.png "06-10.png"
  [32]: ./images/06-11.png "06-11.png"
  [33]: ./images/07-1.png "07-1.png"
  [34]: ./images/07-2.png "07-2.png"
  [35]: ./images/07-3.png "07-3.png"
  [36]: ./images/08-1.png "08-1.png"
  [37]: ./images/08-2.png "08-2.png"
  [38]: ./images/08-3.png "08-3.png"
  [39]: ./images/08-4.png "08-4.png"
  [40]: ./images/08-5.png "08-5.png"
  [41]: ./images/08-6.png "08-6.png"
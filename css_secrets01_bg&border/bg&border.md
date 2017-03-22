## 背景与边框
[TOC]
### 1.半透明边框
>  背景知识：`rgba/hsla`，在`rgb/hsl`的基础长增加了Alpha通道,用于设置颜色的不透明度（就是能透过多少背景）
*	`rgba`接受四个数值，即`rgba(red, green, blue,a)`,`rgb`可以为数值（0 ~ 255），也可以为百分比（0 ~ 100%），a表示不透明度(0~1),1为完全不透明，0表示完全透明。
	`rgba(0,255,0,1)` 表示纯绿色，完全不透明
	
	`rgba(100%,0,0，0.5)`表示纯红色，半透明
	
*  hsla(色相， 饱和度%， 亮度%，透明度)，格式为hsla(0, 0%,0%,0)
	hsla的第一个值表示色相，也就是一个实际的颜色，比如红色或者绿色。搜友的颜色绕色相环（色轮）一周，色相值以圆周上的度数表示。
	
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
  


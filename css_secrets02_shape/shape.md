# 形状

#### 1. 自适应的椭圆
> 背景知识：`border-radius`属性的基本用法

使用border-radius制作一个圆很简单，只要给任何正方形元素设置固定宽高及一半长度以上的border-radius，就可以得到圆形

``` css
background: #fb3;
width: 200px;
height: 200px;
border-radius: 100px;  /*>=正方形边长的一半*/
```

![enter description here][1]

#### 1.1 椭圆

在实际开发中可能更多的是让元素根据内容自动调增，而不是事先设置好宽高，因为很多时候内容是不定的。我们期望：如果宽高相等，就显示一个圆，不相等就显示一个椭圆。

上面代码实现不了，当宽度大于高度的时候，便会出现下面这种情况：

![enter description here][2]

解决方案：

`border-radius`可以单独指定水平和垂直半径，只要用一个(/)分隔这两个值就行。还一个特性是，它可以接受长度值，还可以接受百分比，两种特性结合，就可以自适应了。

``` css
border-radius: 50% / 50%;
```

![enter description here][3]

由于斜杠前后的两个值现在是一致的，可以简写

``` css
border-radius: 50%;
```

#### 1.2 半椭圆

border-radius是一个简写属性，包含四个展开式属性，这样一来可以通过设置四个角的不同半径来达到效果。

* `border-top-left-radius`
* `border-top-right-radius`
* `border-bottom-right-radius`
* `border-bottom-left-radius`

通过属性名字可以很清楚的知道他们用于设置哪个角。

不过还是推荐使用简洁写法，因为可以向它一次性提供用空格分开的多个值。

* 传四个值，这四个值就会被分别从左上角开始以顺时针顺序应用到各个角
* 传三个值，则意味着第四个值与第二个值相同
* 传两个值，意味着第三个值与第一个值相同，第四个值与第二个值相同。
* 传一个值，全部相同。

我们甚至可以为所有四个角提供完全不同的水平和垂直半径，方法就是在斜杠前指定1~4个值，斜杠后指定1~4个值。这两组值是单独展开为四个值。

比如`border-radius：10px / 5px 20px`, 
相当于10px 10px 10px 10px / 5px 20px 5px 20px;

两个例子：

``` css
border-radius:2em;
```

等同于

``` css
border-top-left-radius:2em;
border-top-right-radius:2em;
border-bottom-right-radius:2em;
border-bottom-left-radius:2em;
```

```css
border-radius: 2em 1em 4em / 0.5em 3em;
```

等同于

``` css
border-top-left-radius: 2em 0.5em;
border-top-right-radius: 1em 3em;
border-bottom-right-radius: 4em 0.5em;
border-bottom-left-radius: 1em 3em;
```

我们要实现的效果

![enter description here][4]


弄清了，border-radius的用法，开始分析上面半椭圆的实现

* 这个形状是垂直对称的，意味着左上角和右上角的半径值应该相同，右下角和左下角半径也是相同
* 顶部边缘没有平直的部分，意味着左上角和右上角半径之和应该等于整个形状的宽度，结合上一条，左半径和右半径在水平方向上是`50%`。
* 垂直方向，顶部的两个圆角占据了整个元素的高度，而且底部没有任何圆角，因此在垂直方向上值应该好似`100% 100% 0 0`。
* 因为底部两个角的垂直圆角是零，那么他们的水平圆角是多少就完全不重要了


``` css
border-radius: 50% / 100% 100% 0 0;
```

![enter description here][5]

沿纵轴劈开的半椭圆

``` css
border-radius: 100% 0 0 100% / 50%;
```

![enter description here][6]


#### 1.3 四分之一椭圆

创建一个四分之一椭圆，其中一个角水平和垂直半径值都需要100%，而其他三个角都不能设为圆角

``` css
border-radius: 100% 0 0 0;
```


![enter description here][7]



### 2. 平行四边形
> 背景知识：基本变形属性`transform`，属性很多，用到哪个讲哪个。

我们一般用skew属性来对矩形进行斜向拉伸，变成平行四边形，但是如果直接对元素使用的话，它里面的内容也会跟着斜向变动。

![enter description here][8]



#### 2.1 嵌套元素方案

对容器内容再应用一次反向的skewX()变形，从而抵消容器的变形效果

```html
<a href="#" class="button">
	<div>
		click me 
	</div>
</a>
```

css

``` css
.button {
	transform: skewX(-45deg);
}

.button > div {
	transform: skewX(45deg);
}
```

> `skewX()`	定义沿着 X 轴的 2D 倾斜转换。对应还有`skewY(angle)，skew(x-angle,y-angle)`

#### 2.2 伪元素方法
上面方法需要添加额外的HTML元素。这里提供一个不用添加额外的HTML元素的。

这种方法的思路是把所有的样式(背景、边框等)应用到伪元素上，然后对伪元素进行变形。


``` css
 .button {
		position: relative;
		
		/* 其他的文字颜色、内外变局等样式*/
	}

.button::before {
	content: '';
	position: absolute;
	top: 0;right: 0;bottom: 0;left: 0;

	z-index: -1;

	background: #58a;
	transform: skew(-45deg);
}
```


![enter description here][9]


要注意几点，一是要给宿主元素设置`position: relative`，并给伪类元素设置`position: absolute`，且偏移量都为0，以便让它在水平和垂直方向上都被拉伸至宿主元素的尺寸。二是伪元素生成的方块是重叠在内容之上的，一旦设置背景，就会遮住内容，应该设置`z-index: -1`或者更小的数，宿主没有设置`z-index`默认为0；

### 3. 菱形图片

> 背景知识：css变形、“平行四边形”

#### 3.1 基于变形的方案

思路：需要把图片用一个<div>包起来，然后对其应用相反的rotate()变形样式。


```html
<div class="picture">
  <img src="dog.jpg">
</div>
```

样式

``` css
.picture {
	width: 200px;
	height: 200px;
	transform: rotate(45deg);
	overflow: hidden;
}

.picture img {
	max-width: 100%;
	transform: rotate(-45deg);
}
```

并没有发生我们想象的那样，得到一个八角形，还挺好看的，哈啊。

![enter description here][10]

> `totate()`定义 2D 旋转，在参数中规定角度。

为了观察清楚，为容器添加边框

![enter description here][11]


出现这样的情况主要是`max-width：100%`，`100%`会被解析为容器(.picture)的边长，但是，我们想要图片的宽度与容器的对角线相等。通过计算可以很快计算出正方形的对角线的长度为`根号2` 乘以正方形的边长。因为`根号2`约等于`1.414`，那么可以设置max-width为`1.141 * 100%`，向上取整为142%。

但是更推荐使用scale属性把图片放大，更合理。

* 我们希望图片的尺寸保留100%这个值，这样当浏览器不支持变形样式时仍然可以得到一个合理的布局
* 通过scale变形样式来缩放图片，是以它的中心点进行缩放的。通过width属来放大图片时，只会以它的左上角为原点进行缩放，从容迫使我们动用额外的负边距来调整图片。

``` css
.picture {
	width: 200px;
	height: 200px;
	margin: 100px;
	transform: rotate(45deg);
	overflow: hidden;
}

.picture img {
	max-width: 100%;
	transform: rotate(-45deg) scale(1.42);
}
```

![enter description here][12]





  [1]: ./images/01-1.png "01-1.png"
  [2]: ./images/01-2.png "01-2.png"
  [3]: ./images/01-3.png "01-3.png"
  [4]: ./images/01-4.png "半椭圆"
  [5]: ./images/01-4.png "01-4.png"
  [6]: ./images/01-5.png "01-5.png"
  [7]: ./images/01-6.png "01-6.png"
  [8]: ./images/02-1.png "02-1.png"
  [9]: ./images/02-3.png "02-3.png"
  [10]: ./images/03-1.png "03-1.png"
  [11]: ./images/03-2.png "03-2.png"
  [12]: ./images/03-3.png "03-3.png"
## 背景与边框
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







	


  [1]: ./images/color.jpg "色轮"
  [2]: ./images/1.png "1.png"
  [3]: ./images/2.png "2.png"
  [4]: ./images/02-1.png "02-1.png"
  [5]: ./images/02-2.png "02-2.png"
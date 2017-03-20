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






	


  [1]: ./images/color.jpg "色轮"
  [2]: ./images/1.png "1.png"
  [3]: ./images/2.png "2.png"
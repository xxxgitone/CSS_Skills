# 用户体验

### 1.选用合适的鼠标光标
合适的光标可以让用户知道当前可以执行什么操作，光标值有很多

![enter description here][1]

也可以隐藏光标

``` css
cursor:none;
```


### 2. 扩大可点击区域
使用场景，可点击按钮，还有就是如果想让某个元素在鼠标接近窗口某侧时自动划入。

#### 2.1 border的解决方案
给按钮添加一个透明的边框，使用`background-clip: padding-box`防止背景延伸到边框下面，使用`box-shadow`模拟边框。

``` css
border: 10px solid transparent;
background-clip: padding-box;
box-shadow: 0 0 0 1px  rgba(0, 0, 0, .3) inset;
```
![enter description here][2]

#### 2.2 伪元素方法

如果想给按钮添加一个外部投影，使用上面的方法是行不通的，因为投影会加在边框外。

``` css
box-shadow: 0 0 0 1px  rgba(0, 0, 0, .3) inset,
                0 .1em .2em -.05em rgba(0, 0, 0, .5);
```

![enter description here][3]

> 很神奇，貌似这样也挺好看的。

在按钮的上层覆盖一层透明的伪元素，并让伪元素在四个方向上都比宿主元素大出`10px`；

``` css
button::before {
	content: '';
	position: absolute;
	top: -10px;right: -10px;
	bottom: -10px;left: -10px;
}
```

### 3. 自定义复选框
使用:checked伪类，这个伪类只会在单选框或复选框被选中的时候才会被触发。通过给`lable`元素添加样式模拟出复选框，顶替默认的复选框。

``` css
input[type="checkbox"] + label::before {
	content: '\a0'; /*不换行符*/
	display: inline-block;
	vertical-align: .1em;
	width: .8em;
	height: .8em;
	margin-right: .2em;
	border-radius: .2em;
	background: silver;
	text-indent: .15em;
	line-height: .65;
} 

input[type="checkbox"]:checked + label::before { /*选中后样式*/
	content: '\2713';
	background: yellowgreen;
}

input[type="checkbox"] {/*隐藏默认复选框*/
	position: absolute;
	clip: rect(0,0,0,0);
}
```

这里隐藏原有复选框，没哟直接使用`display:noen`，因为那样会把它从键盘tab键切换焦点的队列中删除。

![enter description here][4]

还可以编写它们聚焦或者禁用时的样式

``` css
input[type="checkbox"]:focus + label::before {
	box-shadow: 0 0 .1em .1em #58a;
}

input[type="checkbox"]:disabled + label::before {
	background: gray;
	box-shadow: none;
	color: #555;
}
```

![enter description here][5]

还可以添加过渡和动画让状态呢切换平滑，还可以制作拟物化的开关。

### 4.通过阴影来弱化背景
也就是制作遮罩层，传统做法是新增一个HTML元素，作遮罩元素。

在body上添加一个伪类

``` css
body.dimmed::before {
	content: '';
	position: fixed;
	top: 0;right: 0;
	bottom: 0;left: 0;
	z-index: -1;
	background: rgba(0, 0, 0, .8);
}
```

在`body`上再增加一个类，防止其他需求已经占用了body元素的`::before`伪类，在`body`上添加伪类，而不直接在那个元素自己身上使用伪类的好处在于，伪元素无法绑定独立的javascript事件处理函数，绑定在body上，便于交互。

### 5.通过模糊来弱化背景
使用`filter`属性，这里需要考虑在那个元素上使用`filter`，在body上使用的话，整个页面都会被模糊处理，包括我们想要显示的元素，所以需要新增一个HTML元素用于包裹需要模糊处理的元素

```html
<dialog>O HAI, I’m a dialog. Click on me to dismiss.</dialog>
<main>
	<button>Show dialog</button>
	<!--其他内容-->
</main>
```


``` css
main {
  transition: .6s;
}

main.de-emphasized {
	filter: blur(3px);
}

dialog {
  position: fixed;
  top: 50%;left: 50%;
  z-index: 1;
  width: 10em;
  padding: 2em;
  margin: -5em;
  border: 1px solid silver;
  border-radius: .5em;
  box-shadow: 0 .2em .5em rgba(0, 0, 0, .5),
	0 0 0 100vmax rgba(0,0,0,.2);  /*第二层阴影是设置模糊背景与blur共存效果更佳*/
}
```

### 6. 滚动提示
当容器还有更多内容时，一层淡淡的阴影会出现在容器的顶部和/或底部。

``` css
ul {
	display: inline-block;
	overflow: auto;
	width: 7.2em;
	height: 7em;
	border: 1px solid silver;
	padding: .3em .5em;
	list-style: none;
	margin-top: 2em;
	font: 100 200%/1.6 'Frutiger LT Std', sans-serif;
	margin-top: 30px;

	background: radial-gradient(at top, rgba(0, 0, 0, .2), transparent 70%) no-repeat;
	background-size: 100% 15px;
}
```
在顶部设置了径向渐变，用于生成阴影

![enter description here][6]

但是这条阴影会一直停留在顶部，哪怕顶部没有更多东西了。

使用`background-attachment`来解决

该属性决定背景是在视口中固定的还是随包含它的区块滚动的

* fixed
此关键字表示背景相对于视口固定。即使一个元素拥有滚动机制，背景也不会随着元素的内容滚动。

* local
此关键字表示背景相对于元素的内容固定。如果一个元素拥有滚动机制，背景将会随着元素的内容滚动， 并且背景的绘制区域和定位区域是相对于可滚动的区域而不是包含他们的边框。

* scroll
此关键字表示背景相对于元素本身固定， 而不是随着它的内容滚动（对元素边框是有效的）

同时我们得设置两层背景：一层用来生成那条阴影，另一层用于遮盖阴影。生成阴影的背景层将具有默认的`scroll`值，因为希望它保持在原位，遮罩层设置为`local`，这样滚动到最顶部的时候就会盖住阴影，向下滚动的时候会跟着滚动，从而露出阴影。

``` css
background: linear-gradient(white, white),
	radial-gradient(at top, rgba(0, 0, 0, .2), transparent 70%);
background-repeat: no-repeat;
background-size: 100% 15px;
background-attachment: local, scroll;
```

![enter description here][7]

增加下部阴影

``` css
background: linear-gradient(white 15px, hsla(0,0%,100%,0)) 0 0 / 100% 50px,
	radial-gradient(at top, rgba(0,0,0,.2), transparent 70%) 0 0 / 100% 15px,
	linear-gradient(to top, white 15px, hsla(0,0%,100%,0)) bottom / 100% 50px,
	radial-gradient(at bottom, rgba(0,0,0,.2), transparent 70%) bottom / 100% 15px;
background-repeat: no-repeat;
background-attachment: local, scroll, local, scroll;
```





  [1]: ./images/01-1.png "01-1.png"
  [2]: ./images/02-1.png "02-1.png"
  [3]: ./images/02-2.png "02-2.png"
  [4]: ./images/03-1.png "03-1.png"
  [5]: ./images/03-2.png "03-2.png"
  [6]: ./images/06-1.png "06-1.png"
  [7]: ./images/06-2.png "06-2.png"
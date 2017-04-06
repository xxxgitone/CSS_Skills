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





  [1]: ./images/01-1.png "01-1.png"
  [2]: ./images/02-1.png "02-1.png"
  [3]: ./images/02-2.png "02-2.png"
  [4]: ./images/03-1.png "03-1.png"
  [5]: ./images/03-2.png "03-2.png"
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

由于斜杠前后的两个值现在是一致的，可以简写

``` css
border-radius: 50%;
```

#### 1.2 半椭圆






  [1]: ./images/01-1.png "01-1.png"
  [2]: ./images/01-2.png "01-2.png"
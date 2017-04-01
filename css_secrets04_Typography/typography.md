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




  [1]: ./images/01-1.png "01-1.png"
  [2]: ./images/01-2.png "01-2.png"
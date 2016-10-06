Sass学习指南
Sass是一个CSS预处理器。在Sass中，你可以使用变量，条件语句，循环，函数，继承等其他编程语言所有的特性。只要一条命令，就能把Sass文件编译成浏览器能识别的CSS文件。

## 1. 安装和编译
#### 1.1 安装
安装Sass需要先安装Ruby，这里假定你已经安装了Ruby。

在命令行输入以下命令（Windows系统需要先安装Linux shell）

```
sudo gem install sass
```
#### 1.2 编译
假定你已经在当前目录创建了一个Sass文件，名叫test.scss：

```
$color:red;
body{
  color:$color;
}
```

你只要进入该目录，执行以下命令：

```
sass test.scss:test.css
```

便可在当前目录生成一个test.css：

```
body {
  color: red;}
```

在编译 Sass 时，你可以开启“watch”功能，这样只要你的代码进行任保修改，Sass都能自动监测到代码的变化，并且给你直接编译出来：

```
sass --watch test.scss:test.css
```

你可以指定生成的CSS文件风格：

```
sass test.scss:test.css --style expanded
```
这样生成的CSS文件会是展开的：

```
body {
  color: red;
}
```
一共有四种样式风格：

- 嵌套输出方式 nested （默认）
- 展开输出方式 expanded  
- 紧凑输出方式 compact 
- 压缩输出方式 compressed （会删除注释）

最后，推荐一款GUI编译工具：Koala。这是一个国人写的开源软件。

## 2. 基本特性
#### 2.1 变量
Sass中的变量使用$开头：

```
$font-size:12px;
$color:green !default;
div{
  color:$color;
  font-size:$font-size;
}
```
!default代表默认值。

你在选择器或者mixin、函数中还可以定义局部变量。

```
$color:red;
div{
  $color:blue;
  color:$color;
}
```

#### 2.2 嵌套
在Sass中，选择器可以嵌套：

```
$color:red;
div{
  color:$color;
  
  a{
    text-decoration:none;
  }
}
```

编译的CSS是：

```
div {
  color: red;
}
div a {
  text-decoration: none;
}

```
在CSS中，有很多同类性质的属性，比如font-size、font-weight,font-family等,使用Sass可以让属性嵌套：

```
$color:red;
div{
  color:$color;
  
  font:{
    size:12px;
    weight:bold;
  }
}
```

对应的CSS：

```
div {
  color: red;
  font-size: 12px;
  font-weight: bold;
}

```
在嵌套的代码中，可以使用&引用父类选择器：

```
a{
    &:hover{
      color:green;
    }
}
```
对应的CSS：

```
a:hover {
  color: green;
}
```

#### 2.3 运算
###### 2.3.1 加法

```
.box {
  width: 20px + 10px;
}
```

编译出来的 CSS:

```
.box {
  width: 30px;
}
```
不同单位进行加法或加法可能会报错。

###### 2.3.2 减法

```
.box {
  width: 20px - 10px;
}
```

编译出来的 CSS:

```
.box {
  width: 10px;
}
```

由于 “-” 号在CSS中可以作为属性标识符的分隔符，所以在有变量参与的减法运算中，最好在 “-” 两边加上空格。
###### 2.3.3 乘法

```
.box {
  width: 10px * 2;
}
```

编译出来的 CSS:

```
.box {
  width: 20px;
}
```
Sass 的乘法运算和加法、减法运算一样，在运算中有不同类型的单位时，也将会报错。

###### 2.3.4 除法

由于“/”符号在 CSS 中被使用，所以作为CSS的超集，在 Sass 中做除法，直接使用“/”符号将不会生效，也不会报错。

要解决这个问题，只需要添加一个小括号( )即可：

```
.box {
  width: (100px / 2);  
}
```

编译出来的 CSS 如下：

```
.box {
  width: 50px;
}
```

”/”符号被当作除法有以下几种情况：

- 如果数值或它的任意部分是存储在一个变量中或是函数的返回值。
- 如果数值被圆括号包围。
- 如果数值是另一个数学表达式的一部分。

如下所示：

```
//SCSS
p {
  font: 10px/8px;             // 纯 CSS，不是除法运算
  $width: 1000px;
  width: $width/2;            // 使用了变量，是除法运算
  width: round(1.5)/2;        // 使用了函数，是除法运算
  height: (500px/2);          // 使用了圆括号，是除法运算
  margin-left: 5px + 8px/2px; // 使用了加（+）号，是除法运算
}
```

编译出来的CSS

```
p {
  font: 10px/8px;
  width: 500px;
  height: 250px;
  margin-left: 9px;
 }
```

除法运算时，如果两个值带有相同的单位值时，除法运算之后会得到一个不带单位的数值。如下所示：

```
.box {
  width: (1000px / 100px);
}
```

编译出来的CSS如下：

```
.box {
  width: 10;
}
```

###### 2.3.5 颜色运算

Sass中的颜色运算会把颜色中的红，绿，蓝三种颜色的值分别进行运算：

```
div{
  color:#112233 + #445566;
}
``` 
其中，对于红：11 + 22 = 33，绿：22 + 55 = 77，蓝：33 + 66 = 99。

编译得到的CSS：

```
div {
  color: #557799;
}
```

###### 2.3.6 字符串运算

```
div{
  &:before{
    content:"Hello" + " " +"world";
  }
}
```
CSS为：

```
div:before {
  content: "Hello world";
}
```

#### 2.4 注释
Sass中有两种注释风格：

- /\*多行注释\*/ 这种注释，在编译以后仍然会保留，除非使用compressed风格
- // 单行注释 这种注释，在编译之后不会存在
- /\*!保留注释\*/ 即使使用compressed风格，仍然存在，通常用于文件首部版权信息。

#### 2.5 插值
使用#{ }可以把一个变量的值动态的插入到其他地方：

```
$side:top;
.conatiner{
  margin-#{$side}:10px;
}
```
得到CSS：

```
.conatiner {
  margin-top: 10px;
}
```

#### 2.6 @import

Sass 扩展了 CSS 的 @import 规则，让它能够引入SCSS文件。 

```
@import "foo.scss";
```

如果你有一个 SCSS 或 Sass 文件需要引入， 但是你又不希望它被编译为一个 CSS 文件， 这时，你就可以在文件名前面加一个下划线，就能避免被编译。 

例如，你有一个文件叫做 _colors.scss。 这样就不会生成 _colors.css 文件了， 而且你还可以这样做：

```
@import "colors";  //不用加下划线
```
来引入 _colors.scss 文件。

注意，在同一个目录不能同时存在带下划线和不带下划线的同名文件。 例如， _colors.scss 不能与 colors.scss 并存。

#### 2.7 @media
Sass 中的 @media 指令和 CSS 的使用规则一样的简单，但它有另外一个功能，可以嵌套在 CSS 规则中。

```
.sidebar {
  width: 300px;
  @media screen and (orientation: landscape) {
    width: 500px;
  }
}
```
CSS:

```
.sidebar {
  width: 300px; 
}
@media screen and (orientation: landscape) {
    .sidebar {
        width: 500px; 
    } 
}
```

#### 2.7 @at-root
当你选择器嵌套多层之后，想让某个选择器跳出，此时就可以使用 @at-root。

```
.a {
  color: red;

  .b {
    color: orange;

    .c {
      color: yellow;

      @at-root .d {
        color: green;
      }
    }
  }  
}
```
编译出来的CSS

```
.a {
  color: red;
}

.a .b {
  color: orange;
}
```


## 3. 代码重用

#### 3.1 混合宏
混合宏相当于C语言中的宏定义一样，你定义了一段代码，接着在另一个地方调用，Sass会在调用的地方原封不动的插入那段代码。当然这样也有不好的地方，那就是编译之后的CSS代码会出现冗余。

Sass使用@mixin定义混合宏，使用@include来调用混合宏。

```
@mixin border-radius{
    -webkit-border-radius: 5px;
    border-radius: 5px;
}

.box1{
  @include border-radius;
}
.box2{
  @include border-radius;
}
```
CSS为：

```
.box1 {
  -webkit-border-radius: 5px;
  border-radius: 5px;
}

.box2 {
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
```

在混合宏中，可以传一个不带任何值的参数，比如：

```
@mixin border-radius($radius){
  -webkit-border-radius: $radius;
  border-radius: $radius;
}
```

在调用的时候可以给这个混合宏传一个参数值：

```
.box {
  @include border-radius(3px);
}
```

还可以设置默认值：

```
@mixin border-radius($radius:5px){
  -webkit-border-radius: $radius;
  border-radius: $radius;
}
```
调用的时候如果未传值，将使用默认值。

有一个特别的参数“…”。当混合宏传的参数过多之时，可以使用参数来替代：

```
@mixin box-shadow($shadows...){
  @if length($shadows) >= 1 {
    -webkit-box-shadow: $shadows;
    box-shadow: $shadows;
  } @else {
    $shadows: 0 0 2px rgba(#000,.25);
    -webkit-box-shadow: $shadow;
    box-shadow: $shadow;
  }
}
```

#### 3.2 继承
###### 3.2.1 继承其他选择器

在Sass中，可以通过@extend 继承一个已有的样式。

```
.box{
  font-size:12px;
}

.box1{
  @extend .box;
  color:red;
}
```

得到的CSS：

```
.box, .box1 {
  font-size: 12px;
}

.box1 {
  color: red;
}
```

###### 3.2.2 继承一个占位符

上面的例子中，被继承的.box选择器最终也出现在了CSS文件中。这往往不是你需要的，更多的时候，你不希望被继承的样式也出现在CSS中。

你可以使用%定义一个占位符：

```
%box{
  font-size:12px;
}

.box1{
  @extend %box;
  color:red;
}

.box2{
  @extend %box;
  color:blue;
}
```
CSS:

```
.box1, .box2 {
  font-size: 12px;
}

.box1 {
  color: red;
}

.box2 {
  color: blue;
}
```

#### 3.1 函数

```
@function greeting($person){
  @return "Hello " + $person;
}

li{
  &:before{
    content:greeting("张三");
  }
}
```
CSS：

```
@charset "UTF-8";
li:before {
  content: "Hello 张三";
}
```

## 4. 控制命令

#### 4.1 条件语句
@if可以用来判断：

```
$line-height:3;
div{
  line-height:$line-height;
  @if $line-height > 5{
    font-size:12px;
  }@else{
    font-size:15px;
  }
}
```
CSS为：

```
div {
  line-height: 3;
  font-size: 15px;
}
```
#### 4.2 循环
###### 4.2.1 @for循环

```
@for $i from 1 to 3{
  .item-#{$i}{
    width:100px * $i;
  }
}
```
CSS为：

```
.item-1 {
  width: 100px;
}

.item-2 {
  width: 200px;
}
```

Sass 的 @for 循环中有两种方式：

```
@for $i from <start> through <end>
@for $i from <start> to <end>
```
这两个的区别是关键字 through 表示包括 end 这个数，而 to 则不包括 end 这个数。


###### 4.2.2 @while循环

```
$i:1;
@while $i < 3{
  .item-#{$i}{
    width:100px * $i;
  }
  $i:$i + 1; 
}
```
CSS为：

```
.item-1 {
  width: 100px;
}

.item-2 {
  width: 200px;
}
```
###### 4.2.3 @each循环
@each 循环就是去遍历一个列表，然后从列表中取出对应的值。
@each 循环指令的形式：

```
@each $var in <list>
```
例子：

```
$list: primary info error warning;
@each $item in $list{
  .item-#{$item}{
    font-size:12px;
  }
}
```
CSS为：

```
.item-primary {
  font-size: 12px;
}

.item-info {
  font-size: 12px;
}

.item-error {
  font-size: 12px;
}

.item-warning {
  font-size: 12px;
}
```

## 5. 常用函数
#### 5.1 字符串函数
- unquote($string)：删除字符串中的引号；
- quote($string)：给字符串添加引号。

#### 5.2 数字函数
- percentage()：将一个不带单位的数字转换成百分比形式
- round()：将一个数四舍五入为一个最接近的整数
- ceil()：将一个数向上取整
- floor()：讲一个数向下取整
- abs()：返回一个数的绝对值

#### 5.1 列表函数 
- length(\$list)：返回一个列表中有几个值
- nth(\$list,\$n)：指定列表中某个位置的值
- unitless()：判断一个值是否带有单位，不带单位返回true，带单位false

#### 5.1 颜色函数

lighten() 和 darken() 两个函数都是围绕颜色的亮度值做调整的，其中 lighten() 函数会让颜色变得更亮

```
//SCSS
.lighten {
    background: lighten($baseColor,10%);
}
.darken{
    background: darken($baseColor,10%);
}
```


# Compass学习指南

Compass是Sass的一个工具库，他们之间的关系就好像JavaScript和jQuery一样。

学习Compass之前，你最好有一定的Sass基础。就好像你不会JavaScript一样可以使用jQuery一样，懂得Sass会让你更好的理解Compass。

## 1. 安装和使用
#### 1.1 安装
跟Sass一样，Compass是基于Ruby的。所以首先，你得安装Ruby。

这里我们假定你已经安装了Ruby，接着，在命令行输入：

```
sudo gem install compass
```

compass就安装好了。

#### 1.2 创建项目

创建项目很简单，假定你要在当前目录创建一个项目为Test：

```
compass create Test
```
上面这个命令会在当前目录下创建一个新目录Test，里面会有一个config.rb，这是一个配置文件，还有两个子目录：sass和stylesheets。你在sass目录中编写sass文件，生成的css文件会出现在stylesheets中。

#### 1.3 编译
编译命令非常简单：

```
compass compile
```
如果你想实时编译，可以使用：

```
compass watch
```

#### 1.4 Compass的模块机制
Compass是Sass的库。它封装了一系列Sass函数、mixin共开发者使用。这些Sass函数和mixin，Compass使用模块来将他们分类。

Compass中一共有七个模块：

1. Reset
2. Layout
3. CSS3
4. Browser Support
5. Typography
6. Utilities
7. Helpers


需要导入某个模块，只需要使用@import指令就可以了。比如导入reset模块：

```
@import "compass/reset";
```

本篇文章也是围绕这七个模块来介绍的。

## 2. 模块

值得一提的是，大多数情况下，你只要使用：

```
@import "compass";
```
就行了，这样会默认导入所有的模块，**除了** Reset和Layout这两个模块。这两个模块需要手动导入：

```
@import "compass/reset";
@import "compass/layout";
```

通常我们需要哪个模块才会导入哪个模块。

打开sass目录下的screen.scss。

#### 2.1 Reset模块

Reset模块用于重置浏览器的默认样式。Compass中的reset模块使用的是meyer reset重置，它会把所有的浏览器默认样式归零。

```
@import "compass/reset";
```
编译得到的就是[reset.css](http://meyerweb.com/eric/tools/css/reset/reset.css);

如果你更喜欢normalize的重置思想，可以看看[Compass中导入Normalize](http://www.cnblogs.com/qiaoer2/p/compass-normalize.html)。


#### 2.2 Layout模块
Layout模块使用率较低，这里只介绍两个子模块。

###### 2.2.1 Stretching子模块
Layout模块中的Stretching子模块，主要用来拉伸元素的：

```
@import "compass/layout/stretching";
```
Stretching子模块中有一个名为stretch的mixin：

```
.box{
    @include stretch();
}
```
生成的CSS为：

```
.box {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
}
```
你也可以单独拉伸水平方向或垂直方向，分别对应stretch-x和stretch-y。

###### 2.2.1 Sticky Footer子模块
Sticky Footer子模块用来生成一个总是在页面最底部的页脚，即使页面本身长度不到浏览器窗口的高。

使用这个子模块需要配合特定的HTML样式：

```
<body>
  <div id="root">
    <div id="root_footer"></div>
  </div>
  <div id="footer">
    Footer content goes here.
  </div>
</body>
```

然后在scss文件中：

```
@include sticky-footer(54px);
```
sticky-footer可以传入一个参数用于指定footer的高。

你也可以自己定义HTML的选择器：

```
@include sticky-footer(54px, "#my-root", "#my-root-footer", "#my-footer")
```


这个模块用的很少。

#### 2.3 CSS3模块
CSS3模块用的最多。它主要解决了CSS3新增属性在不同浏览器中的实现，比如，自动添加浏览器厂商前缀，适配IE。

CSS3模块很多，这里只说background-size，border-radius，opacity，inline-block。

###### 2.3.1 background-size

```
@import "compass/css3";
.box{
	@include background-size(cover);
}
```
生成CSS：

```
.box {
  -moz-background-size: cover;
  -o-background-size: cover;
  -webkit-background-size: cover;
  background-size: cover;
}
```

###### 2.3.2 border-radius

```
@import "compass/css3";
.box{
	@include border-radius(5px);
}
```
生成CSS：

```
.box {
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
```

###### 2.3.3 opacity

```
@import "compass/css3";
.box{
	@include opacity(0.5);
}
```
生成CSS：

```
.box {
  filter: progid:DXImageTransform.Microsoft.Alpha(Opacity=50);
  opacity: 0.5;
}
```

###### 2.3.4 inline-block

```
@import "compass/css3";
.box{
	@include inline-block;
}
```
生成CSS：

```
.box {
  display: inline-block;
  vertical-align: middle;
  *vertical-align: auto;
  *zoom: 1;
  *display: inline;
}

```

#### 2.4 Browser Support模块

这个模块是用来指定支持的浏览器及版本的。所以这个模块设定的结果将影响其他模块。比如修改支持的浏览器：

```
$supported-browsers: chrome;

@import "compass/css3";
.box{
	@include border-radius(4px, 4px);
}
```
这样生成的CSS中厂商前缀将只有chrome：

```
.box {
  -webkit-border-radius: 4px 4px;
  border-radius: 4px / 4px;
}
```

支持多个浏览器可以这样：

```
$supported-browsers: chrome firefox;
```

对于浏览器版本：

```
$browser-minimum-versions: ("chrome": null, "firefox": null, "ie": null, "safari": null, "opera": null)
```

比如：

```
$browser-minimum-versions: ("ie":"8");
```


#### 2.5 Typography模块
Typography模块提供字体排版样式。

分为Links，Lists，Text，Vertical Rhythm四个子模块。

这里只介绍前三个。

###### 2.5.1 Links

主要有三个mixin：hover-link，link-colors，unstyled-link。

- hover-link，用于更改不同状态下超链接是否有下划线：

```
@import "compass";
a{
	@include hover-link();
}
```
生成CSS：

```
a {
  text-decoration: none;
}
a:hover, a:focus {
  text-decoration: underline;
}
```

- link-colors，主要用于更改不同状态下超链接的颜色：

```
link-colors($normal, $hover, $active, $visited, $focus)
```
例子：

```
@import "compass";

a{
	@include link-colors(red,blue,gray,purple,green);
}
```
生成CSS：

```
a {
  color: red;
}
a:visited {
  color: purple;
}
a:focus {
  color: green;
}
a:hover {
  color: blue;
}
a:active {
  color: gray;
}
```

- unstyled-link，主要用于抹除超链接默认样式 ：

```
@import "compass";

a{
	@include unstyled-link();
}
```

生成CSS：

```
a {
  color: inherit;
  text-decoration: inherit;
  cursor: inherit;
}
a:active, a:focus {
  outline: none;
}
```

###### 2.5.2 Lists

这里介绍no-bullets，inline-block-list。

- no-bullets：

```
@import "compass";

ul{
	@include no-bullets();
}
```
生成CSS：

```
ul {
  list-style: none;
}
ul li {
  list-style-image: none;
  list-style-type: none;
  margin-left: 0;
}
```
- inline-block-list：

```
@import "compass";

ul{
	@include inline-block-list();
}
```
生成CSS：

```
ul {
  margin: 0;
  padding: 0;
  border: 0;
  overflow: hidden;
  *zoom: 1;
}

ul li {
  list-style-image: none;
  list-style-type: none;
  margin-left: 0;
  display: inline-block;
  vertical-align: middle;
  *vertical-align: auto;
  *zoom: 1;
  *display: inline;
  white-space: nowrap;
}
```

###### 2.5.1 Text
Text只介绍一个ellipsis：

```
@import "compass";

p{
	@include ellipsis();
}
```

生成CSS：

```
p {
  white-space: nowrap;
  overflow: hidden;
  -ms-text-overflow: ellipsis;
  -o-text-overflow: ellipsis;
  text-overflow: ellipsis;
}
```

#### 2.6 Utilities模块
utilities模块和Helpers模块都用来提供一些不属于其他模块的功能。不同的是，Utilities模块提供的主要是mixin，而Helpers提供的是函数。

对于表格，Utilities提供了outer-table-borders(\$width, \$color)，inner-table-borders(\$width, \$color)，table-scaffolding。

使用的比较多的是table-scaffolding：

```
@import "compass";

table{
	@include table-scaffolding;
}
```
生成CSS：

```
table th {
  text-align: center;
  font-weight: bold;
}
table td,
table th {
  padding: 2px;
}

table td.numeric,
table th.numeric {
  text-align: right;
}
```

还比如清除浮动：

```
@import "compass";

.clearfix{
	@include clearfix;
}
```
生成CSS：

```
.clearfix {
  overflow: hidden;
  *zoom: 1;
}
```

但这种清除浮动有个弊端：无法让子元素通过margin负值悬挂在容器之外的效果，因为一旦超出容器就被hidden了。
Compass考虑了这种特殊的需求，提供了一个pie-clearfix：

```
@import "compass";

.clearfix{
	@include pie-clearfix;
}
```
生成CSS：

```
.clearfix {
  *zoom: 1;
}

.clearfix:after {
  content: "";
  display: table;
  clear: both;
}
```
但display: table;可能低版本的IE不支持，Compass提供了另一个：

```
@import "compass";

.clearfix{
	@include legacy-pie-clearfix;
}
```

生成CSS：

```
.clearfix {
  *zoom: 1;
}

.clearfix:after {
  content: "\0020";
  display: block;
  height: 0;
  clear: both;
  overflow: hidden;
  visibility: hidden;
}
```

对于浮动：

```
@import "compass";

.box1{
	@include float(left);
}
```

生成CSS：

```
.box1 {
  float: left;
}
```
float()这个mixin还会根据$browser-minimum-versions判断要不要兼容IE6（IE6在float元素显示时有bug）。

###### 2.6.1 精灵合图

这里把精灵合图挑出来讲。Compass让我们可以更简单的使用精灵图。

我们在images目录下新建一个logo目录，用来存放精灵图（只能是png格式的）：

```
@import "logo/*.png";

@include all-logo-sprites();
```

然后Compass会自动生成一张合图，再根据每张图片的名字生成对应的class，自动计算每张图的位置。

如果不想使用自动生成的类名，你也可以这样：

```
.error{
    @include logo-sprite("sprite1");//传入图片名
}
```
这样.error类就可以使用sprite1这张精灵图了。

有时候我们要为一个button设定不同状态下的背景，比如active和focus状态下使用不同的图片，那么我们只需要给精灵图这样命名：

```
sprite1_active.png
sprite1_focus.png
```
Compass自动生成不同状态下背景的相应样式。

#### 2.7 Helpers模块

Helpers提供一些函数。
比如image-url()。

如果我们直接写图片的URL，可能会出错。假设我们在项目目录下有一个存放图片的images目录：

```
@import "compass";

p{
	background:url('test.png');
}
```
生成的CSS：

```
p {
  background: url("test.png");
}
```
这样会找不到路径。

在config.rb文件中有一个配置项：

```
images_dir = "images"
```
这项配置指定了图片的目录，接着使用image-url()就可以找到config.rb中images_dir指定的图片目录下的正确地址。

```
@import "compass";

p{
	background:image-url('test.png');
}
```
生成的CSS：

```
p {
  background: url('/images/test.png?1475397292');
}
```
这样就正确了。

但这样生成的URL是绝对地址，Compass默认把config.rb所在目录看成是根目录。我们可以修改config.rb来让Compass使用相对地址：

```
relative_assets = true
```
这样生成的URL为：

```
p {
  background: url('../images/test.png?1475397292');
}
```

与image-url()相类似的还有font-url()和stylesheet-url()，用法相同。

Helpers中还有一个CSS3中经常用到的font-files()，用于返回font的url和format信息，通常配合font-face的mixin使用：

```
@include font-face("Blooming Grove", font-files("examples/bgrove.ttf", "examples/bgrove.otf"));
 
.example {
  font-family: "Blooming Grove";
  font-size: 1.5em;
}
```
生成的CSS：

```
@font-face {
  font-family: "Blooming Grove";
  src: url('/fonts/examples/bgrove.ttf?1408149819') format('truetype'), url('/fonts/examples/bgrove.otf?1408149819') format('opentype');
}
.example {
  font-family: "Blooming Grove";
  font-size: 1.5em;
}
```
font-files()返回的URL地址跟image-url()一样，需要在config.rb中配置：

```
fonts_dir = "fonts"
```






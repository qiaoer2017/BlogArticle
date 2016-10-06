# Compass中导入Normalize
Compass中默认的浏览器重置模块式reset。它会把所有的浏览器默认样式都归零。有时候这不是你所需要的，你可能更想把所有浏览器的默认样式都变得相同，而不是完全抹除，[normalize](https://necolas.github.io/normalize.css/)就是这样做的。

在Compass中使用normalize而不是使用其默认的reset，有两种做法：

- 直接引入normalize.css，导入到项目中
- 使用Compass的插件机制安装Normalize

## 1. 直接引入normalize.css
将下载好的normalize.css文件保存在compass项目里的sass目录下，并将后缀.css改为.scss。

在screen.scss中引入：

```
@import "normalize";
```

## 2. 使用Compass的插件机制

#### 2.1 安装normalize

```
gem install compass-normalize
```
#### 2.2 修改配置文件config.rb
在第一行之后添加require 'compass-normalize'：

```
require 'compass/import-once/activate'
# Require any additional compass plugins here.

require 'compass-normalize'

# Set this to the root of your project when deployed:
```

这里展开一下，config.rb第一行：

```
require 'compass/import-once/activate'
```
是干嘛的？

这行语句告诉Compass，如果文件中引入了相同的文件，只包含一次。你可以自己做实验，在同一个文件中两次引入reset：

```
@import "compass/reset";

//...

@import "compass/reset";
```

打开生成的css文件发现只有一次引入reset的结果。这就是require 'compass/import-once/activate'的作用。如果你确实需要引入两次reset，可以这样：

```
@import "compass/reset";

//...

@import "compass/reset!";
```

#### 2.3 引入normalize

```
@import "normalize";
```

这里主要是借normalize来介绍Compass中的插件机制。引入其他插件也是同理。







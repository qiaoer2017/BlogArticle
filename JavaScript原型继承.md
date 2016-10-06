> ——这篇博文很长，因为JavaScript的原型继承不是一句两句可以说清楚的。你做好心理准备吧，可以随时放弃。

JavaScript在所有面向对象的语言中是最独特的，它的独特源于它怪异的原型继承机制。


## 一个JS学习者的抱怨
1994年，Brendan Eich在设计JavaScript的那10天里，鬼知道他是怎么想的，恐怕连他也觉得自己在设计一门“玩具语言”，所以不想把它变得这么复杂，于是他大胆的抛弃了C++和Java中类的概念，自创了一个叫原型继承的东西。这绝对是一个设计上的错误。这反而使得JavaScript的继承变得更加复杂，许多写了很多年JS的程序员几乎从来没有使用过JS的原生继承，因为没几个人能真正明白。但到了后来，想改变这个错误已经很难了，直到2015年，ES6才加入了class关键字，但好像已经来得太晚了。

为了不让你疯掉，我们还是先来看看ES6中新增的class关键字吧。

<!-- more -->

## ES6新增的class关键字
class关键字使得我们可以像Java一样定义一个类，然后让其他类继承它。

我们先定义一个父类：

```
class Parent{
	constructor(name){
		this.name = name;
	}
	
	sayHello(){
		return "Hello, I'm " + this.name;
	}
}
```

定义一个类使用class关键字，后面跟类名。在类体中，需要定义一个constructor函数（只能叫这个名字）作为构造函数，里面传入你需要的参数,这样的好处是你可以在实例化对象的时候初始化属性：

```
var p = new Parent("张三");
console.log(p.name); //张三
p.sayHello(); //Hello, I'm 张三
```

需要注意的是，我们在class类体中定义函数的时候，不需要加上function关键字。

接着，我们定义一个子类Child继承Parent：

```
class Child extends Parent{
	constructor(name, age){
		super(name);
		this.age = age;
	}
	
	play(){
		return "快跟我一起玩吧";
	}
}
```

继承使用extends关键字，这跟Java一样。在Child类中，我们重写了constructor方法，并使用super关键字调用父类的constructor方法来初始化从父类继承来的属性name。age是Child独有的属性，所以单独初始化。在Child中，我们还定义了一个play()方法。

```
var c = new Child("小明", 10);
console.log(c.name); //小明
console.log(c.age); // 10
c.sayHello(); //Hello, I'm 小明
c.play(); //快跟我一起玩吧
```

是不是很简单！只要你学过其他面向对象的语言，你一定会爱上这种方式。

但遗憾的是，现在大多数浏览器都不支持ES6，特别是早期的IE。不过，你可以通过Babel这类工具将ES6编译成ES5。

## 说说原型继承
说完了令人羡慕的class，还是让我们清醒一下，回头看看令人生畏的原型继承吧。

我们知道，JS中一切皆对象。Brendan Eich在设计继承机制的时候想的也很简单，让一个对象中的某个指针（这个指针叫做原型）指向另一个对象，这样被指向的那个对象中的所有属性和方法不就可以被当前对象使用了吗，也就实现了继承：

```
var parent = {
	name:'张三',
	sayHello: function(){
		return 'Hello, my name is ' + this.name;
	}
};

var child = {
	name:'小明'
};

child.__proto__ = parent;
```

这样就实现了继承。我们可以看到，child中有一个\_\_proto\_\_属性（任何对象都有一个\_\_proto\_\_属性，即使是一个空对象var obj = { };），将它指向parent这个对象就可以了。parent就称为child的**原型对象**。

记住**原型对象**这个名词，它就相当于Java中的父类，一个对象可以获取它的原型对象中的属性和方法。

> 注：\_\_proto\_\_ 是一个不应该在你代码中出现的非正规的用法，这里仅仅用它来解释JavaScript原型继承的工作原理。

这样，在你调用child.name;的时候，JS引擎会首先查找child本身有没有这个属性，发现有，则直接使用child本身的属性，所以返回“小明”。在调用child.sayHello()的时候，JS引擎也是首先查找child本身有没有这个方法，发现没有，就会通过\_\_proto\_\_上溯到parent，发现parent中有，则使用parent中的sayHello方法。

关于\_\_proto\_\_，再举一个例子可能更明白一点：

```
var point = {
	x: 0,
	y: 0,
	print: function(){console.log(this.x, this.y);}
};

var p = {
	x: 1,
	y: 1,
	__proto__:point 
};
```

> 每个对象默认都带有一个__proto__属性，指向这个对象的原型对象。这样，原型对象里的属性和方法就可以被当前对象所使用，就好像使用自己的属性和方法一样。

以下代码展示了JS引擎如何查找属性：

```
function getProperty(obj, prop) {
	if (obj.hasOwnProperty(prop)){
		return obj[prop];
	}else if (obj.__proto__ !== null){
		return getProperty(obj.__proto__, prop);
	}else{
		return undefined;
	}
}
```

牢记这种继承的方法，后面将要说到的内容是基于这一简单的实现的。

## 构造函数
上面实现继承的方式虽然简单，但是实际开发中很少使用。特别是\_\_proto\_\_，低版本的IE是不支持的。所以上面啰嗦那么一大堆，都是废话。但要是不说这些废话，下面的这些内容就很难理解。

每个教授JavaScript原型继承的书籍里面都会给出这样一段代码：

```
function Person(name, age){
	this.name = name;
	this.age = age;
	this.desc = function(){
		console.log("I'm " + this.name + ", I'm " + this.age + ".");
	}
}

var p = new Person("张三", 18);
p.desc();// I'm 张三, I'm 18.
```

是不是头晕了？其实Person()就是一个普通函数，只不过在这里我们叫它构造函数。

为什么？

你知道的，JavaScript中没有类，那要想创建一系列相同类型的对象怎么办？比如要创建两个人，每个人都有name，age属性，难道每创建一个人都要从头用对象字面量的方式重写一遍？就像这样：

```
var p1 = {name:'张三',age:18};
var p2 = {name:'李四',age:20};
```

这也太麻烦了！这样简单的对象还好办，要是复杂一点那还不要命！虽然你可以使用\_\_proto\_\_属性指定p2的原型指向p1，但终究也不是办法。

于是Brendan Eich就借鉴了Java和C++中创建对象的方式：构造函数。

他引入了一个新的关键字：new。并规定，new后面的函数就是构造函数。所以上面那个例子，new Person(“张三”,18)就是调用了构造函数。构造函数返回一个对象，就是你要新创建的那个对象（如果不使用new关键字Person就是一个普通函数，不会返回对象，除非你手动return来返回一个对象）。Person()中的this关键字就代表那个你将要创建的对象，默认，JavaScript引擎会在Person函数末尾会自动添加return this这段代码。

我们来看一下构造函数创建的对象中有什么：

```
var p = new Person("张三", 18);
console.log(p);
```

控制台显示：

```
Person {
	name: "张三",
	age: 18,
	desc: function(),
	__proto__: Object
}
```

通过这个构造函数，你成功的得到了一个对象。但实际上，这还是有点瑕疵的。如果我们创建两个person对象，他们的名字和年龄属性不相同，这是很合理的，因为每个人的名字和年龄可能都不同。但是如果你比较一下desc这个函数，发现这两个人的desc函数也是不相同的，这就不合理了。因为在JavaScript中，函数也是对象，也要内存开销，但两个对象其实只需要共享一个函数对象就可以了。

```
var p1 = new Person("张三", 18);
var p2 = new Person("李四", 19);
console.log(p1.desc === p2.desc); //false
```

那有没有办法能让desc被所有的person实例对象共享呢？

有！办法就是把desc移入原型对象中。这就要引入另一个属性了：prototype。

JavaScript的构造函数（JavaScript中函数也是一个对象）有一个属性叫做prototype。他专门用来生成通过这个构造函数生成的对象的原型对象。

有点绕，我们来屡一下。我们刚才说了，通过构造函数可以生成一个对象：

```
Person {
	name: "张三",
	age: 18,
	desc: function(),
	__proto__: Object
}
```

我们前面又说了，每个对象都有一个\_\_proto\_\_属性指向他的原型对象。默认我们生成的对象，它的\_\_proto\_\_指向Object.prototype（Object也是一个构造函数，它也有一个prototype属性，有点晕吗？那就先别管它）这个原型对象。

还记得我们前面让你记住**原型对象**这个名词吗？我们说原型对象就相当于父类。那我们只要给我们通过构造函数生成的所有的对象生成一个父类不就行了。

```
function Person(name, age){
	this.name = name;
	this.age = age;
}

Person.prototype.desc = function(){
		console.log("I'm " + this.name + ", I'm " + this.age + ".");
};
```

这段代码还是把你吓住了吧。不急，我们慢慢看。

你只要知道，Person.prototype是Person的一个属性，并且是一个对象。

我们前面有这样一段代码不知道你还记不记得：

```
child.__proto__ = parent;
```

这里，parent是child的原型对象。那么：
- var p = new Person(“张三”, 18) 生成的对象p就相当于这里的child。
- Person.prototype就相当于这里的parent。

我们可以把所有需要被实例对象共享的属性和方法放在Person.prototype中，这样生成的对象中就可以直接共享这些属性和方法了。我们将desc方法移入到Person.prototype中，这样所有person对象的desc都是相同的：

```
var p1 = new Person("张三", 18);
var p2 = new Person("李四", 19);
console.log(p1.desc === p2.desc); //true
```

你可以继续往Person.prototype中添加你需要被共享的东西：

```
Person.prototype.nationality = '中国';
Person.prototype.eat = function(){
	console.log("用筷子吃饭");
}
```

这是JavaScript中最常用的创建对象的方式。

如果你还不能理解，那我们就把构造函数的方式改写一下，改成我们熟悉的方式：

```
Person.prototype = {
	nationality: '中国',
	desc: function(){
		console.log("I'm " + this.name + ", I'm " + this.age + ".");
	}
	eat: function(){
		console.log("用筷子吃饭");
	}
}

var p1 = {
	name: '张三',
	age: 18,
	__proto__: Person.prototype
}
```

是不是恍然大悟！

使用构造函数的好处就是我们可以传参，更加简便。不过也就是因为更加简便，所以让很多人不明白\_\_proto\_\_和prototype的区别。

简言之：\_\_proto\_\_就是对象的一个属性，指向它的原型对象prototype，原型对象就是当前对象的爸爸！

还有一点忘了说，prototype中还有一个属性叫constructor，它指向构造函数本身。

```
console.log(Person.prototype.constructor);// Person
```

而且我们也说过，原型对象中的属性可以直接被实例对象所使用，所以：

```
console.log(p1.constructor); // Person
console.log(p1.constructor === Person.prototype.constructor); //true
```

此外，JavaScript还有一些方法用来检验一个对象和另一个对象之间是不是有父子关系：
#### 1.isPrototypeOf()
这个方法用来判断一个对象是不是另一个对象的原型对象（也就是检测一个对象是不是另一个对象的爸爸）。

```
console.log(Person.prototype.isPrototyeOf(p1)); // true
console.log(p2.isPrototypeOf(p1)); // false
```

#### 2.hasOwnProperty()
这个方法用来判断某个属性是本地属性，还是继承自原型对象的属性：

```
console.log(p1.hasOwnProperty('name')); // true
console.log(p1.hasOwnProperty('nationality')); //false
```
 
 
基本上，这就是你需要知道的全部了。

## 构造函数的继承
#### 1.prototype模式
设想现在有一个构造函数表示学生，Student：

```
function Student(name, age){
	this.name = name;
	this.age = age;
}
Student.prototype.goToSchool = function(){
	console.log("我去上学校，天天不迟到。");
}
```

还有一个构造函数表示小学生，Pupils：

```
function Pupils(grade){
	this.grade = grade;
	
}
Puils.prototype.writeHomework = function(){
	console.log("写作业");
}
```

如果我们需要Pupils继承Student，该怎么做？
直到ES6之前，JavaScript都没有直接的办法实现两个构造函数之间的继承。ES6中引入class和extends完美的解决了这个问题。但是ES6之前呢？怎么办？

实际上，我们必须手动来完成这一过程。我们只要将Pupils的prototype指向Student不就行了！具体得这么做：

```
Puils.prototype = new Student();
Pupils.prototype.constructor = Pupils;
Pupils.prototype.writeHomework = function () {
	console.log("写作业");
}
```

我们一句一句的来分析：

```
Pupils.prototype = new Student();
```

这段代码相当于舍弃了Puils构造函数原本的prototype，而将其指向一个新生成的new Student()对象。
注意这里new Student()并没有传参，所以生成的student对象的name和age属性均为undefined。所以以后，所有通过new Pupils()生成的pupils对象，它们的原型对象(也就是他们的爸爸)都是这个生成的new Student()对象。
我们再深入的想想，这个生成的new Student()，它本身也有原型对象啊（它本身也有爸爸啊），那么Pupils.prototype能不能访问到Student.prototype里的东西呢？

当然能！一个对象的原型对象中的属性和方法默认都是可以被当前对象访问的，就好像是自己的一样（你用你爸爸的钱就好像用你自己的一样）。

我们再来看第二行代码：

```
Pupils.prototype.constructor = Pupils;
```

这段代码是用来更改pupils对象的构造函数的。new Student()生成的对象中有一个constructor属性指向Student，所以Pupils.prototype = new Student();使得pupils的constructor也指向了Student，这是不合理的，会使得原型链变得混乱，所以显式更改其指向。

然后第三行代码是给Pupils的原型对象再添加新的方法。

注意，如果把第三行代码放在第一行：

```
Pupils.prototype.writeHomework = function () {
	console.log("写作业");
}
Puils.prototype = new Student();
Pupils.prototype.constructor = Pupils;
```

那么writeHomework这个方法实际上是没有被添加上的，因为此时Pupils.prototype仍然是Pupils构造函数默认生成的原型对象。

但如果我们只想继承一个构造函数中prototype里的东西怎么办？

#### 2.利用空对象
我们可以使用一个空对象，把Student.prototype存起来：

```
function Pupils(grade) {
	this.grade = grade;
}

var F = function () {};
F.prototype = Student.prototype;
Pupils.prototype = new F();
Pupils.prototype.constructor = Pupils;
Pupils.prototype.writeHomework = function () {
	console.log("写作业");
};
```

由于F是空的，所以不存在本地属性，通过Pupils.prototype = new F(); 得到的全都是Student.prototype中的属性和方法。


## 结论
终于快要结束了，拿出一张非常经典的图：

![JavaScript原型继承图示](http://upload-images.jianshu.io/upload_images/1767852-d0f569664b7e634d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面是转载知乎上面的一个对这幅图的解答：
作者：[doris](https://www.zhihu.com/people/doris-53-22)
链接：[原文地址](https://www.zhihu.com/question/34183746/answer/58155878)

---

首先，要明确几个点：
1.在JS里，万物皆对象。方法（Function）是对象，方法的原型(Function.prototype)是对象。因此，它们都会具有对象共有的特点。
即：对象具有属性__proto__，可称为隐式原型，一个对象的隐式原型指向构造该对象的构造函数的原型，这也保证了实例能够访问在构造函数原型中定义的属性和方法。

2.方法(Function)
方法这个特殊的对象，除了和其他对象一样有上述_proto_属性之外，还有自己特有的属性——原型属性（prototype），这个属性是一个指针，指向一个对象，这个对象的用途就是包含所有实例共享的属性和方法（我们把这个对象叫做原型对象）。原型对象也有一个属性，叫做constructor，这个属性包含了一个指针，指回原构造函数。

好啦，知道了这两个基本点，我们来看看上面这副图。
1.构造函数Foo()
构造函数的原型属性Foo.prototype指向了原型对象，在原型对象里有共有的方法，所有构造函数声明的实例（这里是f1，f2）都可以共享这个方法。

2.原型对象Foo.prototype
Foo.prototype保存着实例共享的方法，有一个指针constructor指回构造函数。

3.实例
f1和f2是Foo这个对象的两个实例，这两个对象也有属性__proto__，指向构造函数的原型对象，这样子就可以像上面1所说的访问原型对象的所有方法啦。

另外：
构造函数Foo()除了是方法，也是对象啊，它也有__proto__属性，指向谁呢？
指向它的构造函数的原型对象呗。函数的构造函数不就是Function嘛，因此这里的__proto__指向了Function.prototype。
其实除了Foo()，Function(), Object()也是一样的道理。

原型对象也是对象啊，它的__proto__属性，又指向谁呢？
同理，指向它的构造函数的原型对象呗。这里是Object.prototype.

最后，Object.prototype的__proto__属性指向null。


总结：
1.对象有属性__proto__,指向该对象的构造函数的原型对象。
2.方法除了有属性__proto__,还有属性prototype，prototype指向该方法的原型对象。

---

## 更多的内容
你可能听过有一个人叫阮一峰，他的博客写的很好。这里链接几篇他关于原型继承的博文，写的浅显易懂：
[Javascript继承机制的设计思想](http://www.ruanyifeng.com/blog/2011/06/designing_ideas_of_inheritance_mechanism_in_javascript.html)
[Javascript 面向对象编程（一）：封装](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_encapsulation.html)
[Javascript面向对象编程（二）：构造函数的继承](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance.html)
[Javascript面向对象编程（三）：非构造函数的继承](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance_continued.html)



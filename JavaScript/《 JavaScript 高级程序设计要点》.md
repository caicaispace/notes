# javascript 高级程序设计要点

[TOC]

##### typeof 、instanceof
　　`typeof`返回一个表达式的数据类型的字符串，返回结果为js基本的数据类型，包括`number,boolean,string,object,undefined,function`.语法为`typeof(data)` 或` typeof data`，`instanceof`则为判断一个对象是否为某一数据类型，或一个变量是否为一个对象的实例;返回`boolean`类型语法为` o instanceof A`
```
console.log("typeof(1): "+typeof(1));
console.log("typeof(NaN): "+typeof(NaN));
console.log("typeof(Number.MIN_VALUE): "+typeof(Number.MIN_VALUE));
console.log("typeof(Infinity): "+typeof(Infinity));
console.log("typeof(\"123\"): "+typeof("123"));
console.log("typeof(true): "+typeof(true));
console.log("typeof(window): "+typeof(window));
console.log("typeof(Array()): "+typeof(new Array()));
console.log("typeof(function(){}): "+typeof(function(){}));
console.log("typeof(document): "+typeof(document));
console.log("typeof(null): "+typeof(null));
console.log("typeof(eval): "+typeof(eval));
console.log("typeof(Date): "+typeof(Date));
console.log("typeof(sss): "+typeof(sss));
console.log("typeof(undefined): "+typeof(undefined));
 

console.log("typeof(1):" + typeof(1));//number
console.log("typeof(\"abc\"):" + typeof("abc"));//string
console.log("typeof(true):" +typeof(true));//boolean
console.log("typeof(2009-2-4):" + typeof(2009-2-4));//number
console.log("typeof(\"2009-2-4\"):" + typeof("2009-2-4"));//string
console.log("typeof(m):" + typeof(m));//undefined
var d = new Date();
console.log("typeof(d):" + typeof(d));//object
function Person(){};
console.log("typeof(Person):" + typeof(Person));//function
var a = new Array();
console.log("typeof(a):" + typeof(a));//object
console.log("a instanceof Array:" + (a instanceof Array));
var h = new Person();
var o = {};
console.log("h instanceof Person:" + (h instanceof Person));//true
console.log("h instanceof Object:" + (h instanceof Object));//true
console.log("o instanceof Object:" + (o instanceof Object));//true
console.log(typeof(h));//object
```
## 第 5 章 引用类型
### Function 类型
#### 5.5.4 函数内部属性

　　在函数内部，有两个特殊的对象： `arguments` 和 `this`。其中， `arguments` 在第 3 章曾经介绍过，它是一个类数组对象，包含着传入函数中的所有参数。虽然 `arguments` 的主要用途是保存函数参数，但这个对象还有一个名叫 `callee` 的属性，该属性是一个指针，指向拥有这个 `arguments` 对象的函数。请看下面这个非常经典的阶乘函数。
```js
function factorial(num){
    if (num <=1) {
        return 1;
    } else {
        return num * factorial(num-1);
    }
}
```
　　定义阶乘函数一般都要用到递归算法；如上面的代码所示，在函数有名字，而且名字以后也不会变的情况下，这样定义没有问题。但问题是这个函数的执行与函数名 `factorial` 紧紧耦合在了一起。为了消除这种紧密耦合的现象，可以像下面这样使用 `arguments.callee`。
```js
function factorial(num){
    if (num <=1) {
        return 1;
    } else {
        return num * arguments.callee(num-1);
    }
}
```
　　在这个重写后的  `factorial()`函数的函数体内，没有再引用函数名 `factorial`。这样，无论引用函数时使用的是什么名字，都可以保证正常完成递归调用

##### apply()、 call()
```js
window.color = "red";
var o = { color: "blue" };

function sayColor(){
    console.log(this);
    console.log(this.color);
}

sayColor(); //red
sayColor.call(this); //red
sayColor.call(window); //red
sayColor.call(o); //blue
```

##### bind()
```js
window.color = "red";
var o = { color: "blue" };

function sayColor(){
    console.log(this); // { color: "blue" }
    console.log(this.color);
}
var objectSayColor = sayColor.bind(o);
objectSayColor(); //blue
```
#### 5.6 基本包装类型

>要注意的是，使用 new 调用基本包装类型的构造函数，与直接调用同名的转型函数是不一样的。
- 例如：
```js
var value = "25";
var number = Number(value); //转型函数
alert(typeof number); //"number"
var obj = new Number(value); //构造函数
alert(typeof obj); //"object"
```

#### 5.7 单体内置对象

##### 5.7.1 Global对象
###### 1. URI 编码方法

　　Global 对象的 encodeURI()和 encodeURIComponent()方法可以对 URI（Uniform ResourceIdentifiers，通用资源标识符）进行编码，以便发送给浏览器。有效的 URI 中不能包含某些字符，例如空格。而这两个 URI 编码方法就可以对 URI 进行编码，它们用特殊的 UTF-8 编码替换所有无效的字符，从而让浏览器能够接受和理解。

　　其中， encodeURI()主要用于整个 URI（例如， http://www.wrox.com/illegalvalue.htm），而 encodeURIComponent()主要用于对 URI 中的某一段（例如前面 URI 中的 illegal value.htm）进行编码。它们的主要区别在于， encodeURI()不会对本身属于 URI 的特殊字符进行编码，例如冒号、正斜杠、问号和井字号；而 encodeURIComponent()则会对它发现的任何非标准字符进行编码。来看下面的例子。
```js
var uri = "http://www.wrox.com/illegal value.htm#start";

//"http://www.wrox.com/illegal%20value.htm#start"
console.log(encodeURI(uri));

//"http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.htm%23start"
console.log(encodeURIComponent(uri));
```
　　使用 encodeURI()编码后的结果是除了空格之外的其他字符都原封不动，只有空格被替换成了%20。而 encodeURIComponent()方法则会使用对应的编码替换所有非字母数字字符。这也正是可以对整个 URI 使用 encodeURI()，而只能对附加在现有 URI 后面的字符串使用 encodeURIComponent()的原因所在。

>  一 般来说 ， 我 们使 用 encodeURIComponent() 方法的时候要比使 用encodeURI()更多，因为在实践中更常见的是对查询字符串参数而不是对基础 URI进行编码。

　　与 encodeURI()和 encodeURIComponent()方法对应的两个方法分别是 decodeURI()和decodeURIComponent()。其中， decodeURI()只能对使用 encodeURI()替换的字符进行解码。例如，它可将%20 替换成一个空格，但不会对%23 作任何处理，因为%23 表示井字号（#），而井字号不是使用encodeURI()替换的。同样地， decodeURIComponent()能够解码使用 encodeURIComponent()编码的所有字符，即它可以解码任何特殊字符的编码。来看下面的例子：
```js
var uri = "http%3A%2F%2Fwww.wrox.com%2Fillegal%20value.htm%23start";

//http%3A%2F%2Fwww.wrox.com%2Fillegal value.htm%23start
 console.log(decodeURI(uri));
 
//http://www.wrox.com/illegal value.htm#start
 console.log(decodeURIComponent(uri));
```
　　这里，变量 uri 包含着一个由 encodeURIComponent()编码的字符串。在第一次调用 decodeURI()
输出的结果中，只有%20 被替换成了空格。而在第二次调用 decodeURIComponent()输出的结果中，
所有特殊字符的编码都被替换成了原来的字符，得到了一个未经转义的字符串（但这个字符串并不是一
个有效的 URI）。

>URI 方法 encodeURI()、 encodeURIComponent()、 decodeURI()和 decodeURIComponent()用于替代已经被 ECMA-262第 3版废弃的 escape()和 unescape()方法。 URI 方法能够编码所有 Unicode 字符，而原来的方法只能正确地编码 ASCII 字符。因此在开发实践中，特别是在产品级的代码中，一定要使用 URI 方法，不要使用 escape()和 unescape()方法。

## 第 6 章 面向对象的程序设计

#### 6.1.1 属性类型

ECMA-262 第 5 版在定义只有内部才用的特性（attribute）时，描述了属性（property）的各种特征。
ECMA-262 定义这些特性是为了实现 JavaScript 引擎用的，因此在 JavaScript 中不能直接访问它们。为了
表示特性是内部值，该规范把它们放在了两对儿方括号中，例如[[Enumerable]]。尽管 ECMA-262
第 3 版的定义有些不同，但本书只参考第 5 版的描述。
ECMAScript 中有两种属性：数据属性和访问器属性。
##### 1. 数据属性
　　数据属性包含一个数据值的位置。在这个位置可以读取和写入值。数据属性有 4 个描述其行为的特性。

　　` [[Configurable]]`：表示能否通过 `delete` 删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。像前面例子中那样直接在对象上定义的属性，它们的这个特性默认值为 `true`。

　　` [[Enumerable]]`：表示能否通过 `for-in` 循环返回属性。像前面例子中那样直接在对象上定义的属性，它们的这个特性默认值为 `true`。

　　 `[[Writable]]`：表示能否修改属性的值。像前面例子中那样直接在对象上定义的属性，它们的这个特性默认值为 `true`。

　　` [[Value]]`：包含这个属性的数据值。读取属性值的时候，从这个位置读；写入属性值的时候，把新值保存在这个位置。这个特性的默认值为 `undefined`。对于像前面例子中那样直接在对象上定义的属性，它们的`[[Configurable]]、 [[Enumerable]]`和`[[Writable]]`特性都被设置为`true`，而`[[Value]]`特性被设置为指定的值。例如：
```js
var person = {
	name: "Nicholas"
};
```
　这里创建了一个名为 name 的属性，为它指定的值是`"Nicholas"`。也就是说， `[[Value]]`特性将
被设置为`"Nicholas"`，而对这个值的任何修改都将反映在这个位置。

　　要修改属性默认的特性，必须使用 ECMAScript 5 的 `Object.defineProperty()`方法。这个方法
接收三个参数：属性所在的对象、属性的名字和一个描述符对象。其中，描述符（descriptor）对象的属
性必须是： `configurable、 enumerable、 writable` 和` value`。设置其中的一或多个值，可以修改
对应的特性值。例如：
```js
var person = {};
Object.defineProperty(person, "name", {
	writable: false,
	value: "Nicholas"
});
alert(person.name); //"Nicholas"
person.name = "Greg";
alert(person.name); //"Nicholas"
```

　　这个例子创建了一个名为 `name` 的属性，它的值`"Nicholas"`是只读的。这个属性的值是不可修改
的，如果尝试为它指定新值，则在非严格模式下，赋值操作将被忽略；在严格模式下，赋值操作将会导
致抛出错误。

　　类似的规则也适用于不可配置的属性。例如：
```js
var person = {};
Object.defineProperty(person, "name", {
	configurable: false,
	value: "Nicholas"
});
alert(person.name); //"Nicholas"
delete person.name;
alert(person.name); //"Nicholas"
```

　　把 `configurable` 设置为 `false`，表示不能从对象中删除属性。如果对这个属性调用 `delete`，则在非严格模式下什么也不会发生，而在严格模式下会导致错误。而且，一旦把属性定义为不可配置的，就不能再把它变回可配置了。此时，再调用 `Object.defineProperty()`方法修改除 `writable` 之外的特性，都会导致错误：
```js
var person = {};
Object.defineProperty(person, "name", {
	configurable: false,
	value: "Nicholas"
});
//抛出错误
Object.defineProperty(person, "name", {
	configurable: true,
	value: "Nicholas"
});
```

　　也就是说，可以多次调用 `Object.defineProperty()`方法修改同一个属性，但在把 `configurable`特性设置为 `false` 之后就会有限制了。

　　在调用 `Object.defineProperty()`方法时，如果不指定，` configurable、 enumerable` 和`writable` 特性的默认值都是 `false`。多数情况下，可能都没有必要利用 `Object.defineProperty()`方法提供的这些高级功能。不过，理解这些概念对理解 JavaScript 对象却非常有用。

>IE8 是第一个实现 `Object.defineProperty()`方法的浏览器版本。然而，这个版本的实现存在诸多限制：只能在 DOM 对象上使用这个方法，而且只能创建访问器属性。由于实现不彻底，建议读者不要在 IE8 中使用 `Object.defineProperty()`方法。


#### 6.3.3 组合继承

　　组合继承（combination inheritance），有时候也叫做伪经典继承，指的是将原型链和借用构造函数的技术组合到一块，从而发挥二者之长的一种继承模式。其背后的思路是使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。这样，既通过在原型上定义方法实现了函数复用，又能够保证每个实例都有它自己的属性。下面来看一个例子。
```js
function SuperType(name) {
    this.name = name;
    this.colors = ["red", "blue", "green"];
}

SuperType.prototype.sayName = function() {
    console.log(this.name);
};


function SubType(name, age) {
    //继承属性
    SuperType.call(this, name);
    this.age = age;
}
console.log(SubType.prototype.constructor);
//继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
// console.log(SubType.prototype.constructor);

SubType.prototype.sayAge = function() {
    console.log(this.age);
};

var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
console.log(instance1.colors); //"red,blue,green,black"
instance1.sayName(); //"Nicholas";
instance1.sayAge(); //29
var instance2 = new SubType("Greg", 27);
console.log(instance2.colors); //"red,blue,green"
instance2.sayName(); //"Greg";
instance2.sayAge(); //27
```
#### 6.3.4 原型式继承
```js
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}

var person = {
    name: "Nicholas",
    friends: ["Shelby", "Court", "Van"]
};
var anotherPerson = object(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");
var yetAnotherPerson = object(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");
console.log(person.friends); //"Shelby,Court,Van,Rob,Barbie"


var person = {
    name: "Nicholas",
    friends: ["Shelby", "Court", "Van"]
};
var anotherPerson = Object.create(person);
anotherPerson.name = "Greg";
anotherPerson.friends.push("Rob");
var yetAnotherPerson = Object.create(person);
yetAnotherPerson.name = "Linda";
yetAnotherPerson.friends.push("Barbie");
console.log(person.friends); //"Shelby,Court,Van,Rob,Barbie"
```
#### 6.3.5 寄生式继承

```js
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
}

function createAnother(original) {
    var clone = object(original); //通过调用函数创建一个新对象
    clone.sayHi = function() { //以某种方式来增强这个对象
        console.log("hi");
    };
    return clone; //返回这个对象
}

var person = {
    name: "Nicholas",
    friends: ["Shelby", "Court", "Van"]
};
var anotherPerson = createAnother(person);
anotherPerson.sayHi(); //"hi"
```

#### 6.3.6 寄生组合式继承
```js
function object(o) {
    function F() {}
    F.prototype = o;
    return new F();
};

function SuperType(name) {
    this.name   = name;
    this.colors = ["red", "blue", "green"];
};

SuperType.prototype.sayName = function() {
    console.log(this.name);
};

function SubType(name, age) {
    SuperType.call(this, name);
    this.age = age;
};

function inheritPrototype(subType, superType) {
    var prototype         = object(superType.prototype); //创建对象
    prototype.constructor = subType; //增强对象
    subType.prototype     = prototype; //指定对象
};

inheritPrototype(SubType, SuperType);
SubType.prototype.sayAge = function() {
    console.log(this.age);
};
```

## 第 7 章 函数表达式
### 7.1 递归

```js
function factorial(num){
	if (num <= 1){
		return 1;
	} else {
		return num * arguments.callee(num-1);
	}
}
```
```js
var factorial = (function f(num){
	if (num <= 1){
		return 1;
	} else {
		return num * f(num-1);
	}
});
```
### 7.2 闭包
>**闭包** 是指有权访问另一个函数作用域中的变量的函数

![Alt text](./1436980198985.png)


#### 7.2.1闭包与变量
```js
function createFunctions() {
    var result = new Array();
    for (var i = 0; i < 10; i++) {
        result[i] = function() {
            console.log(i);
            return i;
        };
    }
    return result;
}

var funs = createFunctions();
for (var i = 0; i < funs.length; i++) {
    console.log(funs[i]());
};
// 10
// 10
// 10
// 10
// 10
// 10
// 10
// 10
// 10
// 10
```
　　 这个函数会返回一个函数数组。表面上看，似乎每个函数都应该返自己的索引值，即位置 0 的函数返回 0，位置 1 的函数返回 1，以此类推。但实际上，每个函数都返回 10。因为每个函数的作用域链中都 保 存 着 createFunctions() 函 数 的 活 动 对 象 ， 所 以 它 们 "`引 用`" 的 都 是 同 一 个 变 量 i 。 当createFunctions()函数返回后，变量 i 的值是 10，此时每个函数都引用着保存变量 i 的同一个变量对象，所以在每个函数内部 i 的值都是 10。但是，我们可以通过创建另一个匿名函数强制让闭包的行为符合预期，如下所示。
```js
function createFunctions() {
    var result = new Array();
    for (var i = 0; i < 10; i++) {
        result[i] = function(num) {
            return function() {
                return num;
            };
        }(i);
    }
    return result;
}

var funs = createFunctions();
for (var i = 0; i < funs.length; i++) {
    console.log(funs[i]());
};
// 0
// 1
// 2
// 3
// 4
// 5
// 6
// 7
// 8
// 9
```
#### 7.2.2 关于this对象
　　匿名函数的执行环境具有全局性，因此其 this 对象通常指向 window（然，在通过 call()或 apply()改变函数执行环境的情况下， this 就会指向其他对象。）

```js
var name = "The Window";
var object = {
    name: "My Object",
    getNameFunc: function() {
        console.log(this.name); // My Object
        return function() {
            return this.name;
        };
    }
};
console.log(object.getNameFunc()()); //"The Window"（在非严格模式下）
```

　　以上代码先创建了一个全局变量 name，又创建了一个包含 name 属性的对象。这个对象还包含一个方法——getNameFunc()，它返回一个匿名函数，而匿名函数又返回 this.name。由于 getNameFunc()返回一个函数，因此调用 object.getNameFunc()()就会立即调用它返回的函数，结果就是返回一个字符串。然而，这个例子返回的字符串是"The Window"，即全局 name 变量的值。为什么匿名函数没有取得其包含作用域（或外部作用域）的 this 对象呢？

　　前面曾经提到过，每个函数在被调用时都会自动取得两个特殊变量： this 和 arguments。内部函数在搜索这两个变量时，只会搜索到其活动对象为止，因此永远不可能直接访问外部函数中的这两个变量（这一点通过图 7-2 可以看得更清楚）。不过，把外部作用域中的 this 对象保存在一个闭包能够访问到的变量里，就可以让闭包访问该对象了，如下所示。

```js
var name = "The Window";
var object = {
    name: "My Object",
    getNameFunc: function() {
        var that = this;
        return function() {
            return that.name;
        };
    }
};
console.log(object.getNameFunc()()); //"My Object"
```
　　代码中突出的行展示了这个例子与前一个例子之间的不同之处。在定义匿名函数之前，我们把 this 对象赋值给了一个名叫 that 的变量。而在定义了闭包之后，闭包也可以访问这个变量，因为它是我们在包含函数中特意声名的一个变量。即使在函数返回之后， that 也仍然引用着 object，所以调用object.getNameFunc()()就返回了"My Object"。

>this 和 arguments 也存在同样的问题。如果想访问作用域中的 arguments 对象，必须将对该对象的引用保存到另一个闭包能够访问的变量中。


　　在几种特殊情况下， this 的值可能会意外地改变。 比如， 下面的代码是修改前面例子的结果。
```js
var name = "The Window";
var object = {
    name: "My Object",
    getName: function() {
        return this.name;
    }
};
```
　　这里的 getName() 方法只简单地返回 this.name 的值。 以下是几种调用 object.getName() 的方式以及各自的结果。
>　　object.getName(); //"My Object"
>　　(object.getName)(); //"My Object"
>　　(object.getName = object.getName)(); //"The Window"，在非严格模式下

　　第一行代码跟平常一样调用了 object.getName()，返回的是"My Object"，因为 this.name就是 object.name。第二行代码在调用这个方法前先给它加上了括号。虽然加上括号之后，就好像只是在引用一个函数，但 this 的值得到了维持，因为 object.getName 和(object.getName)的定义是相同的。第三行代码先执行了一条赋值语句，然后再调用赋值后的结果。因为这个赋值表达式的值是"`函数本身`"，所以 this 的值不能得到维持，结果就返回了"The Window"。
　　
　　当然，你不大可能会像第二行和第三行代码一样调用这个方法。不过，这个例子有助于说明即使是语法的细微变化，都有可能意外改变 this 的值。

#### 7.2.3 内存泄漏
　　由于 IE9 之前的版本对 JScript 对象和 COM 对象使用不同的垃圾收集例程（ 第 4 章曾经讨论过），因此闭包在 IE 的这些版本中会导致一些特殊的问题。 具体来说， 如果闭包的作用域链中保存着一个HTML 元素， 那么就意味着该元素将无法被销毁。 来看下面的例子。
```js
function assignHandler() {
    var element = document.getElementById("someElement");
    element.onclick = function() {
        alert(element.id);
    };
}
```
　　以上代码创建了一个作为 element 元素事件处理程序的闭包， 而这个闭包则又创建了一个循环引用（ 事件将在第 13 章讨论）。 由于匿名函数保存了一个对 assignHandler() 的活动对象的引用， 因此就会导致无法减少 element 的引用数。 只要匿名函数存在， element 的引用数至少也是 1， 因此它所占用的内存就永远不会被回收。 不过， 这个问题可以通过稍微改写一下代码来解决， 如下所示。
```js
function assignHandler() {
    var element = document.getElementById("someElement");
    var id = element.id;
    element.onclick = function() {
        alert(id);
    };
    element = null;
}
```
　　在上面的代码中， 通过把 element.id 的一个副本保存在一个变量中， 并且在闭包中引用该变量消除了循环引用。 但仅仅做到这一步， 还是不能解决内存泄漏的问题。 必须要记住： 闭包会引用包含函数的整个活动对象， 而其中包含着 element。 即使闭包不直接引用 element， 包含函数的活动对象中也仍然会保存一个引用。 因此， 有必要把 element 变量设置为 null。 这样就能够解除对 DOM 对象的引用， 顺利地减少其引用数， 确保正常回收其占用的内存。
　　
### 7.3 模仿块级作用域
　　如前所述， JavaScript 没有块级作用域的概念。 这意味着在块语句中定义的变量， 实际上是在包含函数中而非语句中创建的， 来看下面的例子。
```js
function outputNumbers(count) {
    for (var i = 0; i < count; i++) {
        alert(i);
    }
    alert(i); //计数
}
```
　　这个函数中定义了一个for 循环， 而变量 i 的初始值被设置为 0。 在 Java、 C++等语言中， 变量 i 只会在for 循环的语句块中有定义， 循环一旦结束， 变量 i 就会被销毁。 可是在 JavaScrip 中， 变量 i是定义在 ouputNumbers() 的活动对象中的， 因此从它有定义开始， 就可以在函数内部随处访问它。 即使像下面这样错误地重新声明同一个变量， 也不会改变它的值。

```js
function outputNumbers(count) {
    for (var i = 0; i < count; i++) {
        alert(i);
    }
    var i; //重新声明变量
    alert(i); //计数
}
```

　　JavaScript 从来不会告诉你是否多次声明了同一个变量； 遇到这种情况， 它只会对后续的声明视而不见（ 不过， 它会执行后续声明中的变量初始化）。 匿名函数可以用来模仿块级作用域并避免这个问题。用作块级作用域（ 通常称为私有作用域） 的匿名函数的语法如下所示。
```js
(function() {
    //这里是块级作用域
})();
```

　　以上代码定义并立即调用了一个匿名函数。 将函数声明包含在一对圆括号中， 表示它实际上是一个函数表达式。 而紧随其后的另一对圆括号会立即调用这个函数。 如果有读者感觉这种语法不太好理解，可以再看看下面这个例子。

```js
var count = 5;
outputNumbers(count);
```

　　这里初始化了变量 count， 将其值设置为 5。 当然， 这里的变量是没有必要的， 因为可以把值直接传给函数。 为了让代码更简洁， 我们在调用函数时用 5 来代替变量 count， 如下所示。
```js
outputNumbers(5);
```
　　这样做之所以可行， 是因为变量只不过是值的另一种表现形式， 因此用实际的值替换变量没有问题。再看下面的例子。
```js
var someFunction = function() {
    //这里是块级作用域
};
someFunction();
```
　　这个例子先定义了一个函数， 然后立即调用了它。 定义函数的方式是创建一个匿名函数， 并把匿名函 数 赋 值 给 变 量 someFunction。 而 调 用 函 数 的 方 式 是 在 函 数 名 称 后 面 添 加 一 对 圆 括 号， 即someFunction()。 通过前面的例子我们知道， 可以使用实际的值来取代变量 count， 那在这里是不是也可以用函数的值直接取代函数名呢？ 然而， 下面的代码却会导致错误。
```js
function() {
    //这里是块级作用域
}(); //出错！
```
　　这段代码会导致语法错误， 是因为 JavaScript 将 function 关键字当作一个函数声明的开始， 而函数声明后面不能跟圆括号。 然而， 函数表达式的后面可以跟圆括号。 要将函数声明转换成函数表达式，只要像下面这样给它加上一对圆括号即可。
```js
(function() {
    //这里是块级作用域
})();
```
　　无论在什么地方， 只要临时需要一些变量， 就可以使用私有作用域， 例如：
```js
function outputNumbers(count) {
    (function() {
        for (var i = 0; i < count; i++) {
            alert(i);
        }
    })();
    alert(i); //导致一个错误！
}
```
　　在这个重写后的 outputNumbers() 函数中， 我们for 循环外部插入了一个私有作用域。 在匿名函数中定义的任何变量， 都会在执行结束时被销毁。 因此， 变量 i 只能在循环中使用， 使用后即被销毁。而在私有作用域中能够访问变量 count， 是因为这个匿名函数是一个闭包， 它能够访问包含作用域中的所有变量。

　　这种技术经常在全局作用域中被用在函数外部， 从而限制向全局作用域中添加过多的变量和函数。一般来说， 我们都应该尽量少向全局作用域中添加变量和函数。在一个由很多开发人员共同参与的大型应用程序中， 过多的全局变量和函数很容易导致命名冲突。 而通过创建私有作用域， 每个开发人员既可以使用自己的变量， 又不必担心搞乱全局作用域。 例如：

```js
(function() {
    var now = new Date();
    if (now.getMonth() == 0 && now.getDate() == 1) {
        alert("Happy new year!");
    }
})();
```
　　把上面这段代码放在全局作用域中， 可以用来确定哪一天是 1 月 1 日； 如果到了这一天， 就会向用户显示一条祝贺新年的消息。 其中的变量 now 现在是匿名函数中的局部变量， 而我们不必在全局作用域中创建它。

>这种做法可以减少闭包占用的内存问题，因为没有指向匿名函数的引用。只要函数执行完毕，就可以立即销毁其作用域链了。


### 7.4 私有变量
#### 7.4.1 静态私有变量
#### 7.4.2 模块模式
#### 7.4.3 增强的模块模式
```js
var singleton = function() {
    //私有变量和私有函数
    var privateVariable = 10;

    function privateFunction() {
            return false;
        }
        //创建对象
    var object = new CustomType();
    //添加特权/公有属性和方法
    object.publicProperty = true;
    object.publicMethod = function() {
        privateVariable++;
        return privateFunction();
    };
    //返回这个对象
    return object;
}();
```

```js
var application = function() {
    //私有变量和函数
    var components = new Array();
    //初始化
    components.push(new BaseComponent());
    //创建 application 的一个局部副本
    var app = new BaseComponent();
    //公共接口
    app.getComponentCount = function() {
        return components.length;
    };
    app.registerComponent = function(component) {
        if (typeof component == "object") {
            components.push(component);
        }
    };

    return app;
}();
```

### 7.5 小结

　　在 JavaScript 编程中，函数表达式是一种非常有用的技术。使用函数表达式可以无须对函数命名，
从而实现动态编程。匿名函数，也称为拉姆达函数，是一种使用 JavaScript 函数的强大方式。以下总结
了函数表达式的特点。

　　  函数表达式不同于函数声明。函数声明要求有名字，但函数表达式不需要。没有名字的函数表
达式也叫做匿名函数。

　　  在无法确定如何引用函数的情况下，递归函数就会变得比较复杂；

　　  递归函数应该始终使用 arguments.callee 来递归地调用自身， 不要使用函数名——函数名可
能会发生变化。

　　当在函数内部定义了其他函数时，就创建了闭包。闭包有权访问包含函数内部的所有变量，原理
如下。

　　  在后台执行环境中，闭包的作用域链包含着它自己的作用域、包含函数的作用域和全局作用域。

　　  通常，函数的作用域及其所有变量都会在函数执行结束后被销毁。

　　  但是，当函数返回了一个闭包时，这个函数的作用域将会一直在内存中保存到闭包不存在为止。
使用闭包可以在 JavaScript中模仿块级作用域（JavaScript本身没有块级作用域的概念） ，要点如下。

　　  创建并立即调用一个函数，这样既可以执行其中的代码，又不会在内存中留下对该函数的引用。

　　  结果就是函数内部的所有变量都会被立即销毁——除非将某些变量赋值给了包含作用域（即外
部作用域）中的变量。

　　闭包还可以用于在对象中创建私有变量，相关概念和要点如下。

　　  即使 JavaScript 中没有正式的私有对象属性的概念，但可以使用闭包来实现公有方法，而通过公
有方法可以访问在包含作用域中定义的变量。

　　  有权访问私有变量的公有方法叫做特权方法。

　　  可以使用构造函数模式、原型模式来实现自定义类型的特权方法，也可以使用模块模式、增强
的模块模式来实现单例的特权方法。

　　JavaScript 中的函数表达式和闭包都是极其有用的特性，利用它们可以实现很多功能。不过，因为
创建闭包必须维护额外的作用域，所以过度使用它们可能会占用大量内存。


## 第 13 章 事件
### 13.2 事件处理程序
#### 13.2.3 DOM2 级事件处理程序

　　“DOM2级事件” 定义了两个方法， 用于处理指定和删除事件处理程序的操作： addEventListener()和 removeEventListener() 。所有 DOM 节点中都包含这两个方法，并且它们都接受 3 个参数：要处理的事件名、作为事件处理程序的函数和一个布尔值。最后这个布尔值参数如果是 true ，表示在捕获阶段调用事件处理程序；如果是 false ，表示在冒泡阶段调用事件处理程序。
　　要在按钮上为 click 事件添加事件处理程序，可以使用下列代码：

```js
var btn = document.getElementById("myBtn");
btn.addEventListener("click", function(){
	console.log(this.id);
}, false);
```
　　上面的代码为一个按钮添加了 onclick 事件处理程序，而且该事件会在冒泡阶段被触发（因为最后一个参数是 false ） 。与 DOM0 级方法一样，这里添加的事件处理程序也是在其依附的元素的作用域中运行。使用 DOM2 级方法添加事件处理程序的主要好处是可以添加多个事件处理程序。来看下面的例子。
```js
var btn = document.getElementById("myBtn");
btn.addEventListener("click", function() {
	console.log(this.id);
}, false);
btn.addEventListener("click", function() {
	console.log("Hello world!");
}, false);
```

　　这里为按钮添加了两个事件处理程序。这两个事件处理程序会按照添加它们的顺序触发，因此首先会显示元素的 ID，其次会显示 "Hello world!" 消息。
　　通过 addEventListener() 添加的事件处理程序只能使用 removeEventListener() 来移除；移除时传入的参数与添加处理程序时使用的参数相同。这也意味着通过 addEventListener() 添加的匿名函数将无法移除，如下面的例子所示。

```js
var btn = document.getElementById("myBtn");
btn.addEventListener("click", function() {
	console.log(this.id);
}, false);

// 这里省略了其他代码

btn.removeEventListener("click", function() { // 没有用！
	console.log(this.id);
}, false);
```

　　在这个例子中，我们使用 addEventListener() 添加了一个事件处理程序。虽然调用 remove-EventListener() 时看似使用了相同的参数，但实际上，第二个参数与传入 addEventListener() 中的那一个是完全不同的函数。而传入 removeEventListener() 中的事件处理程序函数必须与传入addEventListener() 中的相同，如下面的例子所示。

```js
var btn = document.getElementById("myBtn");
var handler = function() {
	console.log(this.id);
};
btn.addEventListener("click", handler, false);
// 这里省略了其他代码
btn.removeEventListener("click", handler, false); // 有效！
```

>IE9、Firefox、Safari、Chrome和 Opera 支持 DOM2 级事件处理程序。

```js
EventUtil 的用法如下所示。
var EventUtil = {
	addHandler: function(element, type, handler) {
		if (element.addEventListener) {
			element.addEventListener(type, handler, false);
		} else if (element.attachEvent) {
			element.attachEvent("on" + type, handler);
		} else {
			element["on" + type] = handler;
		}
	},
	removeHandler: function(element, type, handler) {
		if (element.removeEventListener) {
			element.removeEventListener(type, handler, false);
		} else if (element.detachEvent) {
			element.detachEvent("on" + type, handler);
		} else {
			element["on" + type] = null;
		}
	}
};
```

可以像下面这样使用 EventUtil 对象：
```
var btn = document.getElementById("myBtn");
var handler = function() {
	console.log("Clicked");
};
EventUtil.addHandler(btn, "click", handler);
// 这里省略了其他代码
EventUtil.removeHandler(btn, "click", handler);
```

### 13.3 事件对象

#### 13.3.1 DOM中的事件对象
在需要通过一个函数处理多个事件时， 可以使用 type 属性。 例如：
```js
var btn = document.getElementById("myBtn");
var handler = function(event) {
	switch (event.type) {
		case "click":
			alert("Clicked");
			break;
		case "mouseover":
			event.target.style.backgroundColor = "red";
			break;
		case "mouseout":
			event.target.style.backgroundColor = "";
			break;
	}
};
btn.onclick = handler;
btn.onmouseover = handler;
btn.onmouseout = handler;
```
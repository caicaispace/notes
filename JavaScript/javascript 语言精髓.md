#javascript 语言精髓
@[js|学习笔记]

[TOC]

---
##第一章 —— 精华（Good Parts）

---
##第二章 —— 语法（Grammar）

---
##第三章 —— 对象（Objects）

---

>**JavaScript** 的简单数据类型包括数字、字符串、布尔值 ( `true` 和`false`)、`null` 值和`undefined`值。其他所有的值都是对象。数字、字符串和布尔值“貌似”对象，因为它们拥有方法，但它们是不可变的。**JavaScript** 中的对象是可变的键控集合`(keyed collections)`。在 **JavaScript** 中，数组是对象，函数是对象，正则表达式是对象，当然，对象自然也是对象。

>对象是属性的容器，其中每个属性都拥有名字和值。属性的名字可以是包括空字符串在内的任意字符串。属性值可以是除`undefined` 值之外的任何值。

>**JavaScript** 里的对象是无类型`(class-free)`的。它对新属性的名字和属性的值没有限制。对象适合用于汇集和管理数据。对象可以包含其他对象，所以它们可以容易地表示成树状或图形结构。

>**JavaScript** 包含一种原型链的特性，允许对象继承另一个对象的属性。正确地使用它能减少对象初始化时消耗的时间和内存。
### 对象字面量（Object Literals）
>对象字面量提供了一种非常方便地创建新对象值的表示法。一个对象字面量就是包围在一对花括号中的零或多个“名/值”对。对象字面量可以出现在任何允许表达式出现的地方。

```js
var empty_object = {};
var stooge = {
	"first-name" : "Jerome",
	"last-name" : "Howard"
};
```
>属性名可以是包括空字符串在内的任何字符串。在对象字面量中，如果属性名是一个合法的 **JavaScript** 标识符且不是保留字，则并不强制要求用引号括住属性名。所以用引号括住`"first-name"`是必需的，但是否括住`first_ name`则是可选的。逗号用来分隔多个 **“名/值"** 对。

>属性的值可以从包括另一个对象字面量在内的任意表达式中获得。对象是可嵌套的
```js
var filght = {
	airline: "Oceanic",
	number : 815,
	departure: {
		IATA: "SYD",
		time: "2015-06-22 20:51",
		city: "Ai Hu Cun No/79"
	},
	arrival: {
		IATA: "LAX",
		time: "2015-06-23 08:30",
		city: "Nai Lin Zhong Xin"
	}
}
```

###检索（Retrieval）
>要检索对象里包含的值，可以采用在`[]`后缀中括住一个字符串表达式的方式。如果字符串表达式是一个字符串字面量，而且它是一个合法的 **JavaScript** 标识符且不是保留字，那么也可以用**`.`**表示法代替。优先考虑使用**`.`**表示法，因为它更紧凑且可读性更好。

```js
stooge["first-name"] 	// "Jerome"
flight.departure.IATA	//	"SYD"
```
>如果你尝试检索一个并不存在的成员属性的值，将返回`undefined`

```js
stooge["middle-name"] 	// undefiend
flight.status 			// undefiend
stooge["FIRST-NAME"] 	// undefiend
```
>`||`运算符可以用来填充默认值

```js
var middle = stooge["middle-name"] || "(none)";
var status = flight.status || "unknown";
```
>尝试从`undefined`的成员属性中取值将会导致`TypeError`异常。这时可以通过`&&`运算符来避免错误。
```js
flight.equipment 							// undefined
flight.equipment.model 						// throw "TypeError"
flight.equipment && flight.equipment.model 	// undefined
```

###更新（Update）

>对象里的值可以通过赋值语句来更新。如果属性名已经存在于对象里，那么这个属性的值就会被替换。

```js
stooge['first-name'] = 'Jerome'
```
>如果对象之前没有拥有那个属性名，那么该属性就被扩充到对象中。
```js
stooge['middle-name'] = 'Laster';
stooge.nikename = 'Curly';
flight.equipment = {
	model: 'Boeing 777'
};
flight.status = 'overdue'
```

###引用（Reference）
>对象通过引用来传递。它们永远不会被复制:

```js
var x = stooge;
x.nikename = 'Curly';
var nick = stooge.nickname;
//因为 x 和 stooge 是指向同一个时象的引用，所以 nick 为’Curly'

var a = {},b = {}, c = {};
// a、b 和 c 每个都引用一个不同的对象
a = b = c = {};
// a、b 和 c 都引用同一个空对象
```
###原型（Prototype）

>每个对象都连接到一个原型对象，并且它可以从中继承属性。所有通过对象字面量创建的对象都连接到。`Object.prototype`，它是 **JavaScript** 中的标配对象。

>当你创建一个新对象时，你可以选择某个对象作为它的原型。**JavaScript** 提供的实现机制杂乱而复杂，但其实可以被明显地简化。我们将给。`Object`增加一个`create`方法。这个方法

>创建一个使用原对象作为其原型的新对象。下一章将会有更多关于函数的内容。

```js
if (typeof Object.beget !== 'function') {
	Object.create = function(o){
		var F = function(){};
		F.prototype = o;
		return new F();
	};
};

var another_stooge = Object.create(stooge);
```
>原型连接在更新时是不起作用的。当我们对某个对象做出改变时，不会触及该对象的原型:

```js
another_stooge['first-name']  = 'Harry';
another_stooge['middle-name'] = 'Moses';
another_stooge.nickname       = 'Moe';
```
>原型连接只有在检索值的时候才被用到。如果我们尝试去获取对象的某个属性值，但该对象没有此属性名，那么 **JavaScript** 会试着从原型对象中获取属性值。如果那个原型对象也没有该属性，那么再从它的原型中寻找，依此类推，直到该过程最后到达终点`Object.prototype`。如果想要的属性完全不存在于原型链中，那么结果就是`undefined`值。这个过程称为委托。

>原型关系是一种动态的关系。如果我们添加一个新的属性到原型中，该属性会立即对所有基于该原型创建的对象可见。
```js
stooge.prototype = 'actor';
another_stooge.profession // 'actor'
```
>我们将会在第6章中看到更多关于原型链的内容。

###反射（Reflection）
>检查对象并确定对象有什么属性是很容易的事情，只要试着去检索该属性并验证取得的值。`typeof`操作符对确定属性的类型很有帮助:
```js
typeof flight.number 	// 'number'
typeof flight.status 	// 'string'
typeof flight.arrival 	// object
typeof flight.manifest 	// 'undefined'
```

>请注意原型链中的任何属性都会产生值:

```js
typeof flight.toString 		// 'function'
typeof flight.constructor 	// 'function'
```
>有两种方法去处理掉这些不需要的属性。第一个是让你的程序做检查并丢弃值为函数的属性。一般来说，当你想让对象在运行时动态获取自身信息时，你关注更多的是数据，而你应该意识到一些值可能会是函数。

>另一个方法是使用`hasOwnProperty`方法，如果对象拥有独有的属性，它将返回`true`。`hasownProperty`方法不会检查原型链。

```js
flight.hasOwnProperty('number');		// true
flight.hasOwnProperty('constructor'); 	// false
```

###枚举（Enumeration）
>`for in`语句可用来遍历一个对象中的所有属性名。该枚举过程将会列出所有的属性 —— 包括函数和你可能不关心的原型中的属性 —— 所以有必要过滤掉那些你不想要的值。最为常用的过滤器是`hasOwnProperty`方法，以及使用`typeof`来排除函数


```js
var name;
for(name in another_stooge){
	if (typeof another_stooge[name] !== 'function') {
		console.log(name + ' : ' + another_stooge[name]);
	};
}
```

>属性名出现的顺序是不确定的，因此要对任何可能出现的顺序有所准备。如果你想要确保属性以特定的顺序出现，最好的办法就是完全避免使用`for in`语句，而是创建一个数组，在其中以正确的顺序包含属性名

```js
var i;
var properties = [
	'first-name',
	'middle-name',
	'last-name',
	'profession'
];
for(i=0; i<properties.length; i += 1){
	console.log(properties[i] + ':' + another_stooge[properties[i]]);
}
```

>通过使用中的属性，`for`而不是`for in`，可以得到我们想要的属性，而不用担心可能发掘出原型链并且我们按正确的顺序取得了它们的值。

###删除（Delete）

>`delete`运算符可以用来删除对象的属性。如果对象包含该属性，那么该属性就会被移除。

>它不会触及原型链中的任何对象。

>删除对象的属性可能会让来自原型链中的属性透现出来:

```js
another_stooge.nickname // 'Moe'

// 删除 another_stooge 的 nickname 属性，从而暴露出原型的 nickname 属性是
delete another_stooge.nickname;

another_stooge.nickname // 'Curly'
```
###减少全局变量污染（Global Abandonment）

>**JavaScript** 可以很随意地定义全局变量来容纳你的应用的所有资源。遗憾的是，全局变量削弱了程序的灵活性，应该避免使用。

>最小化使用全局变量的方法之一是为你的应用只创建一个唯一的全局变量:
```js
var MYAPP = {};
```
>该变量此时变成了你的应用的容器
```js
MYAPP.stooge = {
	"first-name": "Joe",
	"last-name": "Howard"
};

MYAPP.flight = {
	airline: "Oceanic",
	number: 815,
	departure: {
		IATA: "SYD",
		time: "2015-06-22 22:49",
		city: "Nan Chang Ai Hu Cu No/79",
	},
	arrival: {
		IATA: "LAX",
		time: "2015-06-23 08:30",
		city: "Nan Chang Nai Lin Center"
	}
};
```
>只要把全局性的资源都纳人一个名称空间之下，你的程序与其他应用程序、组件或类库之间发生冲突的可能性就会显著降低。你的程序也会变得更容易阅读，因为很明显，`MYAPP.stooge`指向的是顶层结构。在下一章中，我们会看到使用闭包来进行信息隐藏的方式，它是另一种有效减少全局污染的方法。


---
##第四章 —— 函数（Function）
>**JavaScript** 设计得最出色的就是它的函数的实现。它几乎接近于完美。但是，想必你也能预料到，**JavaScript** 的函数也存在瑕疵。

>函数包含一组语句，它们是 **JavaScript** 的基础模块单元，用于代码复用、信息隐藏和组合调用。函数用于指定对象的行为。一般来说，所谓编程，就是将一组需求分解成一组函数与数据结构的技能。

###函数对象（Function Objects）
>**JavaScript** 中的函数就是对象。对象是 **“名/值”** 对的集合并拥有一个连到原型对象的隐藏连接。对象字面量产生的对象连接到`Object.prototype`。函数对象连接到`Function.prototype`（该原型对象本身连接到`Object.prototype`。每个函数在创建时会附加两个隐藏属性：**函数的上下文和实现函数行为的代码**

- `函数的上下文和实现函数行为的代码`：**JavaScript** 创建一个函数时象时，会给该对象设呈一个**“调用”**属性。当 **JavaScript** 调用一个函数时，可理解为调用此函数的“调用”属性。详细的描述请参阅 ECMAScript 规范的“13.2  Creating Function Objects" 


>每个函数对象在创建时也随配有一个`prototype`属性。它的值是一个拥有`constructor`属性且值即为该函数的对象。这和隐藏连接到`Function.prototype`完全不同。这个令人费解的构造过程的意义将会在下个章节中揭示。

>因为函数是对象，所以它们可以像任何其他的值一样被使用。函数可以保存在变量、对象和数组中。函数可以被当做参数传递给其他函数，函数也可以再返回函数。而且，因为函数是对象，所以函数可以拥有方法。

>函数的与众不同之处在于它们可以被调用。

###函数字面量（Function Literal）

>函数对象通过函数字面量来创建:
```js
//创建一个名为 add 的变量，并用来把两个数字相加的函数赋值给它。
var add = function (a, b){
    return a+b;
};
```
>函数字面量包括 4 个部分：

>　　第 1 个部分是保留字`function`

>　　第 2 个部分是函数名，它可以被省略。函数可以用它的名字来递归地调用自己。此名字也能被调试器和开发工具用来识别函数。如果没有给函数命名，比如上面这个例子，它被称为匿名函数`(anonymous)`

>　　第 3 个部分是包围在圆括号中的一组参数。多个参数用逗号分隔。这些参数的名称将被定义为函数中的变量。它们不像普通的变量那样将被初始化为`undefined`，而是在该函数被调用时初始化为实际提供的参数的值。

>　　第 4 个部分是包围在花括号中的一组语句。这些语句是函数的主体，它们在函数被调用时执行。

>函数字面量可以出现在任何允许表达式出现的地方。函数也可以被定义在其他函数中。一个内部函数除了可以访问自己的参数和变量，同时它也能自由访问把它嵌套在其中的父函数的参数与变量。通过函数字面量创建的函数对象包含一个连到外部上下文的连接。这被称为闭包 **(closure)**。它是 **JavaScript** 强大表现力的来源。

###调用（Invocation）
>调用一个函数会暂停当前函数的执行，传递控制权和参数给新函数。除了声明时定义的形式参数，每个函数还接收两个附加的参数:`this`和`arguments`。参数`this`在面向对象编程中非常重要，它的值取决于调用的模式。在 **JavaScript** 中一共有 4 种调用模式:方法调用模式、函数调用模式、构造器调用模式和`apply`调用模式。这些模式在如何初始化关键参数`this`上存在差异。

>调用运算符是跟在任何产生一个函数值的表达式之后的一对圆括号。圆括号内可包含零个或多个用逗号隔开的表达式。每个表达式产生一个参数值。每个参数值被赋予函数声明时定义的形式参数名。当实际参数`(arguments)`的个数与形式参数`(parameters)`的个数不匹配时，不会导致运行时错误。如果实际参数值过多了，超出的参数值会被忽略。如果实际参数值过少，缺失的值会被替换为`undefined`。对参数值不会进行类型检查:任何类型的值都可以被传递给任何参数。

####方法调用模式（The Method Invocation Pattern）

>当一个函数被保存为对象的一个属性时，我们称它为一个方法。当一个方法被调用时，`this`被绑定到该对象。如果调用表达式包含一个提取属性的动作（即包含一个**`.`**点表达式或`[subscript]`下标表达式），那么它就是被当做一个方法来调用。

```js
// 创建 myObject 对象。它有一个 value 属性和一个 increment 方法。
// increment 方法接受一个可选的参数。如果参数不是数字，那么默认使用数字 1

var myObject = {
    value : 0,
    increment: function(inc){
        this.value += typeof inc === 'number' ? inc : 1;
        console.log(this.name); // myObject
    },
    name: 'myObject' 
};

myObject.increment();
console.log(myObject.value); // 1

myObject.increment(2);
console.log(myObject.value); // 3
```
>方法可以使用`this`。访问自己所属的对象，所以它能从对象中取值或对对象进行修改。`this` 到对象的绑定发生在调用的时候。这个“超级”延迟绑定`(very late binding)`使得函数可以对`this`。高度复用。通过`this`可取得它们所属对象的上下文的方法称为公共方法`(public method)`。

####函数调用模式（The Function Invocation Pattern）

>当一个函数并非一个对象的属性时，那么它就是被当做一个函数来调用的:
```js
var sum = add (3,4); //sum的值为 7 
```
>以此模式调用函数时，`this`被绑定到全局对象。这是语言设计上的一个错误。倘若语言设计正确，那么当内部函数被调用时，`this`应该仍然绑定到外部函数的`this`变量。这个设计错误的后果就是方法不能利用内部函数来帮助它工作，因为内部函数的`this`被绑定了错误的值，所以不能共享该方法对对象的访问权。幸运的是，有一个很容易的解决方案:如果该方法定义一个变量并给它赋值为`this`，那么内部函数就可以通过那个变量访问到`this`按照约定，我把那个变量命名为`that`;

```js
// 给 myObj 增加一个 double 方法。

myObject.double = function(){
	var that = this; // 解决方法

	var helper = function(){
		that.value = add(that.value,that.value);
	};

	helper(); // 以函数的形式调用 helper
};

// 以方法的形式调用 double

myObject.double();
console.log(myObject.value); // 6
```

####构造器调用模式（The Construotor Invocation Pattern）

>**JavaScript** 是一门基于原型继承的语言。这意味着对象可以直接从其他对象继承属性。该语言是无类型的。

>这偏离了当今编程语言的主流风格。当今大多数语言都是基于类的语言。尽管原型继承极富表现力，但它并未被广泛理解。 **JavaScript** 本身对它原型的本质也缺乏信心，所以它提供了一套和基于类的语言类似的对象构建语法。有类型化语言编程经验的程序员们很少有愿意接受原型继承的，并且认为借鉴类型化语言的语法模糊了这门语言真实的原型本质。真是两边都不讨好。

>如果在一个函数前面带上`new`来调用，那么背地里将会创建一个连接到该函数的`prototype`成员的新对象，同时`this`会被绑定到那个新对象上。

>`new`前缀也会改变`return`语句的行为。我们将会在后面看到更多的相关内容。

- **原实例**
```js
//创建一个名为 Quo 的构造器函数。它构造一个带有 status 属性的时象。

var Quo = function(string){
	this.status = string;
};

//给 Quo 的所有实例提供一个名为 get_status 的公共方法。

Quo.prototype.get_status = function(){
	return this.status;
};

// 构造一个 Quo 实例。

var myQuo  = new Quo("confused");

console.log(myQuo.get_status()); // confused
```

- **扩展实例`	请使用 chrome console 查看结果`**
```js
var Quo = function(string){

    console.log('this -> ',this); 

    type = string;
    console.log('typeof -> '+ typeof this);
    console.log(string + ' -> ' + type);

    console.log(string + ' -> ' + this.type);
    this.type = string ; 
    console.log(string + ' -> ' + this.type);

    var type = string;

    return this.type || type;
};


Quo.prototype.get_type = function(){
    return this.type ? this.type + ' -> get_type' : 'function -> get_type';
};

// 调用 Quo 函数。
console.log('%c---------- I\'m function ----------','font-size:20px;color:red;');
var func_1      = Quo('function');
var func_return = Quo.prototype.get_type();
console.log('is_function -> ' + func_1);
console.log('function_return -> ' + func_return);
console.log(typeof func_1);

// 构造一个 Quo 实例。
console.log('%c---------- I\'m obj_1 ----------','font-size:20px;color:red;');
var obj_1  = new Quo("obj_1");
console.log(obj_1.get_type());

// 构造另一个 Quo 实例。
console.log('%c---------- I\'m obj_2 ----------','font-size:20px;color:red;');
var obj_2 = new Quo('obj_2');
console.log(obj_2.get_type());


console.log('%c---------- other ----------','font-size:20px;color:red;');
console.log(typeof Quo);
console.log(typeof func_1);
console.log(typeof obj_1);
console.log(typeof obj_2);
```
>一个函数，如果创建的目的就是希望结合`new`前缀来调用，那它就被称为构造器函数。按照约定，它们保存在以大写格式命名的变量里。如果调用构造器函数时没有在前面加上`new`,可能会发生非常糟糕的事情，既没有编译时警告，也没有运行时警告，所以大写约定非常重要。

>我不推荐使用这种形式的构造器函数。在下一章中我们会看到更好的替代方式。

####Apply调用模式（The Apply Invocation Pattern）

>因为 **JavaScript** 是一门函数式的面向对象编程语言，所以函数可以拥有方法。

>`apply`方法让我们构建一个参数数组传递给调用函数。它也允许我们选择`this`的值。`apply`方法接收两个参数
>第 1 个是要绑定给`this`的值
>第 2 个就是一个参数数组。

```js
//构造一个包含两个数字的数组，并将它们相加。
var array = [3,4];
var sum = add.apply(null, array); // sum 值为 7

//构造一个包含status成员的对象。

var statusObject = {
	status:'A-OK'
}
// statusObject 并没有继承自 Quo.prototype，但我们可以在 statusObject 上调
// 用 get_status方法，尽管 statusObject 并没有一个名为 get_status 的方法。

var status = Quo.prototype.get_status.apply(statusObject);
console.log(status);
//status 值为 'A-OK'。
```


###参数 （arguments）

>当函数被调用时，会得到一个“免费”配送的参数，那就是`arguments`数组。函数可以通过此参数访问所有它被调用时传递给它的参数列表，包括那些没有被分配给函数声明时定义的形式参数的多余参数。这使得编写一个**无须指定参数个数的函数成为可能**:
```js
// 构造一个将大童的位相加的函数。
// 注意该函数内部定义的变量sum不会与函数外部定义的sum产生冲突。
// 该函数只会看到内部的那个变量。

var sum = function(){
	//console.log(arguments);//{ '0': 4, '1': 8, '2': 15, '3': 16, '4': 23, '5': 42 }
	var i,sum = 0;
	for (i = 0; i < arguments.length; i += 1){
		sum += arguments[i];
	}
	return sum;
};
console.log(sum(4,8,15,16,23,42)); //108
```
>这不是一个特别有用的模式。在第 6 章中，我们将会看到如何给数组添加一个相似的方法来达到同样的效果。

>因为语言的一个设计错误，`arguments` 并不是一个真正的数组。它只是一个**“类似数组 (array-like)”**的对象。`arguments `拥有一个 length 属性，但它没有任何数组的方法。我们将在本章结尾看到这个设计错误导致的后果。

### 返回（Return）

>当一个函数被调用时，它从第一个语句开始执行，并在遇到关闭函数体的`}`时结束。然后函数把控制权交还给调用该函数的程序。

>**return** 语句可用来使函数提前返回。当 **return** 被执行时，函数立即返回而不再执行余下的语句。

>一个函数总是会返回一个值。如果没有指定返回值，则返回`undefined`。

>如果函数调用时在前面加上了`new`前缀，且返回值不是一个对象，则返回**`this`(该新对象)**。


###异常（Exceptions）

>JavaScript 提供了一套异常处理机制。异常是干扰程序的正常流程的不寻常 ( 但并非完全是出乎意料的 ) 的事故。当发现这样的事故时，你的程序应该抛出一个异常

```js
var add = function(a,b){
	if(typeof a !== 'number' || typeof b !== 'number'){
		throw {
			name : 'TypeError',
			message : 'add needs numbers'
		};
	};
	return a + b;
}
```

>**throw** 语句中断函数的执行。它应该抛出一个 `exception` 对象，该对象包含一个用来识别异常类型的 name 属性和一个描述性的 message 属性。你也可以添加其他的属性。

>该 **exception** 对象将被传递到一个`try`语句的`catch`从句

```js
//构造一个 try_it 让函数，以不正确的方式调用之前的add函数。
var try_it = function(){
	try {
		add('seven');
	} catch (e) {
		console.log(e.name + ':' + e.message); // ReferenceError:add is not defined
	}
}
try_it();
```

>如果在 `try` 代码块内抛出了一个异常，控制权就会跳转到它的 `catch` 从句。

>一个 `try` 语句只会有一个捕获所有异常的 `catch` 代码块。如果你的处理手段取决于异常的类型，那么异常处理器必须检查异常对象的name属性来确定异常的类型。

###扩展类型的功能（Augmenting Types）

>JavaScript 允许给语言的基本类型扩充功能。在第3章中，我们已经看到，通过给 `object.prototype` 添加方法，可以让该方法对所有对象都可用。这样的方式对函数、数组、字符串、数字、正则表达式和布尔值同样适用。

>举例来说，我们可以通过给	`Function.prototype`	增加方法来使得该方法对所有函数可用:

```js
Function.prototype.method = function(name, func) {
	this.prototype[name] = func;
	return this;
};
```
>通过给`Function.prototype`增加一个 **method** 方法，我们下次给对象增加方法的时候就不必键入`prototype`这几个字符，省掉了一点麻烦。

>JavaScript 没有专门的整数类型，但有时候确实只需要提取数字中的整数部分。JavaScript 本身提供的取整方法有些丑陋。我们可以通过给`Number.prototype`增加一个 **integer** 方法来改善它。它会根据数字的正负来判断是使用`Math.ceiling`还是`Math.floor`。

```js
Number.method('integer',function(){
	return Math[this < 0 ? 'ceil' : 'floor'](this);
});
console.log((-10 / 3).integer()); // -3
```

>JavaScript 缺少一个移除字符串首尾空白的方法。这个小疏忽很容易弥补

```js
String.method('itrim',function(){
	return this.replace(/^\s+|\s+$/g, '');
});
console.log('"' + " neat ".trim() + '"');
```

>我们的 **trim** 方法使用了一个正则表达式。我们将在第7章看到更多关于正则表达式的内容。

>通过给基本类型增加方法，我们可以极大地提高语言的表现力。因为 JavaScript 原型继承的动态本质，新的方法立刻被赋予到所有的对象实例上，哪怕对象实例是在方法被增加之前就创建好了。

>基本类型的原型是公用结构，所以在类库混用时务必小心。一个保险的做法就是只在确定没有该方法时才添加它。

```js
//符合条件时才增加方法。
Function.prototype.method = function(name, func){
	if (!this.prototype[name]) {
		this.prototype[name] = func;
	};
	return this;
};
```

>另一个要注意的就是`for in`语句用在原型上时表现很糟糕。我们在第3章已经看到了几个减轻这个问题的影响的办法:我们可以使用`hasOwnProperty`方法筛选出继承而来的属性，或者我们可以查找特定的类型。


###递归（Recursion）

>递归函数就是会直接或间接地调用自身的一种函数。递归是一种强大的编程技术，它把一个问题分解为一组相似的子问题，每一个都用一个 **寻常解** 解决。一般来说，一个递归函数调用自身去解决它的子问题。

- **`寻常解`**：原文中使用了“trivial solution"，该词组为数学中的术语，可翻译为寻常解或明显解。作者用在此处意在说明递归用一般的方式去解决每个子问题。具体可参考 [http://zh.wikipedia.org/wiki/格林函数](http://zh.wikipedia.org/wiki/格林函数)

>**汉诺塔** 是一个著名的益智游戏。塔上有3根柱子和一套直径各不相同的空心圆盘。开始时源柱子上的所有圆盘都按照从小到大的顺序堆叠。目标是通过每次移动一个圆盘到另一根柱子，最终把一堆圆盘移动到目标柱子上，过程中不允许把较大的圆盘放置在较小的圆盘之上。这个问题有一个寻常解:

- **`汉诺塔`**：汉诺塔是印度一个古老传说，也是程序设计中的经典递归问题。详细的说明请参见  http://baike.baidu.com/view/191666.htm

```js
// 定义walk_the_DOM函数，它从某个指定的节点开始，按 HTML 源码中的顺序
// 访问该树的每个节点。
// 它会调用一个函数，并依次传递每个节点给它。walk_the_DOM 调用自身去处理
// 每一个子节点。

var walk_the_DOm = function walk(node, func){
	func(node);
	node = node.firstChild;
	while (node){
		walk(node, func);
		node = node.nextSibling;
	}
}
```


```js
// 定义 getElementsByAttribute 函数。它以一个属性名称字符串
// 和一个可选的匹配值作为参数。
// 它调用walk_the_ DOM，传递一个用来查找节点属性名的函数作为参数。
// 匹配的节点会累加到一个结果数组中。

var getElementsByAttribute = function(att, value){
	var result = [];
	walk_the_DOm(document.body, function(node){
		var actual = node.nodeType === 1 && ndoe.getAttribute(attr);
		if(typeof actual === 'string' && (actual === value || typeof value !== 'string')){
			results.push(node);
		}
	});
	return results;
}
```

>一些语言提供了 **尾递归** 优化。这意味着如果一个函数返回自身递归调用的结果，那么调用的过程会被替换为一个循环，它可以显著提高速度。遗憾的是，JavaScript 当前并没有提供尾递归优化。深度递归的函数可能会因为堆栈溢出而运行失败。

- **`尾递归`**：尾递归 ( tail recursion 或 tail-end recursion ) 是一种在函数的最后执行递归调用语句的特殊形式的递归。参见 http://en.wikipedia.org/wiki/Tail_recursion

```js
// 构建一个带尾递归的函数。因为它会返回自身调用的结果，所以它是尾递归。
//  JavaScript 当前没有对这种形式的递归做出优化。

var factorial = function factorial(i,a) {
	a = a || 1;
	if(i < 2){
		return a;
	}
	return factorial(i-1,a*i);
}
console.log(factorial(4)); // 24
```

###作用域（Scope）

>在编程语言中，作用域控制着变量与参数的可见性及生命周期。对程序员来说这是一项重要的服务，因为它减少了名称冲突，并且提供了自动内存管理。

```js
var foo=function(){
    var a = 3, b = 5;
	var bar=function(){
	    var b = 7, c = 11;
		// 此时，a 为 3,  b 为 7,  c为 11
		a += b + c;
		//此时，a 为 21,   b 为 7, c 为 11
	};
	//此时，a 为 3,   b 为 5，而 c 还没有定义。
	bar();
	//此时，a为21,  b为5
};
```

>大多数类C语言语法的语言都拥有块级作用域。在一个代码块中 ( 括在一对花括号中的一组语句 ) 定义的所有变量在代码块的外部是不可见的。定义在代码块中的变量在代码块执行结束后会被释放掉。这是件好事。

>糟糕的是，尽管 JavaScript 的代码块语法貌似支持块级作用域，但实际上 JavaScript 并不支持。这个混淆之处可能成为错误之源。

>JavaScript 确实有函数作用域。那意味着定义在函数中的参数和变量在函数外部是不可见的，而在一个函数内部任何位置定义的变量，在该函数内部任何地方都可见。

>很多现代语言都推荐尽可能延迟声明变量。而用在 JavaScript 上的话却会成为糟糕的建议，因为它缺少块级作用域。所以，最好的做法是在函数体的顶部声明函数中可能用到的所有变量。

###闭包（Cuosure）

>作用域的好处是内部函数可以访问定义它们的外部函数的参数和妾量 （ 除了`this` 和 `arguments`）。这太美妙了。

>我们的`getElementsByAttribute`函数可以工作，是因为它声明了一个 results 变量，而传递给	**walk the_ DOM** 的内部函数也可以访问  results  变量。

>一个更有趣的情形是内部函数拥有比它的外部函数更长的生命周期。

>之前，我们构造了一个 **myObject** 对象，它拥有一个`value`属性和一个`increment`方法。假定我们希望保护该值不会被非法更改。

>和以对象字面量形式去初始化 **myObject** 不同，我们通过调用一个函数的形式去初始化 **myObject**，该函数会返回一个对象字面量。函数里定义了一个`value`变量。该变量对`increment`和`getValue`方法总是可用的，但函数的作用域使得它对其他的程序来说是不可见的。

```js
var myObject = (function(){
	var value = 0;
	return {
		increment : function(inc){
			value += typeof inc === 'number' ? inc : 1;
		},
		getValue : function(){
			return value;
		}
	};
}());
```

>我们并没有把一个函数赋值给 my0bject。我们是把调用该函数后返回的结果赋值给它。注意最后一行的()。该函数返回一个包含两个方法的对象，并且这些方法继续享有访问 value 变量的特权。

>本章之前的Quo 构造器产生一个带有 status 属性和 get_ status 方法的对象。但那看起来并不是十分有趣。为什么要用一个 getter 方法去访问你本可以直接访问到的属性呢? 如果status 是私有属性，它才是更有意义的。所以，让我们定义另一种形式的quo 函数来做此事:

```js
// 创建一个名为 quo 的构造函数。
// 它构造出带有get_status 方法和 status 私有属性的一个对象。

var quo = function (status){
	return{
		get_status : function(){
			return status;
		}
	};
};

// 构造一个 quo 实例
var myQuo = quo("amazed");
console.log(myQuo.get_status());
```

>这个 `quo` 函数被设计成无须在前面加上 `new` 来使用，所以名字也没有首字母大写。当我们调用qu。时，它返回包含 `get_ status`方法的一个新对象。该对象的一个引用保存在`myQuo`中。即使`quo`已经返回了，但`get_ status`方法仍然享有访问qu。对象的`status`属性的特权。`get_status`方法并不是访问该参数的一个副本，它访问的就是该参数本身。这是可能的‘因为该函数可以访问它被创建时所处的上下文环境。这被称为闭包。

>让我们来看一个更有用的例子:

```js
//定义一个函数，它设显一个DOM节，点为黄色，然后把它渐变为白色。

var fade = fuction(node){
	var level = 1;
	var step = function(){
		var hex = level.toString(16);
		node.style.backgroundColor = '#FFFF' + hex + hex;
		if(level < 15){
			setTimeout(step, 100);
		}
	};
	setTimeout(step, 100);
};
fade(document.body);
```

>我们调用 fade，把 document.body 作为参数传递给它（HTML `<body>`标签所创建的节点），fade函数设置 level 为 1。它定义了一个step函数，接着调用 setTimeout，并传递step函数和一个时间（100毫秒）给它。然后它返回，fade 函数结束。

>在大约十分之一秒后，step 函数被调用。它把 fade 函数的 level 变量转化为16位字符。接着，它修改fade函数得到的节点的背景颜色。然后查看 fade 函数的 level 变量。如果背景色尚未变成白色，那么它增大 fade 函数的工 level 变量，接着用。 setTimeout 预定让它自己再次运行。

>step 函数很快再次被调用。但这次，fade 函数的 level 变量值变成 2。 fade 函数在之前已经返回了，但只要 fade 的内部函数需要，它的变量就会持续保留。

>为了避免下面的问题，理解内部函数能访问外部函数的实际变量而无须复制是很重要的:

```js
// 糟糕的例子
// 构造一个函数，用错误的方式给一个数组中的节点设五事件处理程序。
// 当点击一个节点时，按照预期，应该弹出一个时话框显示节点的序号，
// 但它总是会显示节点的数目。

var add_the_handlers = function(ndoes){
	var i;
	for(i = 0; i < ndoes.length; i += 1){
		console.log(i);
	}
}

// 结束糟糕的例子
```

>**add_the_handlers** 函数的本意是想传递给每个事件处理器一个唯一值`（i）`。但它未能达到目的，因为事件处理器函数绑定了变量 `i` 本身，而不是函数在构造时的变量 `i` 的值。

```js
// 改良后的例子
// 构造一个函数，用正确的方式给一个数组中的节点设里事件处理程序。
// 点击一个节点，将会弹出一个对话框显示节点的序号。

var add_the_handlers = function(nodes){
	var helper = function(i){
		return function(e){
			console.log(i);
		};
	};
	var i;
	for(i = 0; i < nodes.length; i += 1){
		nodes[i].onclick = helper(i);
	}
};
```

>避免在循环中创建函数，它可能只会带来无谓的计算，还会引起混淆，正如上面那个糟糕的例子。我们可以先在循环之外创建一个辅助函数，让这个辅助函数再返回一个绑定了当前`i`值的函数，这样就不会导致混淆了。

###回调（Callbacks）

>函数使得对不连续事件的处理变得更容易。例如，假定有这么一个序列，由用户交互行为触发，向服务器发送请求，最终显示服务器的响应。最自然的写法可能会是这样的:
```js
request  = prepare_ the_ request();
response = send_request_synchronously(request);
display(response);
```
>这种方式的问题在于，网络上的同步请求会导致客户端进入假死状态。如果网络传输或服务器很慢，响应会慢到让人不可接受。

>更好的方式是发起异步请求，提供一个当服务器的响应到达时随即触发的回调函数。异步函数立即返回，这样客户端就不会被阻塞。


```js
request = prepare_the_request();
send_request_asynchronously(request, function (response){
        display(response);
});
```
>我们传递一个函数作为参数给它就会被调用。**send_request_asynchronously** 函数，一旦接收到响应

###模块（Module）

>我们可以使用函数和闭包来构造模块。模块是一个提供接口却隐藏状态与实现的函数或对象。通过使用函数产生模块，我们几乎可以完全摒弃全局变量的使用，从而缓解这个 **JavaScript** 的最为糟糕的特性之一所带来的影响。

>举例来说，假定我们想要给 `String` 增加一个 **deentityify** 方法。它的任务是寻找字符串中的 **HTML** 字符实体并把它们替换为对应的字符。这就需要在一个对象中保存字符实体的名字和它们对应的字符。但我们该在哪里保存这个对象呢?我们可以把它放到一个全局变量中，但全局变量是魔鬼。我们可以把它定义在该函数的内部，但是那会带来运行时的损耗，因为每次执行该函数的时候该字面量都会被求值一次。理想的方式是把它放入一个闭包，而且也许还能提供一个增加更多字符实体的扩展方法:

`error！！！`
```js
String.method('deentityify',function(){

	//字符实体表。它映射字符实体的名字到衬应的字符。
	
	var entity = {
		quot : '"',
		lt : '<',
		gt : '>'
	};

	//返回 deentityify 方法。

	return function(){

		//这才是 deentityify 方法。它调用字符串的 replace方法，
		//查找 'g' 开头和 ';' 结束的子字符串。如果这些字符可以在字符实体表中找到，
		//那么就将该字符实体替换为映射表中的值。它用到了一个正则表达式（参见第7章）。

		return this.replace(/&([^&;]+);/g,
			function (a,b){
				var r = entity[b];
				return typeof r === 'string' ? r : a;
			}
		);
	};
}());
```

>请注意最后一行。我们用`()`运算法立刻调用我们刚刚构造出来的函数。这个调用所创建并返回的函数才是 **deentityify** 方法。

```js
console.log('&lt;&quot;&gt;',deentityify()); // <''>
```

>模块模式利用了函数作用域和闭包来创建被绑定对象与私有成员的关联，在这个例子中，只有 **deentityify** 方法有权访问字符实体表这个数据对象。

>模块模式的一般形式是：一个定义了私有变量和函数的函数;利用闭包创建可以访问私有变量和函数的特权函数;最后返回这个特权函数，或者把它们保存到一个可访问到的地方。

>使用模块模式就可以摒弃全局变量的使用。它促进了信息隐藏和其他优秀的设计实践。对于应用程序的封装，或者构造其他**单例对象**，模块模式非常有效。

>模块模式也可以用来产生安全的对象。假定我们想要构造一个用来产生序列号的对象:

- **`单例对象`**：模块模式通常结合单例模式（Singleton Pattern）使用。**JavaScript** 的单例就是用对象字面童表示法创建的对象，对象的属性值可以是数值或函数，并且属性值在该对象的生命周期中不会发生变化。它通常作为工具为程序其他部分提供功能支持。单例模式的更多内容请参见 [http://zh.wikipedia.org/wiki/单例模式](http://zh.wikipedia.org/wiki/单例模式)。

```js
var serial_maker = function(){

	//	返回一个用来产生唯一字符串的时象。
	//	唯一字符串由两部分组成:前缓 + 序列号。
	//	该对象包含一个设置前缀的方法，一个设笠序列号的方法
	//	和一个产生唯一字符串的 gensym 方法。

	var prefix = '';
	var seq = 0;
	return {
		set_prefix : function(p){
			prefix = String(p);
		},

		set_seq : function(s){
			seq = s;
		},

		gensym : function(){
			var result = prefix + seq;
			seq += 1;
			return result;
		}
	};
};

var seqer = serial_maker();
seqer.set_prefix('Q');
seqer.set_seq(1000);
var unique1 = seqer.gensym(); // unique 是 "Q1001"
var unique2 = seqer.gensym(); // unique 是 "Q1000"
console.log(unique);
```

> **seqer** 包含的方法都没有用到 `this`或 `that`，因此没有办法损害 **seqer** 除非调用对应的方法，否则没法改变`prefix`或`seq`的值。**seqer** 对象是可变的，所以它的方法可能会被替换掉，但替换后的方法依然不能访问私有成员。**seqer** 就是一组函数的集合，而且那些函数被授予特权，拥有使用或修改私有状态的能力。

>如果我们把 **seqer.gensym** 作为一个值传递给第三方函数，那个函数能用它产生唯一字符串，但却不能通过它来改变`prefix`或`seq`的值。

###级联（Cascade）

>有一些方法没有返回值。例如，一些设置或修改对象的某个状态却不返回任何值的方法就是典型的例子。如果我们让这些方法返回`this`而不是`undefined`，就可以启用级联。在一个级联中，我们可以在单独一条语句中依次调用同一个对象的很多方法。一个启用级联的 **Ajax** 类库可能允许我们以这样的形式去编码:


```js
getElement('myBoxDiv')
    .move(350，150)
    .width(100)
    .height (100)
    .color('red')
    .border('lOpx outset')
    .padding('4px')
    .appendText("Please stand by")
    .on('mousedown',function (m){
        this.startDrag(m,,this.getNinth(m));
     })
    .on('mousemove','drag')
    .on('mouseup','stopDrag')
    .later(2000,function(){
            this
              .color('yellow')
              .setHTML("What hath God wraught?")
              .slide (400, 40, 200, 200);
	})
	.tip('This box is resizeable');
```

>在这个例子中，**getElement** 函数产生一个对应于`id="myBoxDiv"`的 **DOM** 元素且给其注人了其他功能的对象。该方法允许我们移动元素，修改它的尺寸和样式，并添加行为。这些方法每一个都返回该对象，所以每次调用返回的结果可以被下一次调用所用。级联技术可以产生出极富表现力的接口。它也能给那波构造 **“全能”** 接口的热潮降降温，一个接口没必要一次做太多事情。

###柯里化（Curry）
- **`柯里化`**：柯里化，也常译为 “局部套用”，是把多参数函数转换为一系列单参数函数并进行调用’的套的技术。这项技术以数学家  Haskell Curry （Haskell 编程语言也是以该数学家命名）更多内容请参考 [http://zh.wikipedia.org/wiki/柯里化](http://zh.wikipedia.org/wiki/柯里化)。本书的上一版中译为 “套用”，再版采用开发人员更为认可的 “柯里化” 的翻译。

>函数也是值，从而我们可以用有趣的方式去操作函数值。柯里化允许我们把函数与传递给它的参数相结合，产生出一个新的函数。

```js
var add1 = add.curry(1);
console.log(add1(6)); // 7
```
>**add1** 是把 1 传递给 **add** 函数的 `curry` 方法后创建的一个函数。**add1** 函数把传递给它的参数的值加 1。**JavaScript** 并没有`curry`方法，但我们可以给 **Function.protoype** 扩展此功能:

```js
Function.method('curry',function(){
	var args = arguments,that = this;
	return function(){
		return that.apply(null,args.concat(arguments));
	};
});  //有些事好像看起来不太对头 .....
```

>`curry`方法通过创建一个保存着原始函数和要被套用的参数的闭包来工作。它返回另一个函数，该函数被调用时，会返回调用原始函数的结果，并传递调用 `curry` 时的参数加上当前调用的参数。它使用 **Array** 的`concat`方法连接两个参数数组。

>糟糕的是，就像我们先前看到的那样，`arguments`数组并非一个真正的数组，所以它并没有`concat`方法。要避开这个问题，们必须在两个`arguments`数组上都应用数组的`slice`方法。这样产生出拥有`concat`方法的常规数组。

```js
Function.method('curry',function(){
	var slice = Array.prototype.slice,
		args = slice.apply(arguments),
		that = this;
	return function(){
		return that.apply(null,args.concat(slice.apply(arguments)));
	};
});
```

---

###记忆（Memoization）
>函数可以将先前操作的结果记录在某个对象里，从而避免无谓的重复运算。这种优化被称为 **记忆** (memoization)。 **JavaScrit** 的对象和数组要实现这种优化是非常方便的。

- **`记忆`**：在计算机领域，记忆 ( memoization ) 是主要用于加速程序计算的一种优化技术，它使得函数避免重复演算之前已被处理的愉入，而返回已缓存的结果 —— 摘自 [http://en.wikipedia.org/wiki/Memoization](http://en.wikipedia.org/wiki/Memoization)

>比如说，我们想要一个递归函数来计算 **Fibonacci**  **数列**。一个 **Fibonacci** 数字是之前两个 **Fibonacci** 数字之和。最前面的两个数字是 0 和 1 。

- **`Fibonacci`**：Fibonacci 数列，中文名为斐波那契数列。它的特点是，前面相邻两项之和等于后一项的值。更多请参考 [http://zh.wikipedia.org/wiki/斐波那契数列](http://zh.wikipedia.org/wiki/斐波那契数列)。

```js
var j = 0;
var fibonacci = function(n){
	j += 1;
	// console.log(n);
	return n < 2 ? n : fibonacci(n-1) + fibonacci(n-2);
};

for(var i = 0; i <= 10; i += 1){
	console.log('//' + i + ': '+fibonacci(i));
}

//0: 0
//1: 1
//2: 1
//3: 2
//4: 3
//5: 5
//6: 8
//7: 13
//8: 21
//9: 34
//10: 55

console.log(j); // 453
```

>这样是可以工作的，但它做了很多无谓的工作。**fibonacci** 函数被调用了453次。我们调用了 11 次，而它自身调用了442次去计可能已被刚计算过的值。如果我们让该函数具备记忆功能，就可以显著地减少运算量。

>我们在一个名为 `memo` 的数组里保存我们的存储结果，存储结果可以隐藏在闭包中。当函数被调用时，这个函数首先检查结果是否已存在，如果已经存在，就立即返回这个结果。

```js
var fibonacci = function(){
	var memo = [0,1];
	var fib  =function(n){
		var result = memo[n];
		if(typeof result !== 'number'){
			result = fib(n-1) + fib(n-2);
			memo[n] = result;
		}
		return result;
	};
	return fib;
}();

for(var i = 0; i <= 10; i += 1){
	console.log('//' + i + ': '+fibonacci(i));
}

//0: 0
//1: 1
//2: 1
//3: 2
//4: 3
//5: 5
//6: 8
//7: 13
//8: 21
//9: 34
//10: 55
```
>这个函数返回同样的结果，但它只被调用了 29 次。我们调用了它 11 次，它调用了自己 18 次去取得之前存储的结果。


>我们可以把这种技术推而广之，编写一个函数来帮助我们构造带记忆功能的函数。**memoizer** 函数取得一个初始的 `memo`数组和 **formula** 函数。它返回一个管理 `meno`存储和在需要时调用 **formula** 函数的 **recur** 函数。我们把这个**recur** 函数和它的参数传递给 **formula** 函数:

```js
var memoizer = function(memo,formula){
	var recur = function(n){
		var result = memo[n];
		if (typeof result !== 'number') {
			result = formula(recur,n);
			memo[n] = result;
		}
		return result;
	};
	console.log(recur);
	return recur;
};
```
>现在，我们可以使用 **memoizer** 函数来定义 **fibonacci** 函数，提供其初始的 `memo` 数组和 **formula** 函数：

```js
var fibonacci = memoizer([0,1],function(recur,n){
	return recur(n-1) + recur(n-2);
})
console.log(fibonacci());
```

>通过设计这种产生另一个函数的函数，极大地减少了我们的工作量。例如，要产生一个可记忆的阶乘函数，我们只需提供基本的阶乘公式即可:

```js
var factorial = memoizer([1,1].function(recur,n){
	return n * recur(n-1);
});
```
---
##第五章 —— 继承（Inheritance）

>在大多数编程语言中，继承都是一个重要的主题。

>在那些基于类的语言(比如Java)中，继承(inheritance或extends)提供了两个有用的服务。首先，它是代码重用的一种形式。如果一个新的类与一个已存在的类大部分相似，那么你只需具体说明其不同点即可。代码重用的模式极为重要，因为它们可以显著地减少软件开发的成本。类继承的另一个好处是引入了一套类型系统的规范。由于程序员无需编写显式类型转换的代码，他们的工作量将大大减轻，这是一件很好的事情，因为类型转换会丧失类型系统在安全上的优势。

>JavaScript是一门弱类型语言，从不需要类型转换。对象继承关系变得无关紧要。对于一个对象来说重要的是它能做什么，而不是它从哪里来。

>JavaScript提供了一套更为丰富的代码重用模式。它可以模拟那些基于类的模式，同时它也可以支持其他更具表现力的模式。在JavaScript中可能的继承模式有很多。在本章中，我们将研究几种最为直接的模式。当然还有很多更为复杂的构造模式，但保持简单通常是最好的。

>在基于类的语言中，对象是类的实例，并且类可以从另一个类继承。JavaScript是一门基于原型的语言，这意味着对象直接从其他对象继承。


###伪类（Pseudoclassical）
>**JavaScript** 的原型存在着诸多矛盾。它的某些复杂的语法看起来就像那些基于类的语言，这些语法问题掩盖了它的原型机制。它不直接让对象从其他对象继承，反而插入了一个多余的间接层：**通过构造器函数产生对象**。

>当一个函数对象被创建时，Function构造器产生的函数对象会运行类似这样的一些代码:
```js
this.prototype={constructor:this};
```
>新函数对象被赋予一个`prototype`属性，它的值是一个包含`constructor`属性且属性值为该新函数的对象。这个`prototype`对象是存放继承特征的地方。因为 **JavaScript** 语言没有提供一种方法去确定哪个函数是打算用来做构造器的，所以每个函数都会得到一个`prototype`对象。`construotor`属性没什么用，重要的是`prototype`对象。

>当采用构造器调用模式，即用`new`前缀去调用一个函数时，函数执行的方式会被修改。如果`new`运算符是一个方法而不是一个运算符，它可能会像这样执行:
```js
Function.method('new',function(){

	//创建一个新对象，它继承自构造器函数的原型对象。
	var that = Object.create(this.prototype);

	//调用构造器函数，绑定 -this- 到新对象上。
	var other = this.apply(that,arguments);

	//如果它的返回值不是一个对象，就返回该新对象。
	return (typeof other === 'object' && other) || that;

});
```

>我们可以定义一个构造器并扩充它的原型:
```js
var Mammal = function(name){
	this.name = name;
}

Mammal.prototype.get_name = function(){
	return this.name;
};

Mammal.prototype.says = function(){
	return this.saying || '';
}
```
>现在，我们可以构造一个实例：
```
var myMamml = new Mammal('Herb the Mammal');
var name = myMamml.get_name(); // 'Herb the Mammal'
```
>我们可以构造另一个伪类来继承 **Mammal**，这是通过定义它的 **constructor** 函数并替换它的prototype为一个 **Mammal** 的实例来实现的：
```js
var Cat = function(name){
	this.name = name;
	this.saying = 'meow';
};
// 替换 Cat.protorype 为一个新的 Mammal();
Cat.prototype = new Mammal();
// 扩充新原型对象，增加 purr 和 get_name 方法。
Cat.prototype.purr = function(n){
	var i,s = '';
	for(i = 0; i<n; i+=1){
		if (s) {
			s += '-';
		};
		s += 'r';
	}
	return s;
}
Cat.prototype.get_name = function(){
	return this.says() + ' ' + this.name + ' ' + this.says();
};

var myCat = new Cat('Herietta');
var says = myCat.says(); // 'meow'
var purr = myCat.purr(5); // 'r-r-r-r-r'
var name = myCat.get_name(); // 'meow Henrietta meow'
//console.log(says,purr,name);
```
>通过隐藏那些无谓的`prototype`操作细节，现在它看起来没那么怪异了。但是否真的有所改善呢?我们现在有了行为像“类“构造器函数，但仔细看它们，你会惊讶地发现：没有私有环境，所有的属性都是公开的。无法访问。**super**（父类）的方法。

>更糟糕的是，使用构造器函数存在一个严重的危害。如果你在调用构造器函数时忘记了在前面加上`new`前缀，那么`this`将不会被绑定到一个新对象上。悲剧的是，`this`将被绑定到全局对象上，所以你不但没有扩充新对象，反而破坏了全局变量环境。那真是糟透了。发生那样的情况时，既没有编译时警告，也没有运行时警告。

>这是一个严重的语言设计错误。为了降低这个问题带来的风险，所有的构造器函数都约定命名成首字母大写的形式，并且不以首字母大写的形式拼写任何其他的东西。这样我们至少可以通过目视检查去发现是否缺少了`new`前缀。一个更好的备选方案就是根本不使用`newo`

>“伪类”形式可以给不熟悉 **JavaScript** 的程序员提供便利，但它也隐藏了该语言的真实的本质。借鉴类的表示法可能误导程序员去编写过于深入与复杂的层次结构。许多复杂的类层次结构产生的原因就是静态类型检查的约束。**JavaScript** 完全摆脱了那些约束。在基于类的语言中，类继承是代码重用的唯一方式。而 **JavaScript** 有着更多且更好的选择。

###对象说明符（Object Specifiers）

>有时候，构造器要接受一大串参数。这可能令人烦恼，因为要记住参数的顺序非常困难。在这种情况下，如果我们在编写构造器时让它接受一个简单的对象说明符，可能会更加友好。那个对象包含了将要构建的对象规格说明。所以，与其这样写：
```js
var myObject = maker(f,l,m,c,s);
```
>不如这么写
```js
var myObject = maker({
	first: f,
	middle: m,
	last: l,
	state: s,
	city: c
});
```
>现在多个参数可以按任何顺序排列，如果构造器会聪明地使用默认值，一些参数可以忽略掉，并且代码也更容易阅读。

>当与JSON(参见附录E)一起工作时，这种形式还可以有一个间接的好处。JSON文本只能描述数据，但有时数据表示的是一个对象，把该数据与它的方法关联起来是有用的。如果构造器取得一个对象说明符，就能让它轻松实现，因为我们可以简单地把JSON对象传递给构造器，而它将返回一个构造完全的对象。

###原型（Prototype）


>在一个纯粹的原型模式中，我们会摒弃类，转而专注于对象。基于原型的继承相比基于类的继承在概念上更为简单:一个新对象可以继承一个旧对象的属性。也许你对此感到陌生，但它真的很容易理解。你通过构造一个有用的对象开始，接着可以构造更多和那个对象类似的对象。这就可以完全避免把一个应用拆解成一系列嵌套抽象类的分类过程。

>让我们先用对象字面量去构造一个有用的对象：


```js
var myMamml = {
	name : 'Herb the Mammal',
	get_name : function(){
		return this.name;
	},
	says : function(){
		return this.saying || '';
	}
}
```
>一旦有了一个想要的对象，我们就可以利用第3章中介绍过的`Object.create`方法构造出更多的实例来。接下来我们可以定制新的实例:

```js
var myCat = Object.create(myMamml);
myCat.name = 'Henrietta';
myCat.saying = 'meow';
myCat.purr = function(n){
	var i,s = '';
	for(i = 0; i<n; i+=1){
		if (s) {
			s += '-';
		};
		s += 'r';
	}
	return s;
}
myCat.get_name = function(){
	return this.says + ' ' + this.name + ' ' + this.says;
};
```
>这是一种**“差异化继承(differential inheritance)”**。通过定制一个新的对象，我们指明它与所基于的基本对象的区别。

- **`差异化继承`**：关于差异化继承的更多内容请参见 [https://developer.mozilla.org/zh-CN/docs/Differential_inheritance_in_JavaScript](https://developer.mozilla.org/zh-CN/docs/Differential_inheritance_in_JavaScript)

>有时候，它对某些数据结构继承于其他数据结构的情形非常有用。这里就有一个例子：假定我们要解析一门类似 **JavaScript** 或 **TEX** 那样用一对花括号指示作用域的语言。定义在某个作用域里定义的条目在该作用域之外是不可见的。但在某种意义上，一个内部作用域会继承它的外部作用域。**JavaScript** 在表示这样的关系上做得非常好。当遇到一个左花括号时 `block`函数被调用。`parse`函数将从`scope`中寻找符号，并且当它定义了新的符号时扩充`scope`:

```js
var block = function(){
	//记住当前的作用域。构造一个包含了当前作用域中所有时象的新作用域
	var oldScope = scope;
	scope = Object.create(scope);
	//传递左花括号作为参数调用advanceo
	advance('{');
	//使用新的作用域进行解析。
	parse(scope);
	//传递右花括号作为参数调用advance并抛弃新作用域，恢复原来老的作用域
	advance('}');
	scope = oldScope;
};
```
###函数化（Functional）
>迄今为止，我们所看到的继承模式的一个弱点就是没法保护隐私。对象的所有属性都是可见的。我们无法得到私有变量和私有函数。有时候这样没关系，但有时候却是大麻烦。遇到这些麻烦的时候，一些无知的程序员接受了一种伪装私有**（pretend privacy）**的模式。如果想构造一个私有属性，他们就给它起一个怪模怪样的名字，并且希望其他使用代码的用户假装看不到这些奇怪的成员。幸运的是，我们有一个更好的选择，那就是应用模块模式。

>我们从构造一个生成对象的函数开始。我们以小写字母开头来命名它，因为它并不需要使用`new`前缀。该函数包括 **4** 个步骤。

>1.创建一个新对象。有很多的方式去构造一个对象。它可以构造一个对象字面量，或者它可以和`new`前缀连用去调用一个构造器函数，或者它可以使用 **Object.create** 方法去构造一个已经存在的对象的新实例，或者它可以调用任意一个会返回一个对象的函数。

>2.有选择地定义私有实例变量和方法。这些就是函数中通过`var`语句定义的普通变量。

>3.给这个新对象扩充方法。这些方法拥有特权去访问参数，以及在第2步中通过`var`语句定义的变量。

>4.返回那个新对象。


这里是一个函数化构造器的伪代码模板（文本表示强调）：

```js
var constructor = function(spec,my){
	var that, 其他的私有变量；
	my = my || {};
	
	把共享的变量和函数添加到 my 中
	
	that = 一个新对象

	添加给 that 的特权方法

	return that;
};
```

>`spec`对象包含构造器需要构造一个新实例的所有信息。spe。的内容可能会被复制到私有变量中，或者被其他函数改变，或者方法可以在需要的时候访问spec的信息。(一个简化的方式是替换`spec`为一个单一的值。当构造对象过程中并不需要整个`spec`对象的时候，这是有用的。)

>`my`对象是一个为继承链中的构造器提供秘密共享的容器。my对象可以选择性地使用。如果没有传人一个`my`对象，那么会创建一个`my`对象。

>接下来，声明该对象私有的实例变量和方法。通过简单地声明变量就可以做到。构造器的变量和内部函数变成了该实例的私有成员。内部函数可以访问`spec, my, that`，以及其他私有变量。

>接下来，给`my`对象添加共享的秘密成员。这是通过赋值语句来实现的:
```js
my.member = value;
```
>现在，我们构造了一个新对象并把它赋值给that。有很多方式可以构造一个新对象。我们可以使用对象字面量，可以用`new`运算符调用一个伪类构造器，可以在一个原型对象上使用`object.create`方法，或者可以调用另一个函数化的构造器，传给它一个`spec`对象（可能就是传递给当前构造器的同一个`spec`对象）和`my`对象。`my`对象允许其他的构造器分享我们放到`my`中的资料。其他构造器可能也会把自己可分享的秘密成员放进`my`对象里，以便我们的构造器可以利用它。

>接下来，我们扩充`that`，加入组成该对象接口的特权方法。我们可以分配一个新函数成为`that`的成员方法。或者，更安全地，我们可以先把函数定义为私有方法，然后再把它们分配给`that`:
```js
var methodical = function(){
	···
};
that.methodical = methodical;
```
>分两步去定义`methodical`的好处是，如果其他方法想要调用`methodical`，它们可以直接调用`methodical()`而不是`that.methodical()`。如果该实例被破坏或篡改，甚至`that.methodical`被替换掉了，调用`methodica`工的方法同样会继续工作，因为它们私有的`methodical`不受该实例被修改的影响。

>最后，我们返回`that`

>让我们把这个模式应用到`mammal`例子里。此处不需要`my`，所以我们先抛开它，但会使用一个`spec`对象。

>`name`和`saying`属性现在是完全私有的。只有通过`get_ name`和`says`两个特权方法才可以访问它们。

```js
var mammal = function(spec){
	var that = {};
	that.get_name = function(){
		return spec.name;
	};
	that,says = function(){
		return spec.saying || '';
	};
	return that;
};

var myMamml = mammal({name: 'Herb'});
```

>在伪类模式里，构造器函数`Cat`不得不重复构造器`Mammal`已经完成的工作。在函数化模式中那不再需要了，因为构造器`Cat`将会调用构造器`Mammal`，让`Mammal`去做对象创建中的大部分工作，所以`Cat`只需关注自身的差异即可。
```js
var cat = function(spec){
	spec.saying = spec.saying || 'meow';
	var that = mammal(spec);
	that.purr = function(n){
		var i,s = '';
		for(i=0; i<n; i+=1){
			if(s){
				s+='-';
			}
			s+='r';
		}
		return s;
	};
	that.get_name = function(){
		return that.says() + ' ' + spec.name + ' ' + that.says();
	};
	return that;
};

var myCat = cat({name:'Henrietta'});
```
>函数化模式还给我们提供了一个处理父类方法的方法。我们会构造一个`superior`方法，它取得一个方法名并返回调用那个方法的函数。该函数会调用原来的方法，尽管属性已经变化了。

```js
Object.method('superior',function(name){
	var that = this,
		method = that[name];
	return function(){
		return method.apply(that,arguments);
	};
});
```
>让我们在`coolcat`上试验一下，`coolcat`就像`cat`一样，除了它有一个更酷的调用父类方法的`get_ name`方法。它只需要一点点的准备工作。我们会声明一个`super_ get_ name`变量，并且把调用`superior`方法所返回的结果赋值给它。

```js
var coolcat = function(spec){
	var that = cat(spec),
		super_get_name = that.superior('get_name');
	that.get_name = function(n){
		return 'like' + super_get_name() + 'body';
	};
	return that;
};

var myCoolCat = coolcat({name:'Bix'});
var name = myCoolCat.get_name();
// 'like meow Bix meow bady'
```
>函数化模式有很大的灵活性。它相比伪类模式不仅带来的工作更少，还让我们得到更好的封装和信息隐藏，以及访问父类方法的能力。

>如果对象的所有状态都是私有的，那么该对象就成为一个**“防伪（tamper-proof）”**对象。该对象的属性可以被替换或删除，但该对象的完整性不会受到损害。如果我们用函数化的样式创建一个对象，并且该对象的所有方法都不使用`this`或`that`，那么该对象就是持久性 **（durable）**的。一个持久性对象就是一个简单功能函数的集合。
  
>一个持久性的对象不会被入侵。访问一个持久性的对象时，除非有方法授权，否则攻击者不能访问对象的内部状态。

###部件（Parts）
>我们可以从一套部件中把对象组装出来。例如，我们可以构造一个给任何对象添加简单事件处理特性的函数。它会给对象添加一个`on`方法、一个`fire`方法和一个私有的事件注册表对象:
```js
var eventuality = fuction (that){
	var registry = {};
	that.fire = function(event){

	// 在一个对象上触发一个事件。该事件可以是一个包含事件名称的字符串，
	// 或者是一个拥有包含事件名称的 type属性的对象。
	// 通过‘on’方法注册的事件处理程序中匹配事件名称的函数将被调用。

		var array,
			func,
			handler,
			i,
			type = typeof event === 'string' ? event : event.type;

	//如果这个事件存在一组事件处理程序，那么就遍历它们并按顺序依次执行。

		if (registry.hasOwnProperty(type)) {
			array = registry[type];
			for(i = 0; i<array.length; i+=1){
				handler = array[i];

	// 每个处理程序包含一个方法和一组可选的参数。
	// 如果该方法是一个字符串形式的名字，那么寻找到该函数。

				func = handler.method;
				if (typeof func === 'string') {
					func = this[func];
				};

				func.apply(this,handler.parameters || [event]);
			}
		};
		return this;
	};
	that.on = function(type,method,parameters){

	//注册一个事件。构造一条处理程序条目。将它插入到处理程序数组中，
	//如果这种类型的事件还不存在，就构造一个。

		var handler = {
			method: method,
			parameters :parameters
		};
		if (registry.hasOwnProperty(type)) {
			registry[type].push(handler);
		}else{
			registry[type] == [handler];
		};
		return this;
	};
	return that;
};

```
>我们可以在任何单独的对象上调用`eventuality`，授予它事件处理方法。我们也可以赶在`that`被返回前在一个构造器函数中调用它。

	eventuality(that);

>用这种方式，一个构造器函数可以从一套部件中把对象组装出来。**JavaScript** 的弱类型在此处是一个巨大的优势，因为我们无须花费精力去了解对象在类型系统中的继承关系。相反，我们只需专注于它们的个性特征。

>如果我们想要`eventuality`访问该对象的私有状态，可以把私有成员集`my`传递给它。

##第六章 —— 数组（Arrays）
>数组是一段线性分配的内存，它通过整数计算偏移并访问其中的元素。数组是一种性能出色的数据结构。不幸的是，**JavaScript** 没有像此类数组一样的数据结构。

>作为替代，**JavaScript** 提供了一种拥有一些类数组 **(array-like)** 特性的对象。它把数组的下标转变成字符串，用其作为属性。它明显地比一个真正的数组慢，但它使用起来更方便。它的属性的检索和更新的方式与对象一模一样，只不过多一个可以用整数作为属性名的特性。数组有自己的字面量格式。数组也有一套非常有用的内置方法，我将在第8章描述它们。
###数组字面量（Array Literals）
>数组字面量提供了一种非常方便地创建新数组的表示法。一个数组字面量是在一对方括号中包围零个或多个用逗号分隔的值的表达式。数组字面量允许出现在任何表达式可以出现的地方。数组的第一个值将获得属性名 '0'，第二个值将获得属性名 '1'，依此类推：

```js
var empty = [];
var numbers = [
	'zero','one','two','three','four',
	'five','six','seven','eight','nine'
];

empty[1]; // undefined
numbers[1] // 'one'
empty.length // 0
numbers.length //10;
```
>对象字面量
```js
var numbers_object = {
	'0': 'zero', '1': 'one', '2': 'two',
	'3': 'three', '4': 'four', '5':'five',
	'6': 'six', '7':'seven', '8':'eight',
	'9': 'nine'
};
```
>两者产生的结果相似。`numbers`和`numbers_ object`都是包含 10 个属性的对象，并且那些属性刚好有相同的名字和值。但是它们也有一些显著的不同。`numbers`继承自`Array.prototype`，而`numbers_ object`继承自`Object.prototype`，所以`numbers`继承了大量有用的方法。同时，`numbers`也有一个诡异的`length`属性，而`numbers_ object`则没有。

>在大多数语言中，一个数组的所有元素都要求是相同的类型。**JavaScript** 允许数组包含任意混合类型的值：

###长度（Length）
>每个数组都有一个`length`属性。和大多数其他语言不同，**JavaScript** 数组的`length`是没有上界的。如果你用大于或等于当前`length`的数字作为下标来存储一个元素，那么`length`值会被增大以容纳新元素，不会发生数组越界错误。

>`length`属性的值是这个数组的最大整数属性名加上 1 。它不一定等于数组里的属性的个数：
```js
var myArray = [];
myArray.length //0

myArray[1000000] = true;
myArray.length //1000001
// myArray 只包含一个属性
```
>**[ ]** 后置下标运算符把它所含的表达式转换成一个字符串，如果该表达式有`toString`方法，就使用该方法的值。这个字符串将被用做属性名。如果这个字符串看起来像一个大于等于这个数组当前的`length`且小于 **4294967295** 的正整数，那么这个数组的`length`就会被重新设置为新的下标加 1。

>你可以直接设置`length`的值。设置更大的`length`不会给数组分配更多的空间。而把`length`设小将导致所有下标大于等于新`length`的属性被删除：
```js
numbers .length = 3；
//numbers是['zero','one','two']

//通过把下标指定为一个数组的当前length，可以附加一个新元素到该数组的尾部:
numbers[numbers .length] = 'shi';
//numbers是['zero'，'noe','two','shi']

//有时用 push 方法可以更方便地完成同样的事情:
nunmbers.push('go');
//numbers是['zero','one','twoshi','go']
```
###删除（Delete）
>由于 **JavaScript** 的数组其实就是对象，所以`delete`运算符可以用来从数组中移除元素:
```js
delete numbers[2」;
//numbers是['zero','one','undefined,'shi','go']
```
>不幸的是，那样会在数组中留下一个空洞。这是因为排在被删除元素之后的元素保留着它们最初的属性。而你通常想要的是递减后面每个元素的属性。

>幸运的是，**JavaScript** 数组有一个。`splice`方法。它可以对数组做个手术，删除一些元素并将它们替换为其他的元素。第 1 个参数是数组中的一个序号，第 2 个参数是要删除的元素个数。任何额外的参数会在序号那个点的位置被插入到数组中:
```js
numbers.splice (2，1);
//numbers是['zero','one','shi','go']
```
>值为，'shi'，的属性的键值从 '3' 变到 '2'。因为被删除属性后面的每个属性必须被移除，并且以一个新的键值重新插入，这对于大型数组来说可能会效率不高。

###枚举（Enumeration）
>因为 **JavaScript**  的数组其实就是对象，所以`for in`语句可以用来遍历一个数组的所有属性。遗憾的是，`for in`无法保证属性的顺序，而大多数要遍历数组的场合都期望按照阿拉伯数字顺序来产生元素。此外，可能从原型链中得到意外属性的问题依旧存在。

>幸运的是，常规的`for`语句可以避免这些问题。**JavaScript** 的`for`语句和大多数类 **C（C-like）** 语言相似。它被3个从句控制—第 1 个初始化循环，第 2 个执行条件检测，第 3 个执行增量运算：

```js
var i;
for(i=0; 1<myArray.lenght; i+=1){
	console.log(myArray[i]);
}
```
###容易混淆的地方（Confusion）
>在 **JavaScript** 编程中，一个常见的错误是在必须使用数组时使用了对象，或者在必须使用对象时使用了数组。其实规则很简单:当属性名是小而连续的整数时，你应该使用数组。否则，使用对象。

>**JavaScript** 本身对于数组和对象的区别是混乱的。`typeof`运算符报告数组的类型是 'object'，这没有任何意义。

>**JavaScript** 没有一个好的机制来区别数组和对象。我们可以通过定义自己的`is_array`函数来弥补这个缺陷:
```js
var is_array = function(value){
	return value &&
		typeof value === 'object' &&
		value.constructor === Array;
};
```
>遗憾的是，它在识别从不同的窗口`(window)`或帧`(frame)`里构造的数组时会失败。有一个更好的方式去判断一个对象是否为数组:
```js
var is_array = function(value){
	return Object.prototype.toString.apply(value) === '[object Array]';
}
```
###方法（Methods）

>**JavaScript** 提供了一套数组可用的方法。这些方法是被储存在`Array.prototype`中的函数。在第 3 章里，我们看到`Object.prototype`是可以被扩充的。同样，`Array.prototype`也可以被扩充。

>举例来说，假设我们想要给`array`增加一个方法，它允许我们对数组进行计算:
```js
Array.method('reduce',function(f,value){
	var i;
	for(i=0; i<this.length; i+=1){
		value = f(this[i],value);
	}
	return value;
});
```
>通过给`Array.prototype`扩充一个函数，每个数组都继承了这个方法。在这个例子里，我们定义了一个`reduce`方法，它接受一个函数和一个初始值作为参数。它遍历这个数组，以当前元素和该初始值为参数调用这个函数，并且计算出一个新值。当完成时，它返回这个值。如果我们传入一个把两个数字相加的函数，它会计算出相加之和。如果我们传入把两个数字相乘的函数，它会计算两者的乘积：

```js
// 创建一个数字数组
var data = [4,8,15,16,23,42];

// 定义两个简单的函数，一个是把两个数字相加，另一个是把两个数字相乘。

var add = function(a,b){
	return a + b;
}

var mult = function(a,b){
	return a * b;
}

// 调用 data 的 reduce 方法，传入 add 函数

var sum = data.reduce(add,0);  //sum is 108

// 再次调用 reduce 方法，这次传入 mult 函数

var product = data.reduce(mult,1);

// product 是 7418880

//因为数组其实就是对象，所以我们可以直接给一个单独的数组添加方法:
// 给 data 数组添加一个 total 方法

data.total = function(){
	return this.reduce(add,0);
};
total = data.total(); //total 是 108

console.log(sum,product,total);
```
>因为字符串，`total` 不是整数，所以给数组增加一个 `total`属性不会改变它的`length`当属性名是整数时，数组才是最有用的，但它们依旧是对象，并且对象可以接受任何字符串作为属性名。

>来自第3章的`object.create`方法用在数组是没有意义的，因为它产生一个对象，而不是一个数组。产生的对象将继承这个数组的值和方法，但它没有那个特殊的`length`属性。

###指定初始值（Dimensions）
>**JavaScript**的数组通常不会预置值。如果你用`[]`得到一个新数组，它将是空的。如果你访问一个不存在的元素，得到的值则是`undefined`。如果你知道这个问题，或者你在尝试获取每个元素之前都很有预见性地设置它的值，那就万事大吉了。但是，如果你实现的算法是假设每个元素都从一个已知的值开始（例如 0），那么你必须自己准备好这个数组。**JavaScript** 应该提供一些类似`Array.dim`这样的方法来做这件事情，但我们可以很容易纠正这个疏忽：

```js
Array.dim = function(dimension,initial){
	var a = [],i;
	for(i=0; i<dimension; i+=1){
		a[i] = initial;
	}
	return a;
}
// 创建一个包含 10 个 0 的数组
```
>**JavaScript** 没有多维数组，但就像大多数类 C 语言一样，它支持元素为数组的数组:
```js
var matrix = [
	[0,1,2],
	[3,4,5],
	[6,7,8]
]
matrix[2][1]; // 7
```
> 为了创建一个二维数组或者说数组的数组，你必须自己去创建那个第二维的数组:

```js
for(i=0; i<n; i += 1){
	my_array = [];
}
// 注意：Array.dim(n,[])在这里不能工作。
// 如果使用它，每个元素都指向同一个数组的引用，那后果不堪设想。
```
>一个空的矩阵的每个单元会拥有一个初始值`undefined`。如果你希望它们有不同的初始值，你必须明确地设置它们。同样地，**JavaScript** 应该对矩阵提供更好的支持。好在我们也可以补上它:

```javascript
/*
	Array
 */
Array.matrix = function(m,n,initial) {
	var a,i,j,mat = [];
	for (i = 0; i<m;i+=1){
		a = [];
		for(j = 0;j<n;j+=1){
			a[j] = initial;
		};
		mat[i] = a;
	};
	return mat;
}

var myMatrix = Array.matrix(4,4,0);
// 构造一个 0 填成的 4x4的矩阵
console.log(myMatrix);  //[ [ 0, 0, 0, 0 ], [ 0, 0, 0, 0 ], [ 0, 0, 0, 0 ], [ 0, 0, 0, 0 ] ]
console.log(myMatrix[3][3]); // 0


// 用来构造一个单位矩阵的方法
Array.identity = function(n){
	var i,mat = Array.matrix(n,n,0);
	for(i = 0;i<n;i+=1){
		mat[i][i] = 1;
	}
	return mat;
};

var myMatrix = Array.identity(4);
console.log(myMatrix);	//[ [ 1, 0, 0, 0 ], [ 0, 1, 0, 0 ], [ 0, 0, 1, 0 ], [ 0, 0, 0, 1 ] ]
console.log(myMatrix[3][3]);	//1
```


##第七章 —— 正则表达式（Regular Expression）

>更多请点击下面的链接
> - [js 正则表达式](http://www.w3school.com.cn/js/js_obj_regexp.asp)
> - [正则表达式30分钟入门教程](http://www.cnblogs.com/deerchao/archive/2006/08/24/zhengzhe30fengzhongjiaocheng.html)

##第八章 —— 方法（Methods）

>更多教程与方法请点击下面的链接
>-  [http://www.w3school.com.cn/js/index.asp](http://www.w3school.com.cn/js/index.asp)

##第九章 —— 代码风格（Style）
##第十章 —— 优美的特性（Beautiful Feature）
##附录 A 毒瘤（Awful Parts）
###全局变量（Gobal Variables）
>在 **JavaScript** 所有的糟糕特性之中，最为糟糕的一个就是它对全局变量的依赖。全局变量就是在所有作用域中都可见的变量。全局变量在微型程序中可能会带来方便，但随着程序变得越来越大，它们很快变得难以管理。因为一个全局变量可以被程序的任何部分在任意时间修改，它们使得程序的行为变得极度复杂。在程序中使用全局变量降低了程序的可靠性。

>全局变量使得在同一个程序中运行独立的子程序变得更难。如果某些全局变量的名称碰巧和子程序中的变量名称相同，那么它们将会相互冲突，可能导致程序无法运行，而且通常难以调试。

>许多编程语言都有全局变量。例如，**Java** 中的`public static`成员属性就是全局变量。**JavaScript** 的问题不仅在于它允许使用全局变量，而且在于它依赖全局变量。**JavaScript** 没有链接器`(linker)`，所有的编译单元都载人一个公共全局对象中。

>共有3种方式定义全局变量。第1种是在任何函数之外放置一个`var`语句:
```js
var foo=value
```
>第 2 种是直接给全局对象添加一个属性。全局对象是所有全局变量的容器。在 **Web** 浏览器里，全局对象名为`window`：
```js
window.foo=value
```
>第 3 种是直接使用未经声明的变量，这被称为隐式的全局变量:
```js
foo=value;
```
>这种方式本来是为方便初学者，有意让变量在使用前无须声明。遗憾的是，忘记声明变量成了一个非常普遍的错误。 **JavaScript** 的策略是让那些忘记预先声明的变量成为全局变量，这导致查找`bug`非常困难。

###作用域（Scope）
>**JavaScript** 的语法来源于C。在所有其他类似 **C** 语言风格的语言里，一个代码块（括在一对花括号中的一组语句）会创造一个作用域。代码块中声明的变量在其外部是不可见的。**JsvaScript** 采用了这样的块语法，却没有提供块级作用域:代码块中声明的变量在包含此代码块的函数的任何位置都是可见的。这让有其他语言编码经验的程序员们大为意外。

>在大多数语言中，一般来说，声明变量的最好的地方是在第一次用到它的地方。但这种做法在**JavaScript**里反而是一个坏习惯，因为它没有块级作用域。更好的方式是在每个函数的开头部分声明所有变量。
###自动插入分号（Semicolon Insertion）

>**JavaScript** 有一个自动修复机制，它试图通过自动插入分号来修正有缺损的程序。但是，千万不要指望它，它可能会掩盖更为严重的错误。有时它会不合时宜地插入分号。请考虑在retu二语句中自动插入分号导致的后果。如果一个`return`语句返回一个值，这个值表达式的开始部分必须和`return`位于同一行:
```js
return
{
status:true
}
```
>这看起来是要返回一个包含`status` 成员元素的对象。遗憾的是，自动插入分号让它变成了返回`undefined`。自动插入分号导致程序被误解，却没有任何警告提醒。如果把{放在上一行的尾部而不是下一行的头部就可以避免该问题:
```js
return{
      status:true
};
```
###保留字（Reserved Words）
>更多请点下面的链接
>- http://www.w3school.com.cn/js/index.asp

###Unicode
>更多请点下面的链接
>- http://www.w3school.com.cn/js/index.asp

###Typeof

>`typeof`运算符返回一个用于识别其运算数类型的字符串。所以:

	typeof 98

>返回’number'。遗憾的是:

	typeof null

>返回`'object'`，而不是 `'null'`。这简直太糟糕了。其实，有更简单也更好的检测`null`的方式:

	my_value === null

>一个更大的问题是检测对象的值。`typeof`不能辨别出`null`与对象，但你可以像下面这样做，因为`null`值为假，而所有对象值为真:

    if (my_value && typeof my_value === 'object'){
          // my_value 是一个对象或数组!
      }
   
>相关的内容，请参阅后面的小节 **'NaN'** 和 **'伪数组'**。在对正则表达式的类型识别上，各种 **JavaScript** 的实现不太一致。对于下面的代码:

	typeof /a/

>一些实现会返回`'object'`，而其他的返回`'function'`。如果返回`'regexp'`，可能会更有用些，但标准不允许那么做。

###parsetInt

>`parseInt`是一个把字符串转换为整数的函数。它在遇到非数字时会停止解析，所以`parseInt("16")`与`parseInt("16 tons")`产生相同的结果。如果该函数会提醒我们出现了额外文本就好了，但它不会那么做。如果该字符串第 1 个字符是。那么该字符串会基于八进制而不是十进制来求值。在八进制中，8 和 9 不是数字，所以`parseInt("08")`和`parseInt("09")`都产生 0 作为结果。这个错误会导致程序解析日期和时间时出现问题。幸运的是，`parseInt`可以接受一个基数作为参数，如此一来`parseInt("08"，10)`结果为 8 。我建议你总是加上这个基数参数。

###+
>`+` 运算符可以用于加法运算或字符串连接。它究竟会如何执行取决于其参数的类型。如果其中一个运算数是一个空字符串，它会把另一个运算数转换成字符串并返回。如果两个运算数都是数字，它返回两者之和。否则，它把两个运算数都转换为字符串并连接起来。这个复杂的行为是`bug`的常见来源。如果你打算用 `+` 去做加法运算，请确保两个运算数都是整数。

###浮点数（Floating Point）
>二进制的浮点数不能正确地处理十进制的小数，因此`0.1+0.2`不等于`0.3`。这是 **JavaScript** 中最经常被报告的`bug`，并且它是遵循二进制浮点数算术标准（ IEEE 754）而有意导致的结果。这个标准对很多应用都是适合的，但它违背了大多数你在中学所学过的关于数字的知识。幸运的是，浮点数中的整数运算是精确的，所以小数表现出来的错误可以通过指定精度来避免。

>举例来说，美元可以通过乘以 100 而全部转成美分，然后就可以准确地将美分相加。它们的和可以再除以 100 转换回美元。当人们计算货币时当然会期望得到精确的结果。

###NaN

>`NaN`是 **IEEE 754** 中定义的一个特殊的数量值。它表示的不是一个数字，尽管下面的表达式返回的是`true`;

- `IEEE 754`是最广泛使用的浮点数运算标准，为许多 **CPU** 与浮点运算器所采用。更多详细内客请参见 [http://zh.wikipedia.org/wiki/IEEE_754](http://zh.wikipedia.org/wiki/IEEE_754)

```js
typeof NaN === 'number'//true
```
>该值可能会在试图把非数字形式的字符串转换为数字时产生。例如:
```js
	+'0' 	//0
	+'oops' 	//NaN
```
>如果`NaN`是数学运算中的一个运算数，那么结果就是`NaN`。所以，如果你有一个公式链产生出`NaN`的结果，那肯定要么其中一个输入项是`NaN`，要么在某个地方产生了`NaN`。

>你可以对`NaN`进行检测。正如我们之前所见，`typeof`不能辨别数字和`NaN`，而且`NaN`也不等同于它自己。所以，下面的代码结果令人惊讶:
```js
	NaN === NaN	// false
	NaN! == NaN // true
```
>**JavaScript** 提供了一个`isNaN`函数，可以辨别数字与`NaN`
```js
isNaN(NaN) 		// true
isNaN(0) 		// false
isNaN('oops') 	// true
isNaN('0') 		// false
```
>判断一个值是否可用做数字的最佳方法是使用`isFinite`函数，因为它会筛除掉`NaN`和`Infinity`。遗憾的是，`isFinite`会试图把它的运算数转换为一个数字，所以，如果值事实上不是一个数字，它就不是一个好的测试。你可以这样定义自己的`isNumber`函数:
```js
var isNumber = function isNumber(value){
	return typeof value === 'number' && isFinite(value);
} 
```

###伪数组（Phony Arrays）
>**JavaScript** 没有真正的数组。这也不全是坏事。**JavaScript** 的数组确实非常容易使用。你不必给它们设置维度，而且它们永远不会产生越界`(out-of-bounds)`错误。但它们的性能相比真正的数组可能相当糟糕。

>`typeof`运算符不能辨别数组和对象。要判断一个值是否为数组，你还需要检查它的`constructor`属性:

```js
if (my_value && typeof my_value === 'object' && my_value.constructor === Array) {
	// my_value 是一个数组
};
```
>上面的检测对于在不同帧或窗口创建的数组将会给出`false`。当数组有可能在其他的帧中被创建时，下面的检侧更为可靠:

```js
if (Object.prototype.toString.apply(my_value) === '[object Array]') {
	// my_value 确实是一个数组
};
```
>`argument`数组不是一个数组，它只是一个有着`length`成员属性的对象。上面的检测会分辨出`arguments`并不是一个数组。

###假值（Falsy Values）
###hasOwnProperty
###对象（Object）


##附录 B 糟粕（Bad Parts）
###==
###with 语句（with Statement）
###eval
###continue 语句（continue Statement）

>`continue`语句跳到循环的顶部。我发现一段代码通过重构移除`continue`语句之后，性能都会得到改善

###switch 穿越（switch Fall Through）
###缺少快语句（Block-less Sattements）
###++ --
###位运算符（Bitwise Operators）
###function 语句对比 function 表达式（The function Statement Versus the function Expression）
###类型包装对象（Typed Wrappers）
###new

>**JavaScript** 的`new`运算符创建一个继承于其运算数的原型的新对象，然后调用该运算数，把新创建的对象绑定给`this`。这给运算数 ( 它应该是一个构造器函数 ) 一个机会在返回给请求者前自定义新创建的对象。如果你忘记了使用此`new`运算符，你得到的就是一个普通的函数调用，并且`this`被绑定到全局对象，而不是新创建的对象。这意味着当你的函数尝试去初始化新成员属性时它将会污染全局变量。这是一件非常糟糕的事情。而且既没有编译时警告，也没有运行时警告。按照惯例，打算与`new`结合使用的函数应该以首字母大写的形式命名，并且首字母大写的形式应该只用来命名那些构造器函数。这个约定帮助我们进行区分，便于我们发现那些 **JavaScript** 语言自身经常忽略但却会带来昂贵代价的错误

###void

---
##附录 C JSLint（JSLint）

---
##附录 D 语法图（Syntax Diagrams）

---
##附录 E JSON（JSON）

---
##扩展阅读
###一 . 基本语法
#### new 一个函数和直接调用函数的异同

>或许许多人对此不以为然，在函数前加 `new` 关键字，不就是实例化一个对象吗？但事情显然没那么简单：

```js
function Test() { 
　　this.name = 'Test'; 
　　return function() { return true; } 
} 
var test = new Test(); // 这里的 test 是什么？ 
```

>是一个 `Test` 对象吗？错！这里 `test` 是一个函数, `Test` 中返回的 `function() { return true; }`。这时，`new Test()` 等效于 `Test()`，注意，是等效于，不是等于，如果使用`new Test() == Test()` 判定两者是否相等，则会返回 `false`，因为 **Javascript**  对于 `Object` 和 `Function` 的比较是基于引用的。

- **为了更清晰的分辨在上述情形下两者间的区别，请继续看以下代码：**

```js
function Test() { 
　　this.name = 'Test'; 
　　return 'Test'; 
}
var fnT = Test(); 
var newT = new Test(); 
```

>显然，`fnT` 是字符串 `'Test'`，此时 `newT` 是一个 `Test` 对象 —— 有一个名为 `name` 的属性，其值为字符串 `'Test'`。

>通过上面两段代码，我们可以得出一个猜测，如果函数返回值为常规意义上的值类型`（Number、String、Boolean）`时，`new` 函数将会返回一个该函数的实例对象，而如果函数返回一个引用类型`（Object、Array、Function）`，则 `new` 函数与直接调用函数产生的结果等同。

####把一个函数赋给一个变量时带括号与不带括号的区别
```js
function hi(){  
  var a = 1;  
  return function(){
   console.log(a++);
  };  
};         
var aaa = hi();
var bbb = hi;
aaa();
aaa();
bbb();
```
>`aaa` 是将 `hi()` 的运行结果赋值给它，即 `return` 返回的匿名函数，此时有一个闭包，则每次调用 `aaa` 时都访问的同一个 `a`，`aaa()` 第一次运行结果为 `1`，第二次为`2`而 `bbb` 将是将 `hi` 这个函数名赋值给它，则调用 `bbb()` 后返回一个函数表达式，即`function(){console.log(a++)};`

#### js函数前面的加号，叹号
```js
!function(){}();
```
>这里的加号，也可以替换成 `+`, `~ `等其他一元操作符，其效果相当于 **`:`**
>如果没有这个加号的话，解析器会认为 **function** 是一个函数声明的开始，而后面（）将会导致语法错误。在 **function** 前面加上`+` 号时，就变成了一个函数表达式，而函数表达式后面又添加了一个 ( ) 就变成了一个立即执行的函数了。

#### javascript 中为何在匿名 function 函数后面还外加一个括号

>　　详细研究过Javascript代码库（如Jquery、YUI）的人，一定会看到过很多如下形式的函数： (function(){...}()) 或 (function(){})()对于很多初学者来说，遇到它们经常会产生一系列问号：这是编程吗，用它做什么，怎么我没在其他语言里见过呢？

- **接下来我就详细地解释一下：**

>　　它可以解释成为“匿名函数自调用”，也就是说，定义一个匿名函数，然后马上调用它（因为它是匿名的，如果不立即调用就获取不到该函数的引用了）。通常它被应用在一些大型的JS框架中（如上面所说的），因为这个匿名函数的函数体相当于提供一个匿名的名字空间，这样就不会再与用户自定义的JS函数、变量、对象发生冲突了。尽管JS没有显示地提供命名空间的定义和使用机制，但这种匿名方式却不失为是一种很好的解决命名空间问题的方法。
　　
>　　所以说，(function(){代码})() 就等于执行了一个函数，只不过它是匿名的而已。如果在这个匿名函数内部想再次调用这个函数，就需要调用 [constructor](http://www.w3school.com.cn/jsref/jsref_constructor_array.asp) 属性了（这是 Object 中定义的，JS 的继承机制如同 Java 一样保证了了所有对象都继承 Object 类）。
　　
>　　明白了它是什么了，下面我们就要学习该怎样使用它了，以下这些问题是我们会经常遇到的，不如提前做好理论只是准备以备后期能顺利地实现开发。请看下面问题：


*1、下列哪些正确？（B、C）*
```js
A.function(){
	console.log("Here!");
　}();

B.(function(){
	console.log("Here!");
　})();

C.(function(){
	console.log("Here!");
　}());
```

*2、下列哪个结果是正确的？（A、B、C、D）*
```js
A.(function(a1,a2){
	console.log("Here!"+(a1+a2));
　})(1,2);

B.(function(a1,a2){
	console.log("Here!" +(a1+a2));
　}(1,2));

C.void function(a1,a2){
	console.log("Here!" +(a1+a2));
　}(1,2);

D.var f = function(a1,a2){
	console.log("Here!" +(a1+a2));
　}(1,2);
```
- `注`：A 、B、C 与 D 四种格式都正确，前两者属于同种情况的不同写法，后两种是将函数对象的返回值赋给其他变量，C 是忽略函数返回值，而 D 正相反！


*具体举个例子*：
```js
　　function test(){
	　　return (function(p1,p2){
	　　　return p1+p2;
	　　})(1,2);
　　};
　　(function(){
		console.log(test());
　　}());
```
**> 下面我们就深入研究一下这种匿名函数：**
```js
　　function Foo() {
　　　 var a  = 123;
　　　 this.a = 456;
　　　 (function() {
　　　　　 console.log(a); // 123
		  console.log(this.a); // undefined
　　　 })();
　　};
　　var f = new Foo();
```
```js
　　function Foo() {
　　　 var a  = 123;
　　　 this.a = 456;
　　　 (function(_this) {
　　　　　 console.log(a); // 123
		  console.log(_this.a); // 456
　　　 })(this);
　　};
　　var f = new Foo();
```


**> 以上两个对比，说明：**
>（1）匿名函数可以直接访问到外层署名函数（Foo）中的变量（使用关键字var定义的），但不能访问外层署名函数的属性（使用关键字this定义的）；
>（2）匿名函数中的this指向的是匿名函数对象的地址，它与外层署名函数（Foo）对象的this指向的地址不同；
>（3）匿名函数若要访问外层署名函数（Foo）中的属性，可以通过参数传递的方式实现。


```js
　　function Foo() {
　　　 var a = 123;
　　　 this.a = 456;
　　　 (function(b) {
　　　　　 console.log(a); // 123
	　　　 console.log(b); // 456
　　　 })(this.a);
　　};
　　var f = new Foo();
```
```js
　　(function() {
　　　 var a = 123;
　　　 this.a = 456;
　　　 (function() {
　　　　　 console.log(a); // 123
		  console.log(this.a); // 456
　　　 })();
　　})();
```
**>  以上两个对比，说明：**
>（1） 匿名函数既可以直接访问外层匿名函数中的变量，又直接可以访问外层匿名函数中的属性，而匿名函数却不可以直接访问外层已命名函数中的属性；
>（2）以上两种方式可以实现相同的功能。
```js
　　(function() {
　　　 var a = 123;
　　　 this.a = 456;
　　　 (function() {
　　　　　  console.log(a); // 123
		   console.log(this.a); // 456
		 　this.b = 789;
　　　 })();
　　　 (function() {
		   console.log(this.b); // 789
　　　 })();
　　})();

　　(function() {
		console.log(this.a); // 456
		console.log(this.b); // 789
　　})();
```
```js
　　function Foo() {
　　　 var a = 123;
　　　 this.a = 456;
　　　 (function() {
　　　　　  console.log(a); // 123
		   console.log(this.a); // undefined
		　 this.b = 789;
　　　 })();
　　　 (function() {
		   console.log(this.b); // 789
　　　 })();
　　};
　　var f = new Foo();

　　(function() {
　　　  console.log(this.a); // undefined
	   console.log(this.b); // 789
　　})();
```
**> 以上两个对比，说明：**
>（1）匿名函数（即用两个小括号括起来的部分）位于一个执行上下文，不论这些代码放在哪个位置上。
```js
　　function Foo() {
	　　(function() {
	　　　  this.b = 789;
	　　})();
	　　(function() {
			console.log(this.b); // 789
			console.log(b); // 789
		　　 var a = 0;
		    console.log(a); // 0
	　　})();
　　}
　　var f = new Foo();

　　(function() {
		console.log(this.b); // 789
		console.log(b); // 789
　　})();
```
```js
　　function Foo() {
	　　(function() {
		　　this.b = 789;
	　　})();
	　　(function() {
			console.log(this.b); // 789
			console.log(b); //undefined
		　　var b = 0;
			console.log(b); // 0
	　　})();
　　}
　　var f = new Foo();

　　(function() {
		console.log(this.b); // 789
		console.log(b); // 789
　　})();
```
**> 以上两个对比，说明：**
>（1）没有加 this取值时，如果当前 {} 中不存在同名的局部变量，则等同于加 this 处理；如果当前 {} 中存在同名的局部变量，则按常规处理。

---
###二 . 特殊用法

####Apply 与 Call
#####Apply
>`Apply（obj,args）`方法接收两个参数，通过劫持另外一个对象的方法与属性从而达到继承的作用
>- `obj`：当是`this`时，本对象里的`this`将继承`Function.apply(obj,args)`的`Function`里的属性与方法
>- `args`：这个是数组，它将作为参数传给`Function（args-->arguments）`

**apply 示范代码如下：**

```js
function Person(name,age){   //定义一个类，人类  
    this.name = name;     	 //名字  
    this.age  = age;       	 //年龄 
    this.sayhello = function(){
        console.log("hello");
    };
    console.log(this,arguments);
}

function Print(){            //显示类的属性 
    this.funcName = "Print"; 
    this.show = function(){      
        var msg = [];
        for(var key in this){ 
            if(typeof(this[key]) != "function"){
                msg.push([key,":",this[key]].join(" "));
                // console.log(msg); 
            }
        } 
        console.log(msg.join(" "));
    };
    console.log(this,arguments);
}

function Student(name,age,grade,school){    //学生类 
    Person.apply(this,arguments);
    Print.apply(this,arguments);
    this.grade  = grade;                	//年级 
    this.school = school;                	//学校 
    // console.log(this,arguments);
}

var p1 = new Person("jake",10);
p1.sayhello();
var pr = new Print();
var s1 = new Student("tom",13,6,"清华小学");
s1.show();
s1.sayhello();
console.log(s1.funcName);
```
>学生类本来不具备任何方法，但是在`Person.apply(this,arguments)`后，他就具备了`Person`类的`sayhello`方法和所有属性。在`Print.apply(this,arguments)`后就自动得到了`show()`方法

 >`Function.apply()`在提升程序性能方面的技巧

>我们先从`Math.max()`函数说起，`Math.max`后面可以接任意个参数，最后返回所有参数中的最大值。

**比如 ：**

```js
console.log(Math.max(5,8))   //8
console.log(Math.max(5,7,9,3,1,6))   //9
```

>但是在很多情况下，我们需要找出数组中最大的元素。
```js
var arr=[5,7,9,1]
console.log(Math.max(arr))    // 这样却是不行的。一定要这样写

function getMax(arr){
    var arrLen=arr.length;
    for(var i=0,ret=arr[0];i<arrLen;i++){
        ret=Math.max(ret,arr[i]);       
    }
    return ret;
}
```
>这样写麻烦而且低效。如果用`apply`呢，看代码：
```js
function getMax2(arr){
    return Math.max.apply(null,arr);
}
```
>两段代码达到了同样的目的，但是`getMax2`却优雅，高效，简洁得多。

**再比如数组的`push`方法；**

```js
var arr1=[1,3,4];
var arr2=[3,4,5];
```

>如果我们要把`arr2`展开，然后一个一个追加到`rr1`中去，最后让`arr1=[1,3,4,3,4,5]`
>`arr1.push(arr2)`显然是不行的。 因为这样做会得到`[1,3,4,[3,4,5]]`

>我们只能用一个循环去一个一个的`push`(当然也可以用`arr1.concat(arr2)`，但是`concat`方法并不改变`arr1`本身)

```js
var arrLen=arr2.length
for(var i=0;i<arrLen;i++){
    arr1.push(arr2[i]);
}
```
>自从有了`Apply`,事情就变得如此简单

```js
Array.prototype.push.apply(arr1,arr2)
```

#####Call

>`Call`和`Apply`的意思一样,只不过是参数列表不一样.

> `Function.call(obj,[param1[,param2[,…[,paramN]]]])`
>- `obj`：与 Apply 相同
>- `params`：指定的参数列表

#####什么情况下用`apply`,什么情况下用`call`

>在给对象参数的情况下,如果参数的形式是数组的时候,比如`apply`示例里面传递了参数`arguments`,这个参数是数组类型,并且在调用`Person`的时候参数的列表是对应一致的 **( 也就是`Person`和`Student`的参数列表前两位是一致的 )** 就可以采用 `apply `, 如果我的`Person`的参数列表是这样的`(age,name)`,而`Student`的参数列表是`(name,age,grade)`,这样就可以用`call`来实现了,也就是直接指定参数列表对应值的位置`(Person.call(this,age,name,grade))`;

---

>`call` 和 `apply` 都是为了改变某个函数运行时的 `context` 即上下文而存在的，换句话说，就是为了改变函数体内部 this 的指向。因为 JavaScript 的函数存在**「定义时上下文」**和**「运行时上下文」**以及**「上下文是可以改变的」**这样的概念。

>二者的作用完全一样，只是接受参数的方式不太一样。例如，有一个函数 func1 定义如下：
```js
var func1 = function(arg1, arg2) {};
```

>就可以通过 `func1.call(this, arg1, arg2)`; 或者 `func1.apply(this, [arg1, arg2])`; 来调用。其中 `this` 是你想指定的上下文，他可以任何一个 JavaScript 对象(JavaScript 中一切皆对象)，`call` 需要把参数按顺序传递进去，而 `apply` 则是把参数放在数组里。

>JavaScript 中，某个函数的参数数量是不固定的，因此要说适用条件的话，当你的参数是明确知道数量时，用 `call`，而不确定的时候，用 ``apply`，然后把参数 `push` 进数组传递进去。当参数数量不确定时，函数内部也可以通过 `arguments` 这个数组来便利所有的参数。

----

>要先明白存在`call`和`apply`的原因，才能记得牢一点：
>在`javascript OOP`中，我们经常会这样定义：

```js
function cat(){

}

cat.prototype={
	food:"fish",
		say: function(){
		alert("I love "+this.food);
	}
}
var blackCat = new cat;
blackCat.say();
```

>但是如果我们有一个对象`whiteDog = {food:"bone"}`,我们不想对它重新定义say方法，那么我们可以通过`call`或`apply`用`blackCat`的`say`方法：`blackCat.say.call(whiteDog)`;

>所以，可以看出`call`和`apply`是为了动态改变`this`而出现的，当一个`object`没有某个方法，但是其他的有，我们可以借助`call`或`apply`用其它对象的方法来操作。

>用的比较多的，通过`document.getElementsByTagName`选择的`dom` 节点是一种类似`array`的`array`。它不能应用Array下的`push,pop`等方法。我们可以通过：
>`var domNodes = Array.prototype.slice.call(document.getElementsByTagName("*"))`;
>这样`domNodes`就可以应用`Array`下的所有方法了。


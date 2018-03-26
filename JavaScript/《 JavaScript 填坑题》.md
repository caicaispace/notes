# javaScript 填坑题

[TOC]

## new 一个函数和直接调用函数的异同

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


## 深入理解Javascript之this关键字

Javascript是一种很灵活的语言, 而This关键字又是灵活中的灵活, 但是因为它的灵活, 也注定了它的难用.

以前我用this的时候, 都会觉得不踏实, 老是担心它不知道怎么地就会指到另外的什么地方.

其实, 这都是因为, 我们对它的不了解.

刚好最近再给百度学院做《Javascript高级-作用域/原型链》的ppt, 而swit1983网友也刚好提这个问题, 索性就把这部分内容独立总结出来, 与大家分享.

首先, 我先抛出一个定论:”**`在Javascript中,this关键字永远都指向函数(方法)的所有者`**”.

函数

首先,让我们看看”函数”:
```js
function introduce() {
     alert("Hello, I am Laruence\r\n");
}
```
对于,这个函数, this关键字指向谁呢?

**`当一个函数作为函数而不是方法调用时这个this关键字引用全局对象`**

如我之前的文章所述(Javascript作用域), 定义在全局的函数, 函数的所有者就是当前页面, 也就是window对象.

这也就是为什么, 我把函数用引号引起来. 因为定义在全局的函数, 其实也就是window对象的一个方法.

所以,我们即可用通过函数名直接调用, 也可用通过window.方法名来调用, 这个时候, 方法中的this关键字指向它的所有者:window对象.

如果, 我们查看window的introduce属性, 就会得到:
```js
var name = "I am Laruence";
function introduce() {
     alert(this.name);
}
alert(window.introduce);
/**
* output:
* function introduce() {
* alert(this.name);
* }
*/
```
看了上面的代码, 也许你就会想到既然, 全局函数是window对象的方法, 全局变量是window对象的属性(Javasript作用域中已有述及), 那么,在全局函数中就可用通过this关键字来访问全局变量吧?

答案是肯定的, 如果调用introduce函数, 你就会认识我是Laruence.

事件处理函数

也许, 对于this关键字的迷惑, 绝大部分原因是来自把函数(方法)用在事件处理的时候.
```
<input id="name" type="text"  name="name" value="Laruence" />
```
比如, 我们现在需要在点击”name”输入框的时候, 显示出name输入框的value. 那么, 可用写下如下代码:
```js
function showValue() {
     alert(this.value);
}
document.getElementById('name').onclick = showValue;
```
上面的代码, 运行正常, 但是why? 不是说函数的this指针永远指向函数所有者么? 不是说全局变量的所有者是window对象么?

呵呵, 如果你能想到这个问题, 那说明你在认真的看我的文章, 否则,,我建议你从头看起, 否则看完了,你还是迷糊~

恩, 对, 对于上面的代码, showValue是定义在了全局对象, 那么看来问题只能发生在onclick事件绑定的时候了.

我们知道, 在Js中一切都是对象, 函数和方法也都是对象的属性, 只不过函数有可执行的内部属性. 所以, 对于上面的代码, 在对onclick绑定处理器的时候, 其实是对id为name的输入框Dom对象的onclick属性赋值.

也就是说, 我们把函数showValue Copy 给了name输入框对象的onclick属性. 如果我们查看此时的onclick:
```js
function showValue() {
     alert(this.value);
}
document.getElementById('name').onclick = showValue;
alert(document.getElementById('name').onclick);
/**
* output
* function showValue() {
* alert(this.value);
* }
*/
```
所以, 当事件触发的时候, 就会去调用name输入框的onclick方法, 这个时候,this关键字自然就指向的是name输入框了.

但是, 迷惑的事情就来了, 比如下面这种写法:
```js
function showValue() {
     alert(this.value);
}
<input id="name" type="text"  name="name" value="Laruence"  onclick="showValue()"/>
```
就无法正常运行, 这又是为什么呢?

恩, 因为这个时候, 并不是赋值, 而是引用.

如果我们注意俩种onclick的写法, 你会发现, 对于之前的方法, 我们使用的是:
```js
dom.onclick = showvalue; //没有调用符
```
而对于刚才的方法:
```js
onclick = "showvalue()" //有调用符
```
这个也能侧面的反映出俩者的区别:对于前者,是赋值, 而对于后者是引用. 如果我们这个时候查看输入框的onclick属性,我们得到:
```js
alert(dom.onclick);
/**
* output:
* function onclick() {
*    showValue();
* }
*/
```
看到区别了么? 也就懂得为什么了吧?

讲到这里, 有一个很有趣的例子 ,大家可以在IE下试试:
```js
<img src="xxx" onerror="alert(1);} function hi() { alert(2); " />
```
改变this的指向

那, 既然我们已经知道为什么了, 那怎么才能让this指向我们想要指的地方呢?

对于上面的事件处理函数来说, 我们可以有如下几种写法:
```js
dom.onclick = showValue();
dom.onclick = function() { alert(this.value) ;}
<input onclick="alert(this.value);" /> //想想刚才我们的引用,是如何把这句嵌入的.
dom.addEventListener(dom, showValue, false); //ff only
```
而对于不是事件处理函数的场合, 我们可以使用apply, 或者call, 来改变this关键字的指向.

比如:
```js
var laruence = {
     name : 'laruence',
     age  : 26,
     position : 'Senior PHP Engineer',
     company : 'Baidu.inc'
};
 
function introduce() {
     alert(this.name);
}
 
introduce.call(laruence);
```

## 闭包
### 一、 什么是闭包？

 > 官方” 的解释是： 闭包是一个拥有许多变量和绑定了这些变量的环境的表达式（ 通常是一个函数）， 因而这些变量也是该表达式的一部分。

>相信很少有人能直接看懂这句话， 因为他描述的太学术。 其实这句话通俗的来说就是： JavaScript中所有的function都是一个闭包。 不过一般来说， 嵌套的function所产生的闭包更为强大， 也是大部分时候我们所谓的“ 闭包”。 看下面这段代码：

```js
function a() {
    var i = 0;

    function b() {
        console.log(++i);
    }
    return b;
}
var c = a();
c();
```

这段代码有两个特点：

- 1、 函数b嵌套在函数a内部；
- 2、 函数a返回函数b。

**引用关系如图**

3dcc4233-c46e-4e38-a4ac-76f29664da95.png

>这样在执行完var c = a() 后， 变量c实际上是指向了函数b， 再执行c() 后就会弹出一个窗口显示i的值(第一次为1)。这段代码其实就创建了一个闭包， 为什么？ 因为函数a外的变量c引用了函数a内的函数b， 就是说：

>当函数a的内部函数b被函数a外的一个变量引用的时候， 就创建了一个闭包。

>让我们说的更透彻一些。 所谓“ 闭包”， 就是在构造函数体内定义另外的函数作为目标对象的方法函数，而这个对象的方法函数反过来引用外层函数体中的临时变量。 这使得只要目标 对象在生存期内始终能保持其方法， 就能间接保持原构造函数体当时用到的临时变量值。 尽管最开始的构造函数调用已经结束， 临时变量的名称也都消失了， 但在目 标对象的方法内却始终能引用到该变量的值， 而且该值只能通这种方法来访问。 即使再次调用相同的构造函数， 但只会生成新对象和方法， 新的临时变量只是对应新 的值， 和上次那次调用的是各自独立的。

### 二、 闭包有什么作用？

>简而言之， 闭包的作用就是在a执行完并返回后， 闭包使得Javascript的垃圾回收机制GC不会收回a所占用的资源， 因为a的内部函数b的执行需要依赖a中的变量。 这是对闭包作用的非常直白的描述， 不专业也不严谨， 但大概意思就是这样， 理解闭包需要循序渐进的过程。

>在上面的例子中， 由于闭包的存在使得函数a返回后， a中的i始终存在， 这样每次执行c()， i都是自加1后alert出i的值。

>那 么我们来想象另一种情况， 如果a返回的不是函数b， 情况就完全不同了。 因为a执行完后， b没有被返回给a的外界， 只是被a所引用， 而此时a也只会被b引 用， 因此函数a和b互相引用但又不被外界打扰(被外界引用)， 函数a和b就会被GC回收。(关于Javascript的垃圾回收机制将在后面详细介绍)

### 三、 闭包内的微观世界

如果要更加深入的了解闭包以及函数a和嵌套函数b的关系， 我们需要引入另外几个概念： 函数的执行环境(excution context)、 活动对象(call object)、 作用域(scope)、 作用域链(scope chain)。 以函数a从定义到执行的过程为例阐述这几个概念。

>　1.当定义函数a的时候， js解释器会将函数a的作用域链(scope chain) 设置为定义a时a所在的“ 环境”， 如果a是一个全局函数， 则scope chain中只有window对象。

>　2.当执行函数a的时候， a会进入相应的执行环境(excution context)。

>　3.在创建执行环境的过程中， 首先会为a添加一个scope属性， 即a的作用域， 其值就为第1步中的scope chain。 即a.scope = a的作用域链。

>　4.然后执行环境会创建一个活动对象(call object)。 活动对象也是一个拥有属性的对象， 但它不具有原型而且不能通过JavaScript代码直接访问。 创建完活动对象后， 把活动对象添加到a的作用域链的最顶端。 此时a的作用域链包含了两个对象： a的活动对象和window对象。

>　5.下一步是在活动对象上添加一个arguments属性， 它保存着调用函数a时所传递的参数。

>　6.最后把所有函数a的形参和内部的函数b的引用也添加到a的活动对象上。 在这一步中， 完成了函数b的的定义， 因此如同第3步， 函数b的作用域链被设置为b所被定义的环境， 即a的作用域。

到此， 整个函数a从定义到执行的步骤就完成了。 此时a返回函数b的引用给c， 又函数b的作用域链包含了对函数a的活动对象的引用， 也就是说b可以访问到a中定义的所有变量和函数。 函数b被c引用， 函数b又依赖函数a， 因此函数a在返回后不会被GC回收。

当函数b执行的时候亦会像以上步骤一样。 因此， 执行时b的作用域链包含了3个对象： b的活动对象、 a的活动对象和window对象， 如下图所示：

8a00e2d5-0e92-446c-9a62-4c791fd6a604.png


如图所示， 当在函数b中访问一个变量的时候， 搜索顺序是：

>先搜索自身的活动对象， 如果存在则返回， 如果不存在将继续搜索函数a的活动对象， 依次查找， 直到找到为止。如果函数b存在prototype原型对象， 则在查找完自身的活动对象后先查找自身的原型对象， 再继续查找。 这就是Javascript中的变量查找机制。

>如果整个作用域链上都无法找到， 则返回undefined。小结， 本段中提到了两个重要的词语： 函数的定义与执行。 文中提到函数的作用域是在定义函数时候就已经确定， 而不是在执行的时候确定（ 参看步骤1和3）。 用一段代码来说明这个问题：

```js
function f(x) {
    var g = function() {
        return x;
    }
    return g;
}
var h = f(1);
alert(h());
```

>这段代码中变量h指向了f中的那个匿名函数(由g返回)。

>假设函数h的作用域是在执行alert(h()) 确定的， 那么此时h的作用域链是： h的活动对象 - > alert的活动对象 - > window对象。

>假设函数h的作用域是在定义时确定的， 就是说h指向的那个匿名函数在定义的时候就已经确定了作用域。 那么在执行的时候， h的作用域链为： h的活动对象 - > f的活动对象 - > window对象。

>如果第一种假设成立， 那输出值就是undefined； 如果第二种假设成立， 输出值则为1。

>运行结果证明了第2个假设是正确的， 说明函数的作用域确实是在定义这个函数的时候就已经确定了。

### 四、 闭包的应用场景

>保护函数内的变量安全。 以最开始的例子为例， 函数a中i只有函数b才能访问， 而无法通过其他途径访问到， 因此保护了i的安全性。

> - 在内存中维持一个变量。 依然如前例， 由于闭包， 函数a中i的一直存在于内存中， 因此每次执行c()， 都会给i自加1。
> - 通过保护变量的安全实现JS私有属性和私有方法（ 不能被外部访问）私有属性和方法在Constructor外是无法被访问的

```js
function Constructor(…) {
    var that = this;
    var membername = value;
    function membername(…) {…}
}
```

以上3点是闭包最基本的应用场景， 很多经典案例都源于此。

### 五、 Javascript的垃圾回收机制

>在Javascript中， 如果一个对象不再被引用， 那么这个对象就会被GC回收。 如果两个对象互相引用， 而不再被第3者所引用， 那么这两个互相引用的对象也会被回收。 因为函数a被b引用， b又被a外的c引用， 这就是为什么函数a执行后不会被回收的原因。


## Javascript原型链和原型的一个误区

之前我对Javascript的原型链中, 原型继承与标识符查找有些迷惑,

如, 如下的代码:

```js
function Foo() {};
var foo = new Foo();
Foo.prototype.label = "laruence";
alert(foo.label); //output: laruence
alert(Foo.label);//output: undefined
```

今天看到了如下这个图:

Javascript object layout
另外, 在Javascript Object Hierarchy看到:

>The prototype is only used for properties inherited by objects/instances created by that function. The function itself does not use the associated prototype.

也就是说, 函数对象的prototype并不作用于原型链查找过程中,

今天在firefox下发现(因为firefox通过__proto__暴露了[[prototype]]), 真正参与标识符查找的是函数对象的__proto__,
```js
function Foo() {};
var foo = new Foo();
Foo.__proto__.label = "laruence";
alert(Foo.label); //output: laruence
alert(foo.label);//output: undefined
```
而, 显然的:
```js
function Foo() {};
alert(Foo.__proto__ === Foo.prototype); //output: false
```
另外, 也解释了,
```js
alert(Object.forEach); // undefined
 
Function.prototype.forEach = function(object, block, context) {
    for (var key in object) {
        if (typeof this.prototype[key] == "undefined") {
            block.call(context, object[key], key, object);
        }
    }
 
};
 
alert(Object.forEach);
alert(Function.forEach);
alert(Object.forEach === Function.forEach); // true
```

## Javascript作用域原理

### 问题的提出

首先看一个例子:

```js
var name = 'laruence';
function echo() {
     alert(name);
     var name = 'eve';
     alert(name);
     alert(age);
}
echo();
```
 
运行结果是什么呢?

上面的问题, 我相信会有很多人会认为是:

```js
laruence
eve
[脚本出错]
```

因为会以为在echo中, 第一次alert的时候, 会取到全局变量name的值, 而第二次值被局部变量name覆盖, 所以第二次alert是’eve’. 而age属性没有定义, 所以脚本会出错.

但其实, 运行结果应该是:

```js
undefined
eve
[脚本出错]
```

为什么呢?

### JavaScript的作用域链

首先让让我们来看看Javasript(简称JS, 不完全代表JScript)的作用域的原理: JS权威指南中有一句很精辟的描述:　”JavaScript中的函数运行在它们被定义的作用域里,而不是它们被执行的作用域里.”　

为了接下来的知识, 你能顺利理解, 我再提醒一下, 在JS中:”一切皆是对象, 函数也是”.

在JS中，作用域的概念和其他语言差不多， 在每次调用一个函数的时候 ，就会进入一个函数内的作用域，当从函数返回以后，就返回调用前的作用域.

JS的语法风格和C/C++类似, 但作用域的实现却和C/C++不同，并非用“堆栈”方式，而是使用列表，具体过程如下(ECMA262中所述):

任何执行上下文时刻的作用域, 都是由作用域链(scope chain, 后面介绍)来实现.
在一个函数被定义的时候, 会将它定义时刻的scope chain链接到这个函数对象的[[scope]]属性.
在一个函数对象被调用的时候，会创建一个活动对象(也就是一个对象), 然后对于每一个函数的形参，都命名为该活动对象的命名属性, 然后将这个活动对象做为此时的作用域链(scope chain)最前端, 并将这个函数对象的[[scope]]加入到scope chain中.

看个例子:

```js
var func = function(lps, rps){
		var name = 'laruence';
	    ........
}
func();
```
 
在执行func的定义语句的时候, 会创建一个这个函数对象的[[scope]]属性(内部属性,只有JS引擎可以访问, 但FireFox的几个引擎(SpiderMonkey和Rhino)提供了私有属性 `__parent__` 来访问它), 并将这个[[scope]]属性, 链接到定义它的作用域链上(后面会详细介绍), 此时因为func定义在全局环境, 所以此时的[[scope]]只是指向全局活动对象window active object.

在调用func的时候, 会创建一个活动对象(假设为aObj, 由JS引擎预编译时刻创建, 后面会介绍)，并创建arguments属性, 然后会给这个对象添加俩个命名属性aObj.lps, aObj.rps; 对于每一个在这个函数中申明的局部变量和函数定义, 都作为该活动对象的同名命名属性.

然后将调用参数赋值给形参数，对于缺少的调用参数，赋值为undefined。

然后将这个活动对象做为scope chain的最前端, 并将func的[[scope]]属性所指向的,定义func时候的顶级活动对象, 加入到scope chain.

有了上面的作用域链, 在发生标识符解析的时候, 就会逆向查询当前scope chain列表的每一个活动对象的属性，如果找到同名的就返回。找不到，那就是这个标识符没有被定义。

注意到, 因为函数对象的[[scope]]属性是在定义一个函数的时候决定的, 而非调用的时候, 所以如下面的例子:

```js
var name = 'laruence';
function echo() {
     alert(name);
}

function env() {
     var name = 'eve';
     echo();
}

env();
```
 
运行结果是:

```js
laruence
```

结合上面的知识, 我们来看看下面这个例子:

```js
function factory() {
     var name = 'laruence';
     var intro = function(){
          alert('I am ' + name);
     }
     return intro;
}
 
function app(para){
     var name = para;
     var func = factory();
     func();
}
 
app('eve');
```

当调用app的时候, scope chain是由: {window活动对象(全局)}->{app的活动对象} 组成.

在刚进入app函数体时, app的活动对象有一个arguments属性, 俩个值为undefined的属性: name和func. 和一个值为’eve’的属性para;

此时的scope chain如下:

```js
[[scope chain]] = [
	{
	     para : 'eve',
	     name : undefined,
	     func : undefined,
	     arguments : []
	}, {
	     window call object
	}
]
```

当调用进入factory的函数体的时候, 此时的factory的scope chain为:

```js
[[scope chain]] = [
	{
	     name : undefined,
	     intor : undefined
	}, {
	     window call object
	}
]
```

注意到, 此时的作用域链中, 并不包含app的活动对象.

在定义intro函数的时候, intro函数的[[scope]]为:

```js
[[scope chain]] = [
	{
	     name : 'laruence',
	     intor : undefined
	}, {
	     window call object
	}
]
```

从factory函数返回以后,在app体内调用intor的时候, 发生了标识符解析, 而此时的sope chain是:

```js
[[scope chain]] = [
	{
	     intro call object
	}, {
	     name : 'laruence',
	     intor : undefined
	}, {
	     window call object
	}
]
```

因为scope chain中,并不包含factory活动对象. 所以, name标识符解析的结果应该是factory活动对象中的name属性, 也就是’laruence’.

所以运行结果是:

```js
I am laruence
```

现在, 大家对”JavaScript中的函数运行在它们被定义的作用域里,而不是它们被执行的作用域里.”这句话, 应该有了个全面的认识了吧?

### Javascript的预编译

我们都知道,JS是一种脚本语言, JS的执行过程, 是一种翻译执行的过程.
那么JS的执行中, 有没有类似编译的过程呢?

首先, 我们来看一个例子:

```js
 <script>
 alert(typeof eve); //function
      function eve() {
           alert('I am Laruence');
      };
 </script>
```

诶? 在alert的时候, eve不是应该还是未定义的么? 怎么eve的类型还是function呢?

恩, 对, 在JS中, 是有预编译的过程的, JS在执行每一段JS代码之前, 都会首先处理var关键字和function定义式(函数定义式和函数表达式).
如上文所说, 在调用函数执行之前, 会首先创建一个活动对象, 然后搜寻这个函数中的局部变量定义,和函数定义, 将变量名和函数名都做为这个活动对象的同名属性, 对于局部变量定义,变量的值会在真正执行的时候才计算, 此时只是简单的赋为undefined.

而对于函数的定义,是一个要注意的地方:

```js
<script>
     alert(typeof eve); //结果:function
     alert(typeof walle); //结果:undefined
     function eve() { //函数定义式
          alert('I am Laruence');
     };
     var walle = function() { //函数表达式
     }
     alert(typeof walle); //结果:function
</script>
```

这就是函数定义式和函数表达式的不同, 对于函数定义式, 会将函数定义提前. 而函数表达式, 会在执行过程中才计算.

说到这里, 顺便说一个问题 :

```js
var name = 'laruence';
age = 26;
```

我们都知道不使用var关键字定义的变量, 相当于是全局变量, 联系到我们刚才的知识:

在对age做标识符解析的时候, 因为是写操作, 所以当找到到全局的window活动对象的时候都没有找到这个标识符的时候, 会在window活动对象的基础上, 返回一个值为undefined的age属性.

也就是说, age会被定义在顶级作用域中.

现在, 也许你注意到了我刚才说的: **JS在执行每一段JS代码..**
对, 让我们看看下面的例子:

```
<script>
     alert(typeof eve); //结果:undefined
</script>
<script>
     function eve() {
          alert('I am Laruence');
     }
</script>
```

明白了么? 也就是JS的预编译是以段为处理单元的…

### 揭开谜底

现在让我们回到我们的第一个问题:

当echo函数被调用的时候, echo的活动对象已经被预编译过程创建, 此时echo的活动对象为:

```js
[callObj] = {
	name : undefined
}
```

当第一次alert的时候, 发生了标识符解析, 在echo的活动对象中找到了name属性, 所以这个name属性, 完全的遮挡了全局活动对象中的name属性.

现在你明白了吧?
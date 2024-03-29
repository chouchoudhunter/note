## 什么是对象？

既然要说清楚，先问一个最最基本的问题：什么是对象？

对象一句话就能解释清楚：

> 对象是一系列属性 & 数据的集合。

在 `JavaScript` 中创建一个对象的方式有很多，常见的如下：

```
// 字面量直接创建
let obj1 = {foo: 'bar'};

// 通过实例化 Object 类
let obj2 = new Object({foo: 'bar'});

class A {
    constructor(foo){
        this.foo = foo;
    }
}

// 通过自定义类创建
let obj3 = new A('bar');
复制代码
```

以上代码最终都产生了 `{foo: 'bar'}` 这个对象。对象下有一个叫 `foo` 的属性，它的值为 `bar`。那么它在堆中是如何存储的呢？

![[Pasted image 20230228214432.jpg]]

看过了 [`JS 变量存储？栈 & 堆？NONONO!`](https://blog.acohome.cn/js-variable-in-memory/) 相信大家对于上图应该不陌生。

## 对象的取值

我们继续看一个较为复杂的对象（对象下某个属性也是一个对象）：

```
let complexObj = {
    num: 1,
    str: 'string',
    obj: {
        foo: 'bar',
    }
}
复制代码
```

在内存中的模型如下：

![复杂对象模型](https://user-gold-cdn.xitu.io/2019/12/9/16ee850f392208b0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

我们模拟一下 `complexObj.obj.foo` 这个取值过程。

![复杂对象取值过程](https://user-gold-cdn.xitu.io/2019/12/9/16ee850f39ea9b9a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

取值过程：碰到存储的值是地址值时，就到相应的地址值继续进行操作。

对象，总的来说，知识点有两点：

1. 创建对象：在内存堆中开辟一块用于存储一系列`属性 & 数据` 的空间。
2. 对象取值：根据存储的数据取值，如果是地址值，则到相应内存堆中继续进行操作。

对象到这里就差不多了，那原型又是什么呢？

## 原型 & 原型链

> 原型是对象下的一个属性，每个对象都有，同时原型也是一个对象。

文本的描述总是不直观的，我们通过代码来看：

```
let proto = {
    foo: 'bar'
}

let obj = {
    // ...
    __proto__: proto
}
复制代码
```

设置对象 `__proto__` 属性的过程，就是给对象设置了原型，同时该属性指向一个对象。

**注：** 当然 `ES6` 已经不建议这么做了，有专门的方法（`setPrototypeOf`）设置原型，为了解释方便，这里用 `ES5` 的代码作为示例。

有人可能会有疑问：既然是赋值操作，那应该也可以给对象的原型设置为非对象，为什么原型是对象呢？关于这个问题的答案，可以用以下代码测试：

```
let a = {};
a.__proto__ = 1;
console.log(a.__proto__);
复制代码
```

复制到浏览器即可看到效果，将对象的原型设置为非对象这个操作，是被禁止的，也就是说你设置了也没用。

原型的定义也很简单，那么原型是干嘛用的呢？

## 原型的作用

> 原型补充了原对象，当需要访问对象中属性，但该属性又不存在时，就会去原型上寻找。

按照上面的例子，`obj.foo` 返回什么？以下伪代码就是寻值的过程：

```
function getValue(obj, attr){
    let searchObj = obj
    while(searchObj 下不存在 attr 属性){
        searchObj = obj 的原型
    }
    return searchObj 下的 attr 属性 
}

getValue(obj, 'foo')
复制代码
```

以下为图示过程：

![获取原型上的属性](https://user-gold-cdn.xitu.io/2019/12/9/16ee850f4eb58ba5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

根据上图（或是伪代码）我们可得知最终的结果是： `bar`。

那如果对象上的原型还是没有该属性呢？

在原型的定义中，提到过：原型同时也是一个对象。转换一下思路，那么这个问题就变成了：如果对象上没有该属性那会发生什么？

去对象下的原型下找！对！我们刚说完！

这种层层递进的关系我们就把它称为：原型链！

到这你可能会问：如果按照这样一直找下去，那不是无穷无尽了？这就需要引出一个特殊的对象，可以在 `Chrome` 的控制台打印 `Object.prototype` 输出的对象，你可以仔细找找，该对象有没有原型？

`OH NO!` 竟然有原型，你个骗子。

不要激动，继续去查看它原型是什么：`null`！

这和我之前说的：原型也是一个对象，有出入，因为 `null` 明显不是一个对象。

但是，`typeof null` 确实是 `object`，笔者也有猜想过是不是和这有关系。但猜想是猜想，为了语义的完整性，我们重新定义一下原型：

> 原型是对象下的一个属性，每个对象都有，同时原型是对象或是 `null`。

同时定义一下原型链的特点：

> 原型链由一个个对象组成，原型链有终点，这个终点是 `null`。

`OK` 既然原型链有了终点，那么如果一个属性在原对象和所有的原型链上都不存在的话，他的值是什么？

`undefined`（未定义）啊！

现在我们已经清楚原型和对象的关系，那如何设置对象的原型呢？

## 设置原型

其实上面已经提到过，我们可以直接设置对象的原型：

### ES5 中

```
let proto = {
    foo: 'bar'
}

let obj = {
    // ...
    __proto__: proto
}
复制代码
```

### ES6 中

由于 `__proto__` 是非标准属性，因此在 `ES6` 中建议使用 `setPrototypeOf` 设置对象的原型。

```
let obj = {};

let proto = {
    foo: 'bar'
}

Object.setPrototypeOf(obj, proto);
复制代码
```

那如果一个对象还没有生成，比如仅仅定义了一个类，但又想控制通过这个类生成的对象的原型，该如何呢？

既然问了，那肯定是有的，解决方案就是函数的 `prototype` 属性。

## prototype

我们都知道在 `JavaScript` 中函数也是一个对象，这个特殊对象下有一个 `prototype` 属性，是干嘛用的呢？

> 在使用 `new` 关键字调用该函数时，函数下的 `prototype` 属性所保存的对象就是生成对象的原型。

通过代码来解释：

```
function A(bar) {
    this.bar = bar;
}

A.prototype.test = function(){
    console.log('test');
}
A.prototype.testAttr = 'testAttr';

let a = new A('bar');
复制代码
```

下面用伪代码来解释 `new A('bar')` 这个过程：

```
function fakeNew(A, bar){
    // 生成 this 为一个空对象。
    let this = {};
    Object.setPrototypeOf(this, A.prototype);
  
    // 执行 A 函数内的代码
    this.foo = bar;
  
    // 将 this 返回
    return this;
}

fakeNew(A, 'bar');
复制代码
```

相信大家看完代码就能理解 `prototype` 这个属性的作用了，但这是 `ES5` 的代码，`ES6` 中并不建议直接写 `prototype` ，而直接使用 `class`，但其本质是一样的。复制下面代码到 `Chrome` 里即可查到真相：

```
class A {
    constructor(bar) {
        this.foo = bar;
    }
    test() {
        console.log('test');
    }
}

console.dir(A);
复制代码
```

![class 的 prototype](https://user-gold-cdn.xitu.io/2019/12/9/16ee850f39abc3bc?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

如上图所示，`A` 仍有 `prototype` 属性，并且定义中除了 `constructor` 函数，其他的函数都在 `prototype` 属性内。

可以认为是 `ES5 function` 语法糖吧，至少这里可以这么认为，但请不要认定，相比较于 `ES5 function` 还是有差别的，这块内容不属于本篇范畴，有机会单独写一篇讨论讨论。

## 对象的创建

绕了一圈，又绕了回来，现在我们回过头来看看对象的创建。

通过以上的阐述，我们知道了以下几点：

1. 对象是一些列`属性 & 数据` 的集合。
2. 原型是对象下的一个属性，它的值是一个对象（或`null`）。

那好，现在我在问一个问题，你用以下代码创建的对象，它的原型是什么？

```
let obj = {};
复制代码
```

有点疑惑？因为它既没有主动添加原型，也不是从类创建的对象，那他的原型就没有了？

答案当然是否定的，只要你把这段代码贴到 `Chrome` 控制台就可以了。想要进一步知道这个对象到底哪儿来的，试试以下代码：

```
obj.__proto__ === Object.prototype; // true
复制代码
```

很明显，这个新创建的对象为 `Object` 这个类的 `prototype` 属性，难不成这个 `obj` 是通过 `Object` 类创建的？

`bingo ~` 你离真相又进了一步，在 `JavaScript` 中所有的对象都由 `Object` 所创建（PS：不管你用什么姿势！）。

好，对象的直接创建弄明白了，在来个间接创建的问题，请问以下代码所创建的 `obj` 的原型是什么？

```
function A (){};
let obj = new A();
复制代码
```

当然是 `A` 的 `prototype` 啊，这还用问？那 `A` 的 `prototype` 又是什么呢？

放到 `Chrome` 下一看便知：

![function 的 prototype](https://user-gold-cdn.xitu.io/2019/12/9/16ee850f4ed909b6?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

由上图可见，是一个简单的对象，最原始的 `prototype` 其中仅仅包含了 `constructor` 和 `__proto__`。

`constructor` 就是引用它自己。

```
A.prototype.constructor === A; // true
复制代码
```

`__proto__` 就是这个对象的原型，只要是一个对象，自然而然会有这个属性，那么这个属性的值是什么？

```
A.prototype.__proto__ === Object.prototype; // true
复制代码
```

当然是 `Object.prototype` 啊，所有对象都由 `Object` 这个类所创建嘛！

作者：斑码
链接：https://juejin.im/post/6844904016367845389
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## 前言

这次的 `why what or how` 主题：`JavaScript` 对象 `&` 原型。

此类文章在百度上一搜一大把，其实不用再写了，但是本着把这个问题解释的清清楚楚明明白白，还是开始写了。

原因如下：

1. 面试宝典类文章，弄张图片一糊弄，让人觉得自己理解了。
2. 解释类文章，告诉你一堆语法，对语法一顿解释，告诉你就是这样的，还是没说清楚。
3. 很少有文章单独解释这个点！但这个点是基础！真的很重要！

所以本篇文章想说一说`对象 & 原型`，但为了确保能顺利理解，请先看完 [`JS 变量存储？栈 & 堆？NONONO!`](https://blog.acohome.cn/js-variable-in-memory/)，因为该篇文章从变量存储的角度来解释 `JavaScript 对象 & 原型`，请确保看完，在看这篇文章。

## 什么是对象？

既然要说清楚，先问一个最最基本的问题：什么是对象？

对象一句话就能解释清楚：

> 对象是一系列属性 & 数据的集合。

在 `JavaScript` 中创建一个对象的方式有很多，常见的如下：

```
// 字面量直接创建
let obj1 = {foo: 'bar'};

// 通过实例化 Object 类
let obj2 = new Object({foo: 'bar'});

class A {
    constructor(foo){
        this.foo = foo;
    }
}

// 通过自定义类创建
let obj3 = new A('bar');
复制代码
```

以上代码最终都产生了 `{foo: 'bar'}` 这个对象。对象下有一个叫 `foo` 的属性，它的值为 `bar`。那么它在堆中是如何存储的呢？

![JavaScript 下对象的存储](https://user-gold-cdn.xitu.io/2019/12/9/16ee850f391b7f82?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

看过了 [`JS 变量存储？栈 & 堆？NONONO!`](https://blog.acohome.cn/js-variable-in-memory/) 相信大家对于上图应该不陌生。

## 对象的取值

我们继续看一个较为复杂的对象（对象下某个属性也是一个对象）：

```
let complexObj = {
    num: 1,
    str: 'string',
    obj: {
        foo: 'bar',
    }
}
复制代码
```

在内存中的模型如下：

![复杂对象模型](https://user-gold-cdn.xitu.io/2019/12/9/16ee850f392208b0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

我们模拟一下 `complexObj.obj.foo` 这个取值过程。

![复杂对象取值过程](https://user-gold-cdn.xitu.io/2019/12/9/16ee850f39ea9b9a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

取值过程：碰到存储的值是地址值时，就到相应的地址值继续进行操作。

对象，总的来说，知识点有两点：

1. 创建对象：在内存堆中开辟一块用于存储一系列`属性 & 数据` 的空间。
2. 对象取值：根据存储的数据取值，如果是地址值，则到相应内存堆中继续进行操作。

对象到这里就差不多了，那原型又是什么呢？

## 原型 & 原型链

> 原型是对象下的一个属性，每个对象都有，同时原型也是一个对象。

文本的描述总是不直观的，我们通过代码来看：

```
let proto = {
    foo: 'bar'
}

let obj = {
    // ...
    __proto__: proto
}
复制代码
```

设置对象 `__proto__` 属性的过程，就是给对象设置了原型，同时该属性指向一个对象。

**注：** 当然 `ES6` 已经不建议这么做了，有专门的方法（`setPrototypeOf`）设置原型，为了解释方便，这里用 `ES5` 的代码作为示例。

有人可能会有疑问：既然是赋值操作，那应该也可以给对象的原型设置为非对象，为什么原型是对象呢？关于这个问题的答案，可以用以下代码测试：

```
let a = {};
a.__proto__ = 1;
console.log(a.__proto__);
复制代码
```

复制到浏览器即可看到效果，将对象的原型设置为非对象这个操作，是被禁止的，也就是说你设置了也没用。

原型的定义也很简单，那么原型是干嘛用的呢？

## 原型的作用

> 原型补充了原对象，当需要访问对象中属性，但该属性又不存在时，就会去原型上寻找。

按照上面的例子，`obj.foo` 返回什么？以下伪代码就是寻值的过程：

```
function getValue(obj, attr){
    let searchObj = obj
    while(searchObj 下不存在 attr 属性){
        searchObj = obj 的原型
    }
    return searchObj 下的 attr 属性 
}

getValue(obj, 'foo')
复制代码
```

以下为图示过程：

![获取原型上的属性](https://user-gold-cdn.xitu.io/2019/12/9/16ee850f4eb58ba5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

根据上图（或是伪代码）我们可得知最终的结果是： `bar`。

那如果对象上的原型还是没有该属性呢？

在原型的定义中，提到过：原型同时也是一个对象。转换一下思路，那么这个问题就变成了：如果对象上没有该属性那会发生什么？

去对象下的原型下找！对！我们刚说完！

这种层层递进的关系我们就把它称为：原型链！

到这你可能会问：如果按照这样一直找下去，那不是无穷无尽了？这就需要引出一个特殊的对象，可以在 `Chrome` 的控制台打印 `Object.prototype` 输出的对象，你可以仔细找找，该对象有没有原型？

`OH NO!` 竟然有原型，你个骗子。

不要激动，继续去查看它原型是什么：`null`！

这和我之前说的：原型也是一个对象，有出入，因为 `null` 明显不是一个对象。

但是，`typeof null` 确实是 `object`，笔者也有猜想过是不是和这有关系。但猜想是猜想，为了语义的完整性，我们重新定义一下原型：

> 原型是对象下的一个属性，每个对象都有，同时原型是对象或是 `null`。

同时定义一下原型链的特点：

> 原型链由一个个对象组成，原型链有终点，这个终点是 `null`。

`OK` 既然原型链有了终点，那么如果一个属性在原对象和所有的原型链上都不存在的话，他的值是什么？

`undefined`（未定义）啊！

现在我们已经清楚原型和对象的关系，那如何设置对象的原型呢？

## 设置原型

其实上面已经提到过，我们可以直接设置对象的原型：

### ES5 中

```
let proto = {
    foo: 'bar'
}

let obj = {
    // ...
    __proto__: proto
}
复制代码
```

### ES6 中

由于 `__proto__` 是非标准属性，因此在 `ES6` 中建议使用 `setPrototypeOf` 设置对象的原型。

```
let obj = {};

let proto = {
    foo: 'bar'
}

Object.setPrototypeOf(obj, proto);
复制代码
```

那如果一个对象还没有生成，比如仅仅定义了一个类，但又想控制通过这个类生成的对象的原型，该如何呢？

既然问了，那肯定是有的，解决方案就是函数的 `prototype` 属性。

## prototype

我们都知道在 `JavaScript` 中函数也是一个对象，这个特殊对象下有一个 `prototype` 属性，是干嘛用的呢？

> 在使用 `new` 关键字调用该函数时，函数下的 `prototype` 属性所保存的对象就是生成对象的原型。

通过代码来解释：

```
function A(bar) {
    this.bar = bar;
}

A.prototype.test = function(){
    console.log('test');
}
A.prototype.testAttr = 'testAttr';

let a = new A('bar');
复制代码
```

下面用伪代码来解释 `new A('bar')` 这个过程：

```
function fakeNew(A, bar){
    // 生成 this 为一个空对象。
    let this = {};
    Object.setPrototypeOf(this, A.prototype);
  
    // 执行 A 函数内的代码
    this.foo = bar;
  
    // 将 this 返回
    return this;
}

fakeNew(A, 'bar');
复制代码
```

相信大家看完代码就能理解 `prototype` 这个属性的作用了，但这是 `ES5` 的代码，`ES6` 中并不建议直接写 `prototype` ，而直接使用 `class`，但其本质是一样的。复制下面代码到 `Chrome` 里即可查到真相：

```
class A {
    constructor(bar) {
        this.foo = bar;
    }
    test() {
        console.log('test');
    }
}

console.dir(A);
复制代码
```

![class 的 prototype](https://user-gold-cdn.xitu.io/2019/12/9/16ee850f39abc3bc?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

如上图所示，`A` 仍有 `prototype` 属性，并且定义中除了 `constructor` 函数，其他的函数都在 `prototype` 属性内。

可以认为是 `ES5 function` 语法糖吧，至少这里可以这么认为，但请不要认定，相比较于 `ES5 function` 还是有差别的，这块内容不属于本篇范畴，有机会单独写一篇讨论讨论。

## 对象的创建

绕了一圈，又绕了回来，现在我们回过头来看看对象的创建。

通过以上的阐述，我们知道了以下几点：

1. 对象是一些列`属性 & 数据` 的集合。
2. 原型是对象下的一个属性，它的值是一个对象（或`null`）。

那好，现在我在问一个问题，你用以下代码创建的对象，它的原型是什么？

```
let obj = {};
复制代码
```

有点疑惑？因为它既没有主动添加原型，也不是从类创建的对象，那他的原型就没有了？

答案当然是否定的，只要你把这段代码贴到 `Chrome` 控制台就可以了。想要进一步知道这个对象到底哪儿来的，试试以下代码：

```
obj.__proto__ === Object.prototype; // true
复制代码
```

很明显，这个新创建的对象为 `Object` 这个类的 `prototype` 属性，难不成这个 `obj` 是通过 `Object` 类创建的？

`bingo ~` 你离真相又进了一步，在 `JavaScript` 中所有的对象都由 `Object` 所创建（PS：不管你用什么姿势！）。

好，对象的直接创建弄明白了，在来个间接创建的问题，请问以下代码所创建的 `obj` 的原型是什么？

```
function A (){};
let obj = new A();
复制代码
```

当然是 `A` 的 `prototype` 啊，这还用问？那 `A` 的 `prototype` 又是什么呢？

放到 `Chrome` 下一看便知：

![function 的 prototype](https://user-gold-cdn.xitu.io/2019/12/9/16ee850f4ed909b6?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

由上图可见，是一个简单的对象，最原始的 `prototype` 其中仅仅包含了 `constructor` 和 `__proto__`。

`constructor` 就是引用它自己。

```
A.prototype.constructor === A; // true
复制代码
```

`__proto__` 就是这个对象的原型，只要是一个对象，自然而然会有这个属性，那么这个属性的值是什么？

```
A.prototype.__proto__ === Object.prototype; // true
复制代码
```

当然是 `Object.prototype` 啊，所有对象都由 `Object` 这个类所创建嘛！


{: id="20201117114453-osch393" type="doc"}

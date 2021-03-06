首先，来个一个简单的问题。下列哪段代码会产生错误：

第一个创建实例，然后定义使用的类：

```
new Car('red'); // 是否会报错?

class Car {
  constructor(color) {
    this.color = color;
  }
}
复制代码
```

或者先于函数定义之前调用函数：

```
greet('World'); // 是否会报错?

function greet(who) {
  return `Hello, ${who}!`;
}   
复制代码
```

正确的答案是：第一个代码片段会报 `ReferenceError: Cannot access 'Car' before initialization` 错误。第二个代码正常运行。

如果你的答案与上述不同，或者你在不知道这背后的原理是什么而进行了猜测，那么你需要掌握**临时死区（TDZ）**的知识。

**TDZ** 管理 `let`、`const` 和 `class` 语法的可用性。变量在 JS 中的工作方式非常重要。

## 1.什么是临时死区

咱们先从一个简单的 `const` 变量声明开始。 首先声明并初始化变量，然后访问它，一切正常运行：

```
const white = '#FFFFFF';

white; // => '#FFFFFF'
复制代码
```

那如果在 声明之前访问 `white` 变量，会怎么样》

```
white; // throws `ReferenceError`

const white = '#FFFFFF';

white;
```

在 `const white = '#FFFFFF'` 语句之前的代码行中，变量 `white` 位于**临时死区**。

在 **TDZ** 中访问 `white` 后，JS 抛出`ReferenceError: Cannot access 'white' before initialization`

![](https://user-gold-cdn.xitu.io/2020/4/3/1713d5bb26e0e4d0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

> 临时死区语义禁止在变量声明之前访问它。它加强了顺序：在声明之前不要使用任何东西。

## 2.受 TDZ 影响的声明

来看看受 TDZ 影响的声明。

#### 2.1 const 变量

如前所述，`const` 变量位于声明和初始化行之前的 **TDZ** 中：

```
// 无法工作
pi; // throws `ReferenceError`

const pi = 3.14;
```

咱们必须在声明之后使用 `const` 变量：

```
const pi = 3.14;

// Works!
pi; // => 3.14
```

#### 2.2 let 变量

在声明行之前，`let` 声明语句也会受到 TDZ 的影响：

```
// 无法工作
count; // throws `ReferenceError`

let count;

count = 10;
```

同样，仅在声明之后使用 `let` 变量：

```
let count;

// Works!
count; // => undefined

count = 10;

// Works!
count; // => 10
```

#### 2.3 class 的声明

正如在介绍中看到的，在定义 `class` 之前不能使用它：

```
// 无法工作
const myNissan = new Car('red'); // throws `ReferenceError`

class Car {
  constructor(color) {
    this.color = color;
  }
}
```

#### 2.4 构造函数内部的 super()

如果在构造函数中调用 `super()`之前扩展父类，则此绑定位于 TDZ 中。

```
class MuscleCar extends Car {
  constructor(color, power) {
    this.power = power;
    super(color);
  }
}

// Does not work!
const myCar = new MuscleCar('blue', '300HP'); // `ReferenceError`
```

在构造 `constructor()` 中，在调用`super()`之前不能使用 `this`。

TDZ 建议调用父构造函数来初始化实例。这样做之后，实例就准备好了，就可以在子构造函数中进行调整。

```
class MuscleCar extends Car {
  constructor(color, power) {
    super(color);
    this.power = power;
  }
}

// Works!
const myCar = new MuscleCar('blue', '300HP');
myCar.power; // => '300HP'
复制代码
```

#### 2.5 默认函数参数

默认参数存在于一个中间作用域中，与全局作用域和函数作用域分离。默认参数也遵循 TDZ 限制。

```
const a = 2;
function square(a = a) {
  return a * a;
}
// Does not work!
square(); // throws `ReferenceError`
```

在声明表达式 `a = a`之前，在表达式的右侧使用参数 `a`，这将生成关于 `a` 的引用错误。

确保在声明和初始化之后使用默认参数。 咱们可以使用一个特殊的变量 `init`，该变量在使用前已初始化：

```
const init = 2;
function square(a = init) {
  return a * a;
}
// Works!
square(); // => 4
复制代码
```

## 3.var, function, import 语句

与上述陈述相反，`var` 和 `function` 定义不受 TDZ 的影响。它们被提升到当前的作用域顶部。

如果在声明之前访问 `var` 变量，则只会得到一个 `undefined` 的变量

```
// 正常运行, 但不要这样做！
value; // => undefined

var value;
复制代码
```

但是，可以根据函数的定义位置来使用它：

```
// 正常工作
greet('World'); // => 'Hello, World!'

function greet(who) {
  return `Hello, ${who}!`;
}

// 正常工作
greet('Earth'); // => 'Hello, Earth!'
复制代码
```

通常，咱们一般对函数的实现不太感兴趣，而只是想调用它。 因此，有时在定义函数之前先调用该函数是有意义的。

有趣的是，import 模块也被提升了。

```
// 正常工作
myFunction();

import { myFunction } from './myModule';
复制代码
```

当然，建议将 `import` 写在文件开头，以便读写方法。

## 4. TDZ 中的 typeof 行为

`typeof` 操作符用于确定是否在当前作用域内定义了变量。

例如，未定义变量 `notDefined`。对该变量应用 `typeof` 操作符不会引发错误：

```
typeof notDefined; // => 'undefined'
复制代码
```

因为变量没有定义，所以 `typeof notDefined` 的值为 `undefined`。

但是 `typeof` 操作符在与临时死区中的变量一起使用时具有不同的行为。在本例中，JS 抛出一个错误：

```
typeof variable; // throws `ReferenceError`

let variable;
复制代码
```

此引用错误背后的原因是您可以静态地(仅通过查看代码)确定已经定义了该变量。

## 5. TDZ 在当前作用域内采取行动

临时死区在声明语句所在的作用域内影响变量。

![](https://user-gold-cdn.xitu.io/2020/4/3/1713d5bed4592351?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

来看看例子：

```
function doSomething(someVal) {
  // 函数作用域
  typeof variable; // => undefined
  if (someVal) {
    // 内部块使用域
    typeof variable; // throws `ReferenceError`
    let variable;
  }
}
doSomething(true);
复制代码
```

有 2 个作用域：

* 函数作用域
* 定义 let 变量的内部块作用域

在函数作用域中，`typeof variable` 的计算结果为 `undefined`。在这里，`let` 变量语句的 TDZ 没有作用。

在内部作用域中，`typeof variable` 语句在声明之前使用一个变量，抛出一个错误。`ReferenceError:在初始化之前不能访问‘variable’`，TDZ 只存在于这个内部作用域内。

## 6.总结

TDZ 是影响 `const`、`let` 和 `class` 语句可用性的重要概念。它不允许在声明之前使用变量。相反，可以在声明之前使用 `var` 变量时，`var` 变量会继承较旧的行为，应该避免这样做。
{: id="20201116142842-9u1iqiw"}


{: id="20201116142345-pmelbaj" type="doc"}

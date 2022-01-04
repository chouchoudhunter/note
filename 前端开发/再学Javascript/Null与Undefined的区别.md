### 一、基本数据类型

在介绍 undefined 与 null 之前，我们先来了解一下 ECMAScript 中的数据类型。在 ECMAScript 中有六种简单数据类型(也称为基本数据类型): Undefined、Null、Boolean、Number 和 String、Symbol (ES6 中引入) 。还有一种复杂数据类型——Object。

Undefined 和 Null 都只有一个值，分别对应着 undefined 和 null。这两种不同类型的值，既有着不同的语义和场景，又表现出较为相似的行为。

### 二、undefined

undefined 的字面意思就是：未定义的值 。这个值的语义是，希望表示一个变量最原始的状态，而非人为操作的结果 。 这种原始状态会在以下 4 种场景中出现：

#### **1、声明一个变量，但是没有赋值**

```javascript
var foo;
console.log(foo); // undefined
```

访问 foo，返回了 undefined，表示这个变量自从声明了以后，就从来没有使用过，也没有定义过任何有效的值。

#### **2、访问对象上不存在的属性或者未定义的变量**

```
console.log(Object.foo); // undefined
console.log(typeof demo); // undefined
```

访问 Object 对象上的 foo 属性，返回 undefined ， 表示 Object 上不存在或者没有定义名为 foo 的属性；对未声明的变量执行 typeof 操作符返回了 undefined 值。

#### **3、函数定义了形参，但没有传递实参**

```
//函数定义了形参 a
function fn(a) {
    console.log(a); // undefined
}
fn(); //未传递实参
```

函数 fn 定义了形参 a，但 fn 被调用时没有传递参数，因此，fn 运行时的参数 a 就是一个原始的、未被赋值的变量。

#### **4、使用 void 对表达式求值**

```
void 0 ; // undefined
void false; // undefined
void []; // undefined
void null; // undefined
void function fn(){} ; // undefined
```

ECMAScript 明确规定 void 操作符 对任何表达式求值都返回 undefined ，这和函数执行操作后没有返回值的作用是一样的，JavaScript 中的函数都有返回值，当没有 return 操作时，就默认返回一个原始的状态值，这个值就是 undefined，表明函数的返回值未被定义。

因此，undefined 一般都来自于某个表达式最原始的状态值，不是人为操作的结果。当然，你也可以手动给一个变量赋值 undefined，但这样做没有意义，因为一个变量不赋值就是 undefined 。

### 三、null

null 的字面意思是：空值  。这个值的语义是，希望表示一个对象被人为的重置为空对象，而非一个变量最原始的状态 。 在内存里的表示就是，栈中的变量没有指向堆中的内存对象。

#### **1、一般在以下两种情况下我们会将变量赋值为 null**

* 如果定义的变量在将来用于保存对象，那么最好将该变量初始化为 null，而不是其他值。换句话说，只要意在保存对象的变量还没有真正保存对象，就应该明确地让该变量保存 null 值，这样有助于进一步区分 null 和 undefined。
* 当一个数据不再需要使用时，我们最好通过将其值设置为 null 来释放其引用，这个做法叫做解除引用。不过解除一个值的引用并不意味着自动回收改值所占用的内存。解除引用的真正作用是让值脱离执行环境，以便垃圾收集器在下次运行时将其回收。解除引用还有助于消除有可能出现的循环引用的情况。这一做法适用于大多数全局变量和全局对象的属性，局部变量会在它们离开执行环境时(函数执行完时)自动被解除引用。

#### **2、特殊的 typeof null**

当我们使用 typeof 操作符检测 null 值，我们理所应当地认为应该返"Null"类型呀，但是事实返回的类型却是"object"。

```
var data = null;
console.log(typeof data); // "object"
```

是不是很奇怪？其实我们可以从两方面来理解这个结果：

* 一方面从逻辑角度来看，null 值表示一个空对象指针，它代表的其实就是一个空对象，所以使用 typeof 操作符检测时返回"object"也是可以理解的。
* 另一方面，其实在 JavaScript 最初的实现中，JavaScript 中的值是由一个表示类型的标签和实际数据值表示的(对象的类型标签是 0)。由于 null 代表的是空指针（大多数平台下值为 0x00），因此，null 的类型标签也成为了 0，typeof null 就错误的返回了"object"。在 ES6 中，当时曾经有提案为历史平凡， 将 type null 的值纠正为 null, 但最后提案被拒了，所以还是保持"object"类型。

### 四、总结

用一句话总结两者的区别就是：undefined 表示一个变量自然的、最原始的状态值，而 null 则表示一个变量被人为的设置为空对象，而不是原始状态。所以，在实际使用过程中，为了保证变量所代表的语义，不要对一个变量显式的赋值 undefined，当需要释放一个对象时，直接赋值为 null 即可。


{: id="20201116165045-kiffv20" type="doc"}

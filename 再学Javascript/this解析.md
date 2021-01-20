# 函数中的 this
{: id="20210107144829-7kictkz"}

ES6 中新增了箭头函数这种语法，箭头函数以其简洁性和方便获取 this 的特性。下面来总结一下他们之间的区别：
{: id="20210107144829-v3jaicq"}

普通函数下的 this:
{: id="20210107144829-0j29d9m"}

* {: id="20210107144829-gvirj31"}在**普通函数中的 this 总是代表它的直接调用者**，在默认情况下，this 指的是 window，
* {: id="20210107144829-ii3rpnz"}在严格模式下，没有直接调用者的函数中的 this 是 undefined 使用
* {: id="20210107144829-hbsydtp"}call,apply,bind(ES5 新增)绑定的，this 指的是 绑定的对象
{: id="20210107144829-wgnkwn4"}

箭头函数中的 this:
{: id="20210107144829-xt91w13"}

* {: id="20210107144829-zufh69e"}箭头函数没有自己的 this, 它的 this 是继承而来; 默认**指向在定义它时所处的对象(宿主对象),**
* {: id="20210107144829-7vzcuru"}而不是执行时的对象， 定义它的时候，可能环境是 window，也有可能是其他的。
{: id="20210107144829-82qqqpf"}

##### 普通函数
{: id="20210107144829-uck24lr"}

```javascript
function test() { 
   console.log(this);  
 }  
 test();  //window
```
{: id="20210107144829-520c8qm"}

test 是一个全局函数，也就是挂载在 window 对象下的 test()等价于 window.test();
{: id="20210107144829-babaqfz"}

```javascript
var obj = {  
   func: function () {  
     console.log(this); 
     setTimeout(function () {   
       console.log(this);
     },0);   
   }  
 }  
 obj.func(); 
// obj,此时的this是obj对象  
// window
```
{: id="20210107144829-kliubue"}

func 的宿主环境是 obj，所以 func 里面的 this 是 obj。 **定时器中的函数，由于没有默认的宿主对象，所以 this 指向 window**
{: id="20210107144829-m7qav9d"}

严格模式下的 this:
{: id="20210107144829-bqm3i3i"}

```javascript
function test() {  
   'use strict';  
   console.log(this); 
 }  
 test(); 
 //undefined   
```
{: id="20210107144829-3omtblk"}

##### 严格模式
{: id="20210107144829-4f8fql6"}

在严格模式下，没有直接调用者的函数中的 this 是 undefined
{: id="20210107144829-admf1hg"}

```javascript
"use strict";   
    var obj={   
      say:function(){   
        console.log(this);
      }  
    };  
obj.say();  
//obj  
```
{: id="20210107144829-iperrb0"}

有直接调用者的 this 是它的调用者
{: id="20210107144829-rl9f877"}

##### 箭头函数
{: id="20210107144829-8hilbrf"}

```javascript
var obj = {  
   say: function () {  
     setTimeout(() => {  
       console.log(this);
     });  
   }  
 }  
 obj.say(); 
 // obj  
```
{: id="20210107144829-jei8ry9"}

此时的 this 继承自 obj, 指的是定义它的对象 obj, 而不是 window
{: id="20210107144829-yp1roj6"}

```javascript
var obj = {  
 say: function () {  
   var f1 = () => {  
     console.log(this); // obj  
     setTimeout(() => {  
       console.log(this); // obj  
     })  
   }  
   f1();  
 }  
}   
obj.say() 
```
{: id="20210107144829-l2ybsh2"}

因为 f1 定义时所处的函数 中的 this 是指的 obj, setTimeout 中的箭头函数 this 继承自 f1，所以不管有多层嵌套，都是 obj
{: id="20210107144829-d0oy2ey"}

```javascript
var obj = {  
 say: function () {  
   var f1 = function () {  
     console.log(this);  
     setTimeout(() => {  
       console.log(this); 
   })  
   };  
   f1();  
 }  
}  
obj.say()
// window, f1调用时,没有宿主对象,默认是window 
// window  
```
{: id="20210107144829-kh95xl4"}

箭头函数没有 this 的绑定，必须通过查找作用链来决定其值。如果箭头函数被非箭头函数包含，则 this 绑定的是最近以层的非箭头函数的 this；否则。this 的值会被设置为 undefined。 箭头函数在定义的时候它所处的环境相当于是 window, 所以在箭头函数内部的 this 函数 window
{: id="20210107144829-ym83jtr"}


{: id="20201116113450-alge3qj" type="doc"}

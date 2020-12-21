## 1.bind（）

```javascript
window.a=3
function test(){
	console.log(this.a)
}
test.bind({a:10})()
//输出10而不是3，bind让函数动态绑定了一个对象，this指向这个对象
```

## 2.let

1. let 声明的变量是具有块级作用域的`{}`
2. let 声明的全局变量不能通过全局对象的属性来访问

   ```javascript
   let c=4;
   console.log(window.c)//错误
   ```
3. let 不能重复定义一个变量
4. 不能做变量的提升


{: id="20201027224811-f4nfmbt" type="doc"}

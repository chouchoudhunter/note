# 在H5中

在文档元素上挂载属性

```javascript
window.document.documentElement.setAttribute('data-theme', 'light')
```

通过css选择器限定属性值，使用scss混入

```scss
@mixin background-color {
  [data-theme="dark"] & {
    background-color: black;
  }

  [data-theme="light"] & {
    background-color: rgb(255, 255, 255);
  }
}
```

# uni-app 解决方案

vuex

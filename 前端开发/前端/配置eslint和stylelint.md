# 配置Eslint

### setting.json

```javascript
"editor.codeActionsOnSave": {
    "source.fixAll": true,//同时开启eslint和stylelint
},
"eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript"
],
"eslint.alwaysShowStatus": true,
"stylelint.validate": [
    "css",
    "less",
    "postcss",
    "scss",
    "vue",
    "sass"
],
```

### 安装 eslint

`yarn add eslint -dev`

### 初始化eslint

注意根目录下不能有eslint的配置文件（这一步必须做，会自动安装所需模块）

`eslint --init`

### 注意

如果此时保存没有格式化就右键一下格式化就可以用了

# 配置Stylelint

### 注意版本14有重大更新，使用以下版本

`yarn add --dev stylelint@13.3.3 stylelint-config-standard@20.0.0`

### 在项目根目录建立 stylelint 配置文件 .stylelintrc.json ：

```json
{
  "extends": [
    "stylelint-config-standard"
  ]
}
```

### setting.json

```json
"stylelint.validate": [
    "css",
    "less",
    "postcss",
    "scss",
    "vue",
    "sass"
],
```


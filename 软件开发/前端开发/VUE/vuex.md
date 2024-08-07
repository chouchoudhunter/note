# Vuex

## 一、简单介绍

解决不同组件之间的数据共享，组件里数据持久化

### 1.定义 vuex

```javascript
import Vue frome 'vue'
import Vuex from 'vuex'

Vue.use(Vuex);
//state用于存储数据
var state={
	count:1;
}
//mutations里面放的是方法，用于改变state里的数据
var mutations={
	incCount(){
		++state.count
	}
}
//类似计算属性,改变state里面的count数据的时候会触发 getters里面的方法获取新的值
var getters= {
	computedCount: (state) => {
		return state.count*2
		    }
}
const store=new Vuex.Store{
	state:{
		state,
		mutations,
		getters
	}
}

export default store;
```

### 2.使用 vuex

1. 引入 store或在main.js挂载

   ```javascript
   import store from '../vuex/store.js';
   ```
2. 注册使用

   ```javascript
   export default{
   		data(){
   		    return {   
   		       msg:'我是一个home组件',
   			value1: null,
   
   		    }
   		},
   		store,
   		methods:{
   		    incCount(){
   
   			this.$store.commit('incCount');   /*触发 state里面的数据*/
   		    }
   
   		}
   	    }
   ```
3. 获取 state 里的数据

   ```javascript
   this.$store.state.数据
   ```
4. 触发 mutations 改变 state 里面的数据

   ```javascript
   this.$store.commit('incCount');
   ```

## 二、建议使用方法

### 1.目录结构

![image-20220408160312541](image-20220408160312541.png)

### 2.index.js

```javascript
import Vuex from 'vuex'
import Vue from 'vue'
import getters from './getters'
Vue.use(Vuex)

 const.moduleFiles=require.context('./modules', true, /\.js$/)//引入目录下所有模块
 const modules = modulesFiles.keys().reduce((modules, modulePath) => {
   // 根据文件名设置模块名
   const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/, '$1')
   const value = modulesFiles(modulePath)
   modules[moduleName] = value.default
   return modules
 }, {})
 
 const store = new Vuex.Store({
   modules,
   getters
 })
 
 export default store
```

### 3.getters.js

```javascript
const getters = {

}
export default getters
```

4.main.js

```javascript
import store from './store'

const app = new Vue({
    ...App,
	store
})
```


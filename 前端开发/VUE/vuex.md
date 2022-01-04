# Vuex

解决不同组件之间的数据共享，组件里数据持久化

> 定义 vuex

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

> 使用 vuex

1. 引入 store

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


{: id="20201026214657-yj0d2wk" type="doc"}

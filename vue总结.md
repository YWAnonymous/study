## vue资料总结

```markdown
基本上所有的markdown标记都是基于这四个符号或组合，需要注意的是，如果以基本符号开头的标记，注意基本符号后有一个用于分割标记符和内容的空格。

	每一个路由都是由“/”开头
# 建议不要直接使用 cnpm 安装以来，会有各种诡异的 bug。可以通过如下操作解决 npm 下载速度慢的问题
	npm install --registry=https://registry.npm.taobao.org
# vue 2升3
	npm uninstall vue-cli -g
	npm install -g @vue/cli
```

### 1、路由导航守位

```javascript
cosnt routes=[
    //这里面写配置的路由路径
];
const router = new VueRouter({ 
	routes;
})
// 挂载路由导航守卫,to表示将要访问的路径，from表示从哪里来，next是下一个要做的操作 next('/login')强制跳转login
router.beforeEach((to, from, next) => {
   // 访问登录页，放行
  if(to.name="/login") return next();
    //这里面可以获取token，判断是否登录
  if(!isAuthenticated) return next("/login");
   next();
});
export default router;
```

### 2、路由拦截器

```javascript
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
   // 为请求头对象，添加token验证的Authorization字段
  config.headers.Authorization = window.sessionStorage.getItem('token')
　　// 在发送请求之前做些什么
　　return config
}, function (error) {
　　// 对请求错误做些什么
return Promise.reject(error)
});
```

### 3、vue中路由守卫的问题

```javascript
Maximum call stack size exceeded （栈溢出了）

如果sessionStorage没有token 就进入登陆页
但是代码执行会引起死循环，原因是没有出口，执行next({path: "/login"})会再次执行全局前置导航守卫 

原因：进入了死循环
解决方案：
router.beforeEach((to, from, next) => {
  let token = window.sessionStorage.getItem('token');
  if (to.path != '/login' && !token) {
    next({
      path: '/login'
    })
  } else {
    if (to.path == '/login' && token) {
      next('/dashboard')
    } else {     
      next()
    }
  }
})
```

### 4、slot插槽

```markdown
# 父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。
<el-table :data="userList" stripe style="width: 100%">
    <el-table-column label="用户状态">
        <template slot-scope="scope">
        	{{ scope.row }}
        </template>
	</el-table-column>
</el-table>

slot-scope='scope' 作用域插槽中定义一个对象(这里对象被定义为scope)来存储插槽上绑定的数据的用法
scope.row 使用ElementUI表格模板渲染数据时使用

* 当前行数据的获取也会用到插槽,scope相当于一行的数据， scope.row相当于当前行的数据对象 *
# v-slot插槽
	1、匿名插槽：没有指定某一个插槽，全部显示
	2、具名插槽：指定插槽显示内容
	3、作用域插槽：可以进行数据绑定，父子组件通讯
		
		父组件：
            <slot v-bind:name="slotName"></slot>
		子组件：
			传给父组件：{columnType: "slot",slotName: "status"}
			
			<template v-slot:status="slotData">
				{{ slotData }}
			</template>
			v-slot:status   status是插槽 prop,对应父组件的name
```

### 5、vuex

```javascript
# vuex的核心特征---->store.js
	1、state是公共数据源
		1）this.$store.state.全局数据名称
		2）导入mapState函数：import {mapState} from 'vuex'
			computed:{
				...mapState(['全局数据名称'])
			}
	2、Mutaion:只有 mutations 中定义的函数，才有权利修改 state 中的数据
		#不要在 mutations 函数中，执行异步操作
        #commit 的作用，就是调用 某个 mutation 函数
        1）实例：mutations: {
            add(state,step){
                  //第一个形参永远都是state也就是$state对象
                  //第二个形参是调用add时传递的参数
                  state.count+=step;
                }
              }
		2) //使用commit函数调用mutations中的对应函数，
            //第一个参数就是我们要调用的mutations中的函数名
            //第二个参数就是传递给add函数的参数
            this.$store.commit('add',10)
		3)导入mapMutations函数：import { mapMutations } from 'vuex'
                methods:{
                  ...mapMutations(['add'])
                }
   	3、Action:
		# 这里的 dispatch 函数，专门用来触发 action
      	1)实例：actions: {
              addAsync(context,step){
                setTimeout(()=>{
                  context.commit('add',step);
                },2000)
              }
            }
         2) this.$store.dispatch('addAsync',5)
         3)导入mapActions函数：import { mapActions } from 'vuex'
            methods:{
              ...mapMutations(['subAsync'])
            }
   	4、getter：Getter用于对Store中的数据进行加工处理形成新的数据它只会包装Store中保存的数据，并不会修改Store中保存的数据，当Store中的数据发生变化时，Getter生成的内容也会随之变化
        1）$store.getters.showNum
        2）导入mapGetters函数：import { mapGetters } from 'vuex'
            computed:{
            ...mapGetters(['showNum'])
            }
```

### 6、watch

```markdown
# 普通监听和深度监听
	watch:{}：可监听数据，是vue中数据发生变化进行处理的函数，三个选项：
		一、handler:监听到变化时执行的函数。
		二、deep(true,false):是否深入监听。
		三、immediate(true,false):确认是否已当前的初始值执行hangler函数
1、普通监听
	watch:{
		firstName(newVal,oldVal){
			this.fullName = newVal;
		}
	}
2、深度监听
	watch:{
		firstName:{
			handler(newVal,oldVal){
				this.fullName = newVal;
			},
			// 代表在wacth里声明了firstName这个方法之后立即先去执行handler方法
    		//immediate: true
    		//deep的意思就是深入观察，监听器会一层层的往下遍历，给对象的所有属性都加上这个监听器
    		deep: true
		}
	}
```

### 7、组件

```js
# 父子组件（例el-dialog）
父组件： <Dialog  :flag.sync="DialogFlag"></Dialog>
	### :flag.sync 向子组件传递数据
	this.DialogFlag = true;
子组件： <el-dialog :visible.sync="dialog_info_flag"></el-dialog>
    props:{
        flag:{
            type:Boolean,
			default: false
        }
    },
    methods:{
      close(){
          // 向父组件中的 flag 传递数据
          this.$emit("update:flag",false);
         // this.$emit("flag",false);
      } 
    },
	watch: {
		// 监听父组件中字段flag
        flag: {
          handler: function(newVal, oldVal) {
            this.dialog_info_flag = newVal;
          },
          deep: true //深度监听
        }
  	}
```

### 8、浅拷贝和深拷贝

```markdown
# 浅拷贝
	Object.assign方法的第一个参数是目标对象，后面的参数都是源对象。
		Object.assign(target, source1, source2);
```

### 9、axios接口封装以及api的统一管理

```js
# request.js
import axios from "axios";
// 创建axios，赋给变量service
const service = axios.create({
    baseURL: 'http://127.0.0.1:8888/api/private/v1/',
    timeout: 15000,
  });
  //添加请求拦截器
export default service;
# login.js
import service from "@/utils/request";
 //api统一管理--post方法
export function Login(data){
    return service.request({
        method: "post",
        url: "/login/",
        data
    })
}
//get方法
export function GetUserList(id,params){
    return service.request({
        method: "get",
        url: "/users/"+id,
        params:params
    })
}
```


















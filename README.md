#初识 JavaScript 渐进式框架 Vue.js


## Vue 基础

1. 模板语法
2. 计算属性
3. 指令
4. 事件处理器
5. 表单控件
6. 生命周期


```javascript
var vm = new Vue({
	el:'#app',
	data:{
		message:'hello vue!!!'
	},
	computed:{
		messageReverse:function(){
			return this.message.split('').reverse().join('');
		},
		methods:{
			changeBgColor:function(ev){
				ev.target.style.background = 'red';
			}
		},
		mounted:function(){
			console.log(123);
		},
		updated:function(){
			console.log(456); // 数据发生改变时调用
		},
		watch:{
			message:function(news,olds){ // messgae 发生改变时调用此方法，打印旧值与新值
				console.log(news);
				console.log(olds);	
			}
		}
	}
});
```


## Vue 组件

1. 组件系统是将一个大型的界面切分成一个一个更小的可控单元
2. 组件时可复用的，可维护的
3. 组件具有强大的封装性，易于使用
4. 大型应用中，组件与组件之间交互是可以解耦操作的（组件通信）


#### 创建Vue组件

1. 全局组件
2. 局部组件
3. 组件数据的特点
4. Vue实例与Vue组件的关系



###### 全局组件

```javascript
Vue.component('my-header',{
        template:"<h1>全局组件</h1>"
    });
```

###### 局部组件

```javascript
var vm = new Vue({
        el:'#app',
        components:{
            'my-header':{
                template:'<h1>局部组件</h1>'
            }
        }
    });
```

###### 组件数据的特点

*数据模板之间的隔离*

```javascript
var vm = new Vue({
        el:'#app',
        data:{
            message:'Hello my header!!'
        },
        components:{
            'my-header':{
                template:'<h1 v-on:click="changeCount">{{count}}</h1>',
                data:function(){
                    return {
                        message:'Hello my header!!!',
                        count:0
                    };
                },
                methods:{
                    changeCount:function(){
                        this.count++;
                    }
                }
            }
        }
    });
```



---

###### 组件与 Vue 实例之间的关系

```html
<div id="ex">{{message}}</div>
```

```javascript
var myComponent = Vue.extend({
        data:function(){
            return {
                message:'Holle vue!'
            }
        }
    });

    var vm2 = new myComponent({
        el:'#ex'
    });
```



## Vue 组件的模板方式

1. \
2. `
3. `<template>`
4. `<script type="text/x-template">`
5. `.vue` 



###### template使用方式

```html
<template>
    <div>
        <h1>{{message}}</h1>
        <span>123</span>
    </div>
</template>
```

```javascript
var v = new Vue({
    el:'#app',
    data:{
        message:'Hello,world!'
    },
    template:"#tem"
});
```

---

##### `<script type="text/x-template">`使用方式

```html
<script type="text/x-template" id="temp">
	<div>
        <h1>{{message}}</h1>
        <span>123</span>
    </div>
</script>
```




## Vue 中父子组件通信

1. 父组件像子组件通信
		`Pass Pross`
2. 子组件像父组件通信
	`Emit Events`


###### 父组件像子组件通信

```html
<my-header :list="list"></my-header>
```

```javascript
var v = new Vue({
        el:'#app',
        data:{
            message:'Hello,world!',
            list:['one','two','three']
        },
        components:{
            'my-header':{
                template:`
                    <div>
                        <h1>{{message}}</h1>
                    <ul>
                        <li v-for="item in list">{{item}}</li>
                    </ul>
                    </div>
                `,
                data:function(){
                    return {
                        message:'Hello my header!!!'
                    }
                },
                props:['list']  // 父组件向子组件进行通信操作
            },
        }
    });
```

** 组件之间通信的数据验证 **

```javascript
 props:{
         'list':{
                type:Array,
                default:function(){   // 数组需要使用工厂模式返回数据
					return ['1','2','3']
					}
                }
        }
```


##### 子组件像父组件通信


**定义一个方法，并自定义一个事件**

`this.$emit('changeEvents',ev.target.innerHTML);`


```javascript
'my-nav':{
                        template:`
                               <ul>
                                    <li v-on:click="getContent" v-for="item in list">{{item}}</li>
                               </ul>
                        `,
                        props:['list'],
                        methods:{
                            getContent:function(ev){
                                //console.log(ev.target.innerHTML);
                                this.$emit('changeEvents',ev.target.innerHTML);
                            }
                        }
                    }
```


**父组件接收数据**

*监听这个事件,触发 getChildContent方法*

```html
<my-nav v-on:changeEvents="getChildContent" :list="list"></my-nav>
```

```javascript
methods:{
                    getChildContent:function(str){
                        //console.log(str);
                        //this.nowMessage = str;  // 数据操作
                        this.$refs.my_title.innerHTML = str;  // DOM操作
                    }
                }
```



**补充**
*DOM选择操作*

`<h1 ref="my_title">{{nowMessage}}</h1>`
`this.$refs.my_title.innerHTML = str;`





## Vue 组件注意事项

1. 正确的嵌套方式与 `is` 属性
2. 单向数据流
3. 数据的作用域
4. 原生事件 `.native`
5. `$refs`



### 正确的嵌套方式与 `is` 属性

```htmlbars
<table>
	<!-- <my-header></my-header> -->
	<tr is="my-header"></tr>
</table>
```

### 原生事件 `.native`

```htmlbars
<my-header @click.native="show"></my-header>
```


### `$refs` DOM选择

*DOM选择操作*

`<h1 ref="my_title">{{nowMessage}}</h1>`
`this.$refs.my_title.innerHTML = str;`



## Vue 中非父子组件通信

1. 空实例与自定义事件
	`$emit`	
	`$on`
2. *Vuex*状态管理
	`state`
	`mutations`
	`commit`
	

#### 空实例与自定义事件

1. 定义一个空实例

```javascript
var busVm = new Vue();
```

2. 数据发送端定义`$emit`方法

```javascript
busVm.$emit('changeEvents',ev.target.innerHTML)
```

3. 接收

```javascript
mounted:function(){
          busVm.$on('changeEvents',function(str){
            //console.log(str);
            this.message = str;
          }.bind(this));  // 绑定到当前this
          }
```



---

### *Vuex*状态管理

1. 引入`Vuex.js` 文件，具体还可以参考Vue官网Vuex生态社区
		`<script src="https://cdn.bootcss.com/vuex/2.5.0/vuex.js"></script>`

2. 创建`Vuex`实例

```javascript
var store = new Vuex.Store({
        state:{
            count:0
        },
        mutations:{
            add:function(state){
                state.count++;
            },
            remove:function(state){
                state.count--;
            }
        }
    });
```


3. 使用计算属性`computed`渲染`Vuex`中的状态属性
```javascript
computed:{
            count:function(){
                return store.state.count;
            }
        },
```


4. 使用`commit`调用`Vuex`中的方法

```javascript
store.commit('add');
```
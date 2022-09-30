# Vue 第七天

---



## 1. 组件通讯-$children与 $parent



**知识点：**



1. 使用`$parent`可以让组件访问父组件的实例

   - 通过`$parent`访问到的是上一级父组件的实例
   - 在根组件`App.vue`上拿 `$parent` 得到的是 `new Vue()` 的实例
   - 在 `new Vue()` 中使用 `$parent` 得到的是 `undefined`

   

2. 使用`$children` 可以访问所有子组件的实例，并返回由所有子组件的实例组成的数组

   - `$children`并不能保证顺序
   - **需要注意 `$children` 并不保证顺序，也不是响应式的**



**落地代码：**

```vue
<template>
  <div class="children">
    <p>子组件</p>
    <p>获取到 parent 中的数据: {{ parentData }}</p>
  </div>
</template>

<script>
  export default {
    name: 'VChildren',
    data() {
      return {
        parentData: ''
      }
    },
    mounted () {
      this.parentData = this.$parent.name
    }
  }
</script>
```

```vue
<template>
  <div class="app">
    根组件

    <Children />

    <button @click="change">点击改变子组件值</button>
  </div>
</template>

<script>
  import Children from './components/Children.vue'

  export default {
    name: 'App',
    components: {
      Children
    },
    data() {
      return {
        name: 'Tom'
      }
    },
    methods: {
      change () {
        this.$children[0].sonData = 'child' 
      }
    }
  }
</script>
```









##2. 组件通讯-provide与 inject



**知识点：**



`provide` 与  `inject` 传值的方式在 `Vue` 中叫做**依赖注入**，这对选项需要一起使用，以允许一个祖先组件向其所有子孙后代注入一个依赖，不论组件层次有多深，如果你熟悉 React，这与 React 的上下文特性很相似。



`provide` 选项应该是一个对象或返回一个对象的函数，该对象包含可注入其子孙的 property

`inject` 选项一般是一个数组，每一项为 `provide`  提供的数据



**落地代码：**

```vue
<template>
  <div class="app">
    根组件
    <Children />
  </div>
</template>

<script>
  import Children from './components/Children.vue'

  export default {
    name: 'App',
    components: {
      Children
    },
    provide() {
      return {
        myName: '亚瑟'
      }
    }
  }
</script>

```

```vue
<template>
  <div class="children">
    <p>子组件-{{ myName }}</p>
  </div>
</template>

<script>
  export default {
    name: 'VChildren',
    inject: ['myName'],
  }
</script>

```









##3. 组件通讯-$attr与 $listeners



**知识点：**



`$attrs / $listeners`，实现组件之间的跨代通信



1. 先来看一下`inheritAttrs`

   - 默认值 `true`，继承所有的父组件属性除`props`之外的所有属性
   - `inheritAttrs：false` 只继承class属性 

   

2. `$attrs`：继承所有的父组件属性（除了prop传递的属性、class 和 style ），一般用在子组件的子元素上，并且可以通过 v-bind="$attrs" 传入内部组件。

   

3. `$listeners`：该属性是一个对象，里面包含了作用在这个组件上的所有监听器，可以配合 `v-on="$listeners"` 将所有的事件监听器指向这个组件的某个特定的子元素，即可以通过 `v-on="$listeners" `传入内部组件





**落地代码：**



App.vue

```vue
<template>
  <div>
    <Child1
      :name="name"
      :age="age"
      :gender="gender"
      :height="height"
      @getData="getDataFunc"
      class="box"
    ></Child1>
  </div>
</template>

<script>
import Child1 from './components/Child1.vue'

export default {
  components: { Child1 },
  data() {
    return {
      name: 'Tom',
      age: '3',
      gender: '男',
      height: '180'
    };
  },
  methods: {
    getDataFunc () {
      console.log('111')
    }
  }
};
</script>
```



child1.vue

```vue
<template class="border">
  <div>
    <p>name: {{ name}}</p>
    <p>childCom1的$attrs: {{ $attrs }}</p>
    <p>childCom1的$listeners: {{ $listeners }}</p>
    <hr>
    <Child2 v-bind="$attrs"></Child2>
  </div>
</template>

<script>
import Child2 from './Child2.vue'
export default {
  name: 'VChild1',
  components: {
    Child2
  },
  inheritAttrs: false,
  props: {
    name: String
  },
  created() {
    console.log(this.$attrs)
    console.log(this.$listeners)
  }
};
</script>
```



child2.vue

```vue
<template>
  <div class="border">
    <p>age: {{ age}}</p>
    <p>childCom2: {{ $attrs }}</p>

    <button @click="Test">Test</button>
  </div>
</template>
<script>

export default {
  name: 'VChild2',
  inheritAttrs: false,
  props: {
    age: String
  },
  created() {
    console.log(this)
    console.log(this.$attrs)
  },
  methods: {
    Test () {
      this.$emit('getData')
    }
  }
}
</script>
```







## 4. TodoList-案例初始化



**实现步骤：**



1. 随后拆分成：`Header`、`List`、`Item`、`Footer`组件
2. 先把所有的`html`结构、`css`样式，都放在`App`组件中
3. 把：结构、样式，都放在对应的`.vue`里



**落地代码：**

`App.vue`

```vue
<template>
  <div class="app">
    <div class="todo-container">
      <div class="todo-wrap">
        <!-- 头部区域 -->
        <Header />
        <!-- 主体区域 -->
        <List />
        <!-- 底部区域 -->
        <Footer />
      </div>
    </div>
  </div>
</template>

<script>
// 导入组件
import Header from './components/Header.vue'
import List from './components/List.vue'
import Footer from './components/Footer.vue'

export default {
  name: 'App',
  components: {
    Header,
    List,
    Footer
  }
}
</script>

<style>
body {
  background: #fff;
}

.btn {
  display: inline-block;
  padding: 4px 12px;
  margin-bottom: 0;
  font-size: 14px;
  line-height: 20px;
  text-align: center;
  vertical-align: middle;
  cursor: pointer;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.05);
  border-radius: 4px;
}

.btn-danger {
  color: #fff;
  background-color: #da4f49;
  border: 1px solid #bd362f;
}

.btn-danger:hover {
  color: #fff;
  background-color: #bd362f;
}

.btn:focus {
  outline: none;
}

.todo-container {
  width: 600px;
  margin: 0 auto;
}
.todo-container .todo-wrap {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
}

/*main*/
.todo-main {
  margin-left: 0px;
  border: 1px solid #ddd;
  border-radius: 2px;
  padding: 0px;
}

.todo-empty {
  height: 40px;
  line-height: 40px;
  border: 1px solid #ddd;
  border-radius: 2px;
  padding-left: 5px;
  margin-top: 10px;
}
</style>

```



`Header.vue`

```vue
<template>
  <div class="todo-header">
    <input type="text" placeholder="请输入你的任务名称，按回车键确认" />
  </div>
</template>

<script>
export default {
  name: 'TodoHeader'
}
</script>

<style scoped>
.todo-header input {
  width: 560px;
  height: 28px;
  font-size: 14px;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 4px 7px;
}

.todo-header input:focus {
  outline: none;
  border-color: rgba(82, 168, 236, 0.8);
  box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px rgba(82, 168, 236, 0.6);
}
</style>

```



`List.vue`

```vue
<template>
  <ul class="todo-main">
    <Item v-for="todo in todos" :key="todo.id" :todo="todo" />
  </ul>
</template>

<script>
import Item from './Item.vue'
export default {
  name: 'TodoItem',
  components: {
    Item
  },
  data() {
    return {
      todos: [
        { id: 'qw7ywqe28', title: '吃饭', done: false },
        { id: 'dw3iw92kj', title: '睡觉', done: true },
        { id: 'opkmi9s72', title: '学习', done: false },
        { id: 'uytrewert', title: '打豆豆', done: true }
      ]
    }
  }
}
</script>

<style scoped>
/*main*/
.todo-main {
  margin-left: 0px;
  border: 1px solid #ddd;
  border-radius: 2px;
  padding: 0px;
}

.todo-empty {
  height: 40px;
  line-height: 40px;
  border: 1px solid #ddd;
  border-radius: 2px;
  padding-left: 5px;
  margin-top: 10px;
}
</style>

```



`Item.vue`

```vue
<template>
  <li>
    <label>
      <input type="checkbox" />
      <span>{{ todo.title }}</span>
    </label>
    <button class="btn btn-danger" style="display: none">删除</button>
  </li>
</template>

<script>
export default {
  name: 'TodoItem',
  props: {
    todo: {
      type: Object,
      required: true
    }
  }
}
</script>

<style scoped>
/*item*/
li {
  list-style: none;
  height: 36px;
  line-height: 36px;
  padding: 0 5px;
  border-bottom: 1px solid #ddd;
}

li label {
  float: left;
  cursor: pointer;
}

li label li input {
  vertical-align: middle;
  margin-right: 6px;
  position: relative;
  top: -1px;
}

li button {
  float: right;
  display: none;
  margin-top: 3px;
}

li:before {
  content: initial;
}

li:last-child {
  border-bottom: none;
}
</style>

```



`Footer.vue`

```vue
<template>
  <div class="todo-footer">
    <label>
      <input type="checkbox" />
    </label>
    <span> <span>已完成0</span> / 全部2 </span>
    <button class="btn btn-danger">清除已完成任务</button>
  </div>
</template>

<script>
export default {
  name: 'TodoFooter'
}
</script>

<style scoped>
.todo-footer {
  height: 40px;
  line-height: 40px;
  padding-left: 6px;
  margin-top: 5px;
}

.todo-footer label {
  display: inline-block;
  margin-right: 20px;
  cursor: pointer;
}

.todo-footer label input {
  position: relative;
  top: -1px;
  vertical-align: middle;
  margin-right: 5px;
}

.todo-footer button {
  float: right;
  margin-top: 5px;
}
</style>

```







## 5. TodoList-渲染列表



1. 需要准备一个`todos`数组，用于保存多个`todo`对象。

   ```js
   todos:[
     {id:'qw7ywqe28',title:'吃饭',done:false},
     {id:'dw3iw92kj',title:'睡觉',done:true},
     {id:'opkmi9s72',title:'学习',done:false},
     {id:'uytrewert',title:'打豆豆',done:true},
   ]
   ```

2. 观察发现：`todos`数组，好多组件都要使用，所以我们选择放在`List`组件中。

     

3. `List`组件中：接收`todos`数组，使用`v-for`遍历，生成多个`Item`组件，<font color="red">**同时传递每一个`todo`对象给`Item`组件**</font>。

   ```vue
   <template>
     <ul class="todo-main">
       <Item v-for="todo in todos" :key="todo.id" :todo="todo" />
     </ul>
   </template>
   
   <script>
   import Item from './Item.vue'
   export default {
     name: 'TodoItem',
     components: {
       Item
     },
     data() {
       return {
         todos: [
           { id: 'qw7ywqe28', title: '吃饭', done: false },
           { id: 'dw3iw92kj', title: '睡觉', done: true },
           { id: 'opkmi9s72', title: '学习', done: false },
           { id: 'uytrewert', title: '打豆豆', done: true }
         ]
       }
     }
   }
   </script>
   ```

4. `Item`组件接收`todo`对象，并展示。

   ```vue
   <template>
     <li>
       <label>
         <input type="checkbox" />
         <span>{{ todo.title }}</span>
       </label>
       <button class="btn btn-danger" style="display: none">删除</button>
     </li>
   </template>
   
   <script>
   export default {
     name: 'TodoItem',
     props: {
       todo: {
         type: Object,
         required: true
       }
     }
   }
   </script>
   ```









## 6. TodoList-添加任务



添加功能在头部完成，需要往 `todos` 数组中添加数据，但是 `todos` 数组在 `List.vue` 中，因此我们需要更改 `todos` 所在的位置，将其放到 `App.vue` 中

> 很多组件都用的数据，可以放在他们共同的父组件中，这种操作叫：状态提升（数据提升）。



1. 观察发现：`todos`数组，好多组件都要使用，所以我们选择放在`App`组件中。

   ```vue
   <script>
   // ...
   
   export default {
     name: 'App',
     components: {
       Header,
       List,
       Footer
     },
     data() {
       return {
         todos: [
           { id: 'qw7ywqe28', title: '吃饭', done: false },
           { id: 'dw3iw92kj', title: '睡觉', done: true },
           { id: 'opkmi9s72', title: '学习', done: false },
           { id: 'uytrewert', title: '打豆豆', done: true }
         ]
       }
     }
   }
   </script>
   ```

   

2. `App`组件中：通过`props`传递给`List`组件

   ```vue
   <List :todos="todos"/>
   ```

   

3. 由于数据在`App`组件中，所以在`App`中创建一个`addTodo`方法，专门用于添加`todo`。

     

4. `App`组件中，通过`props`将`addTodo`传给`Header`组件。

   > 注意判断重复数据，使用数组的`find`方法。

   ```vue
   <script>
   export default {
     name: 'App',
     components: {
       Header,
       List,
       Footer
     },
     data() {
       return {
         todos: [
           { id: '1', title: '吃饭', done: false },
           { id: '2', title: '睡觉', done: true },
           { id: '3', title: '学习', done: false },
           { id: '4', title: '打豆豆', done: true }
         ]
       }
     },
     methods: {
       addTodo(newTodo) {
         const result = this.todos.find((item) => {
           return item.id === newTodo.id
         })
   
         if (result) {
           alert('输入重复，请重新输入')
         } else {
           this.todos.unshift(newTodo)
         }
       }
     }
   }
   </script>
   ```

   

5. `Header`组件中，接收`addTodo`，需要添加的时候，直接调用即可

   ```vue
   <script>
   export default {
     name: 'TodoHeader',
     props: ['addTodo', 'todos'],
     methods: {
       handleAdd(event) {
         let { value } = event.target
   
         // 获取所有任务的 ID
         let ids = this.todos.map((item) => {
           return item.id
         })
   
         // 计算所有任务 ID 的最大值
         let maxId = Math.max.apply(null, ids) + 1
   
         if (value.trim()) {
           let todoObj = {
             id: maxId,
             title: value.trim(),
             done: false
           }
   
           //调用App传递过来的addTodo，去实现添加
           this.addTodo(todoObj)
         } else {
           alert('输入不能为空！')
         }
   
         //清空用户输入
         event.target.value = ''
       }
     }
   }
   </script>
   ```







## 7. TodoList-删除任务



1. 由于数据在`App`组件中，所以在`App`中创建一个`deleteTodo`方法，用于删除某个`todo`。

   ```js
   methods: {
     deleteTodo(id){
       this.todos = this.todos.filter((todo)=>{
         return todo.id !== id
       })
     }
   },
   ```

   

2. `App`组件中，通过`props`将`deleteTodo`传给List组件。

   ```vue
   <List :todos="todos" :deleteTodo="deleteTodo"/>
   ```

   

3. List组件接收到deleteTodo后，进一步传给Item组件

   ```vue
   <template>
     <ul class="todo-main">
       <Item v-for="t in todos" :key="t.id" :todo="t" :deleteTodo="deleteTodo"/>
     </ul>
   </template>
   
   <script>
   	import Item from './Item.vue'
     
   	export default {
   		name: "List",
   		components:{Item},
   		props:['todos','deleteTodo']
   	};
   </script>
   ```

   

4. `Item`组件中，接收`deleteTodo`，并在需要删除的时候使用

   ```vue
   <template>
     <li>
       <label> 
   			<input type="checkbox" v-model="todo.done"/>
   			<span>{{todo.title}}</span> 
   		</label>
       <button class="btn btn-danger" @click="handleDelete(todo.id)">删除</button>
     </li>
   </template>
   
   <script>
   	export default {
   		name: "Item",
   		props:['todo','deleteTodo'],
   		methods: {
   			handleDelete(id){
   				if(confirm('确定删除吗？')){
   					this.deleteTodo(id)
   				}
   			}
   		},
   	};
   </script>
   ```







## 8.  TodoList-完成任务



将 复选框的值绑定`v-model`，所以已经实现了勾选单个`todo`



```vue
<template>
  <li>
    <label>
      <!-- eslint-disable-next-line -->
      <input type="checkbox" v-model="todo.done" />
      <span>{{ todo.title }}</span>
    </label>
    <button class="btn btn-danger" @click="handleDelete(todo.id)">删除</button>
  </li>
</template>
```











## 9. TodoList_清除已完成

1. `App`组件中，通过`props`将`clearAllDone`传给`Footer`组件。

   ```vue
   <Footer :clearAllDone="clearAllDone"/>
   
   <script>
     methods: {
       clearAllDone(){
         this.todos = this.todos.filter(item => !item.done)
       }
     }
   </script>
   ```

   

2. `Footer`组件中，接收`clearAllDone`，并使用

   ```vue
   <button class="btn btn-danger" @click="handleClear">清除已完成任务</button>
   
   <script>
     export default {
       name: "Footer",
       props:['clearAllDone','todos'],
       methods:{
         handleClear(){
           if(confirm('确定清除已完成吗？')){
             this.clearAllDone()
           }
         }
       }
     }
   </script>
   ```







## 10. TodoList-底部计算



使用计算属性对数据进行计算的处理，然后将数据传递通过 props 传给子组件



`App.vue`

```vue
<script>

export default {
  // ......

  computed: {
    // 计算出数据总条数
    total() {
      return this.todos.length
    },

    doneTotal() {
      return this.todos.filter((item) => item.done).length
    }
  }
}

</script>
```



`Footer`

```vue
<template>
  <div class="todo-footer">
    <label>
      <input type="checkbox" />
    </label>
    <span>
      <span>已完成 {{ doneTotal }} </span> / 全部 {{ total }}
    </span>
    <button class="btn btn-danger" @click="handleClear">清除已完成任务</button>
  </div>
</template>

<script>
export default {
  name: 'TodoFooter',
  props: ['clearAllDone', 'total', 'doneTotal'],
  methods: {
    handleClear() {
      if (confirm('确定清除已完成吗？')) {
        this.clearAllDone()
      }
    }
  }
}
</script>
```





## 11. TodoList-底部全选



对于：`<input type="checkbox"/>`来说，有两种实现方式：

- 第一种：使用`v-model`进行双向绑定，但需要注意的是`v-model`绑定的值，最好别是`props`。
- 第二种：使用`:checked="xxx"` 和 `@click="yyy"`实现。



**实现方式 1：使用 v-bind 写法**

```vue
<template>
  <div class="app">
    <div class="todo-container">
      <div class="todo-wrap">
        <!-- 头部区域 -->
        <Header :addTodo="addTodo" :todos="todos" />
        <!-- 主体区域 -->
        <List :todos="todos" :deleteTodo="deleteTodo" />
        <!-- 底部区域 -->
        <Footer
          :clearAllDone="clearAllDone"
          :total="total"
          :todos="todos"
          :doneTotal="doneTotal"
+          :checkAllTodo="checkAllTodo"
        />
      </div>
    </div>
  </div>
</template>

<script>
// 导入组件
// .......

export default {
  // .......
  methods: {
    // ......

    //全选 or 取消全选todo
    checkAllTodo(done) {
      this.todos.forEach((todo) => {
        todo.done = done
      })
    }
  }
}
</script>
```



```vue
<template>
  <div class="todo-footer">
    <label>
      <-- <input type="checkbox" v-model="checked" @click="handleCheckAll" /> -->
      <input type="checkbox" :checked="checkedAll" @click="handleCheckAll" />
    </label>
    <span>
      <span>已完成 {{ doneTotal }} </span> / 全部 {{ total }}
    </span>
    <button class="btn btn-danger" @click="handleClear">清除已完成任务</button>
  </div>
</template>

<script>
export default {
  name: 'TodoFooter',
  props: ['clearAllDone', 'total', 'doneTotal', 'todos', 'checkAllTodo'],
  methods: {
    // ......

    handleCheckAll(e) {
      // 通知App去全选 or 全不选
        
      this.checkAllTodo(e.target.checked)
    }
  },
    
  computed: {
    checkedAll() {
      return this.doneTotal === this.todos.length && this.todos.length > 0
    }
  }
}
</script>
```





**实现方式 2：使用 v-model 写法**

```vue
<template>
  <div class="todo-footer">
    <label>
      <!-- <input type="checkbox" :checked="checkedAll" @click="handleCheckAll" /> -->
      <!-- eslint-disable-next-line -->
      <input type="checkbox" v-model="checkedAll" />
    </label>
    <span>
      <span>已完成 {{ doneTotal }} </span> / 全部 {{ total }}
    </span>
    <button class="btn btn-danger" @click="handleClear">清除已完成任务</button>
  </div>
</template>

<script>
export default {
    
  //......
    
  computed: {
    // checkedAll() {
    //   return this.doneTotal === this.todos.length && this.todos.length > 0
    // }
   
    checkedAll: {
      get() {
        return this.doneTotal === this.todos.length && this.todos.length > 0
      },
      set(value) {
        this.checkAllTodo(value)
      }
    }
  }
}
</script>

```









## 12. TodoList - 本地存储



**业务需求**



刷新页面，关闭浏览器，`TodoList`列表不清空，依然是之前的数据

实现思路：将数据存储进`localStorage`中。



**思路分析**



1. 在`App`组件中监视`todos`数组，只要数组发生变化，就存入`localStorage`。

2. 具体编码：

   ```js
   //......
   data() {
     return {
       todos: JSON.parse(localStorage.getItem('todos')) || []
     }
   }
   
   //......
   watch: {
     todos: {
       handler(value) {
         localStorage.setItem('todos', JSON.stringify(value))
       },
       deep: true,
       immediate: true
     }
   }
   ```





## 13. 脚手架配置代理解决跨域



### 13.1 方法一

1. 在`vue.config.js`中添加如下配置：

   ```js
   devServer:{
     proxy:"http://localhost:5000"
   }
   ```

   

2. 注意：此种方式只能配置1个代理





### 13 .2 方法二



编写`vue.config.js`配置具体代理规则：

```js
module.exports = {
  devServer: {
      proxy: {
      '/api1': {// 匹配所有以 '/api1'开头的请求路径
        target: 'http://localhost:5000',// 代理目标的基础路径
        changeOrigin: true,
        pathRewrite: {'^/api1': ''}
      },
      '/api2': {// 匹配所有以 '/api2'开头的请求路径
        target: 'http://localhost:5001',// 代理目标的基础路径
        changeOrigin: true,
        pathRewrite: {'^/api2': ''}
      }
    }
  }
}
/*
   changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000
   changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:8080
   changeOrigin默认值为true
*/
```





**落地代码：**

```vue
<template>
  <div>
    <button @click="getStudentInfo">点我获取学生信息</button>
    <button @click="getCarInfo">点我获取汽车信息</button>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  methods: {
    getStudentInfo() {
      axios.get('http://localhost:8080/peiqi/students').then(
        (response) => {
          console.log(response.data)
        },
        (error) => {
          console.log(error)
        }
      )
    },
    getCarInfo() {
      axios.get('http://localhost:8080/qiaozhi/cars').then(
        (response) => {
          console.log(response.data)
        },
        (error) => {
          console.log(error)
        }
      )
    }
  }
}
</script>

```



vue.config.js

```js
module.exports = {
  //......
  
  //配置内置的dev-server（dev-server是帮我们启动那台内置的8080小服务器的）
  devServer: {
    open: true, //自动打开浏览器
    proxy: {
      '/peiqi': {
        //请求前缀，当请求地址是以/api开头的时候，会把该请求转发给5000服务器
        target: 'http://localhost:5000', //转发的目标服务器
        pathRewrite: { '^/peiqi': '' },
        changeOrigin: true
      },
      '/qiaozhi': {
        target: 'http://localhost:5001',
        pathRewrite: { '^/qiaozhi': '' },
        changeOrigin: true
      }
    }
  }
}

```










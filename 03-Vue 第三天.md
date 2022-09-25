# Vue 第三天备课笔记



## 1. 计算属性和侦听器的区别

> 🎯 目标：能够说出 计算属性 和 侦听器 的区别 💯

 

1. `computed`和`watch`之间的区别与联系：

   - `computed`能完成的功能，`watch`都可以完成，但一般来说`computed`的写法会更简便。

     > 例如：姓名案例，`computed`、`watch`都能完成，但`computed`写起来，更简单。

   - `watch`能完成的功能，`computed`不一定能完成。

     > 例如：还做姓名案例，但要求：姓改完1秒钟后，全名再变。

2. 理解`computed`（计算属性）：
   (1). 侧重在【算】，核心是：计算出来的值。
   (2). 靠`return`来输出计算的结果。
   (3). 不能开启异步任务。

   

3. 理解`watch`（监视属性）：
   (1). 侧重在【监视】，核心是：`xxx`变了，我要做`???`事。
   (2). 无需`return`，靠内部逻辑去完成要做的事。
   (3). 能开启异步任务。	



### 1.1 重温姓名案例

使用 computed 计算属性重温姓名案例



```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>搭建 Vue 开发环境</title>
</head>

<body>

  <div id="app">
    <p>姓氏：<input type="text" v-model="firstName" /></p>
    <p>名字：<input type="text" v-model="lastName" /></p>
    <p>全名：{{ fullName }}</p>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data() {
        return {
          firstName: '',
          lastName: ''
        }
      },
      computed: {
        fullName() {
          return this.firstName + this.lastName
        }
      }
    })
  </script>
</body>

</html>
```







###  1.2 使用 watch 实现姓名案例



**通过案例对比发现：**

1. 姓名案例，`computed`、`watch`都能完成，但`computed`写起来，更简单。`watch` 写起来更冗余
2. computed 核心在于【算】，计算结果直接返回即可
3.  watch 核心在于【监听】，需要内部创建业务逻辑



**落地代码：**

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>搭建 Vue 开发环境</title>
</head>

<body>

  <div id="app">
    <div id="app">
      <p>姓氏：<input type="text" v-model="firstName" /></p>
      <p>名字：<input type="text" v-model="lastName" /></p>
      <p>全名：{{ fullName }}</p>
    </div>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data() {
        return {
          firstName: 'zhang',
          lastName: 'san',
          fullName: ''
        }
      },
      watch: {
        firstName: {
          handler(newValue) {
            this.fullName = newValue + '-' + this.lastName
          },
          immediate: true
        },
        lastName: {
          handler(newValue) {
            this.fullName = this.firstName + '-' + newValue
          },
          immediate: true
        }

        // 使用简写方式，不能在页面加载的时候执行 watch 侦听器
        // firstName(newValue) {
        //   this.fullName = newValue + '-' + this.lastName
        // },

        // lastName(newValue) {
        //   this.fullName = this.firstName + '-' + newValue
        // }
      }
    })
  </script>
</body>

</html>
```







###  1.3 异步实现姓名案例



**通过案例对比发现：**

1. computed 不能开启异步任务，靠`return`来输出计算的结果。
2. watch 能开启异步任务，靠内部逻辑来输出计算的结果。



**落地代码：**

watch 版本

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>搭建 Vue 开发环境</title>
</head>

<body>

  <div id="app">
    <div id="app">
      <p>姓氏：<input type="text" v-model="firstName" /></p>
      <p>名字：<input type="text" v-model="lastName" /></p>
      <p>全名：{{ fullName }}</p>
    </div>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data() {
        return {
          firstName: 'zhang',
          lastName: 'san',
          fullName: ''
        }
      },
      watch: {
        firstName: {
          handler(newValue) {
            this.fullName = newValue + '-' + this.lastName
          },
          immediate: true
        },
        lastName: {
          handler(newValue) {
            // 回调函数不能写成普通函数的方式，如果写成普通的函数方式
            // this 交给 js 引擎解析后，this 指向 window

            // 如果写成箭头函数的方式，则指向函数所在的上下文
            const b = setTimeout(() => {
              this.fullName = this.firstName + '-' + newValue
            }, 1000)
          },
          immediate: true
        }
      }
    })
  </script>
</body>

</html>
```



computed 版本：

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>搭建 Vue 开发环境</title>
</head>

<body>

  <div id="app">
    <p>姓氏：<input type="text" v-model="firstName" /></p>
    <p>名字：<input type="text" v-model="lastName" /></p>
    <p>全名：{{ fullName }}</p>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data() {
        return {
          firstName: 'zhang',
          lastName: 'san'
        }
      },
      computed: {
        fullName() {
          // 定时器内部的 return 返回给了回调函数

          // 错误写法：不能定义变量接收定时器内部回调函数的返回值，因为这样接收的是定时器的 ID
          const a = setTimeout(() => {
            return this.firstName + '-' + this.lastName
          }, 1000)

          console.log(a)
        }
      }
    })
  </script>
</body>

</html>
```









## 2. 样式处理-绑定 class 样式

> 🎯 目标：能够对 class 进行动态的绑定 



写法 `:class="xxx"`，其中的 `xxx` 可以是：字符串、对象、数组。

1. 字符串写法适用于：要绑定的样式，类名不确定。
2. 对象写法适用于：要绑定的样式，类名确定，但不确定用不用。—— 用的多！
3. 数组写法适用于：要绑定的样式，个数、类名都不确定。



###  2.1 字符串写法



**落地代码**

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>搭建 Vue 开发环境</title>
  <style>
    .basic{
      width: 400px;
      height: 100px;
      border: 1px solid black;
      background-color: lightblue;
    }

    div {
      margin-top: 10px;
    }
    
    /* 三种心情，三选一 */
    .happy{
      border: 1px solid red;;
      background-color: rgba(255, 255, 0, 0.644);
    }
    .sad{
      border: 1px dashed black;
      background-color: gray;
    }
    .normal{
      border: 1px solid deepskyblue;
      background-color: skyblue;
    }
    
    /* 三个样式，随意选择，可以同时存在 */
    .atguigu1{
      background-color: yellowgreen;
    }
    .atguigu2{
      font-size: 30px;
      text-shadow:2px 2px 10px red;
    }
    .atguigu3{
      border-radius: 20px;
    }
  </style>
</head>

<body>

  <div id="app">
    <!-- 基础样式 -->
    <div class="basic"></div>

    <!-- 动态绑定 class 样式：字符串用法 -->
    <div class="basic" :class="str">{{ str }}</div>
    <p>
      <button @click="changeMood">切换心情</button>
    </p>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        str: 'happy'
      },
      methods: {
        // 切换心情
        changeMood() {
          const arr = ['happy', 'sad', 'normal']
          const index = Math.floor(Math.random()*3)

          this.str = arr[index]
        }
      }
    })
  </script>
</body>

</html>
```





###  2.2 对象写法

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>搭建 Vue 开发环境</title>
  <style>
    .basic{
      width: 400px;
      height: 100px;
      border: 1px solid black;
      background-color: lightblue;
    }

    div {
      margin-top: 10px;
    }
    
    /* 三种心情，三选一 */
    .happy{
      border: 1px solid red;;
      background-color: rgba(255, 255, 0, 0.644);
    }
    .sad{
      border: 1px dashed black;
      background-color: gray;
    }
    .normal{
      border: 1px solid deepskyblue;
      background-color: skyblue;
    }
    
    /* 三个样式，随意选择，可以同时存在 */
    .atguigu1{
      background-color: yellowgreen;
    }

    .atguigu2{
      box-shadow: 8px 8px lightgreen;
    }
    
    .atguigu3{
      border-radius: 20px;
    }
  </style>
</head>

<body>

  <div id="app">
    <!-- 基础样式 -->
    <div class="basic"></div>

    <!-- 动态绑定 class 样式：对象用法 -->
    <div class="basic" :class="obj"></div>

    <p>
      <button @click="obj.atguigu1 = !obj.atguigu1">应用 or 取消 atguigu1</button>
      <button @click="obj.atguigu2 = !obj.atguigu2">应用 or 取消 atguigu2</button>
      <button @click="obj.atguigu3 = !obj.atguigu3">应用 or 取消 atguigu3</button>
    </p>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        obj: {
          atguigu1: true,
          atguigu2: true,
          atguigu3: true
        }
      }
    })
  </script>
</body>

</html>
```





###  2.3 数组写法

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>搭建 Vue 开发环境</title>
  <style>
    .basic{
      width: 400px;
      height: 100px;
      border: 1px solid black;
      background-color: lightblue;
    }

    div {
      margin-top: 10px;
    }
    
    /* 三种心情，三选一 */
    .happy{
      border: 1px solid red;;
      background-color: rgba(255, 255, 0, 0.644);
    }
    .sad{
      border: 1px dashed black;
      background-color: gray;
    }
    .normal{
      border: 1px solid deepskyblue;
      background-color: skyblue;
    }
    
    /* 三个样式，随意选择，可以同时存在 */
    .atguigu1{
      background-color: yellowgreen;
    }

    .atguigu2{
      box-shadow: 8px 8px lightgreen;
    }
    
    .atguigu3{
      border-radius: 20px;
    }
  </style>
</head>

<body>

  <div id="app">
    <!-- 基础样式 -->
    <div class="basic"></div>

    <!-- 动态绑定 class 样式：数组用法 -->
    <div class="basic" :class="arr"></div>
    
    <div class="basic" :class="[isActive]"></div>

    <p>
      <input type="text" @keyup.enter="addClass" placeholder="请输入类名">
    </p>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        arr: [],
        isActive: 'atguigu1'
      },
      methods: {
        addClass(e) {
          this.arr.push(e.target.value)
        }
      }
    })
  </script>
</body>

</html>
```









## 3.样式处理-绑定 style 样式

> 🎯 目标：了解 style 如何进行动态的绑定 



**知识点**



写法 `:style="xxx"`，其中的 `xxx` 也可以是：字符串、对象、数组。

1. 字符串写法：很少使用 ——了解即可。
2. 对象写法适用于：样式的属性名确定，但值不确定 —— 也用！
3. 数组写法：很少使用 ——了解即可。



**落地代码：**



```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>搭建 Vue 开发环境</title>
  <style>
    .basic {
      width: 400px;
      height: 100px;
      border: 1px solid black;
      background-color: lightblue;
    }

    div {
      margin-top: 10px;
    }

    /* 三种心情，三选一 */
    .happy {
      border: 1px solid red;
      ;
      background-color: rgba(255, 255, 0, 0.644);
    }

    .sad {
      border: 1px dashed black;
      background-color: gray;
    }

    .normal {
      border: 1px solid deepskyblue;
      background-color: skyblue;
    }

    /* 三个样式，随意选择，可以同时存在 */
    .atguigu1 {
      background-color: yellowgreen;
    }

    .atguigu2 {
      box-shadow: 8px 8px lightgreen;
    }

    .atguigu3 {
      border-radius: 20px;
    }
  </style>
</head>

<body>

  <div id="app">
    <!-- style 绑定样式：字符串方式，包含一整段样式的字符串，基本不用 -->
    <div :style="str"></div>

    <!-- style 绑定样式：对象方式 -->
    <div :style="obj"></div>

    <!-- 偶尔也会用下，场景是属性值不确定的时候，但是用的极少 -->
    <div :style="{ width: '300px', height: '100px', border: '1px solid #ccc', backgroundColor: bgc}"></div>

    <!-- style 绑定样式：数组方式，用于将多个样式对象应用到同一个元素 -->
    <div :style="arr"></div>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        str: 'width: 300px; height: 100px; border: 1px solid #ccc; background-color: lightblue',
        obj: {
          width: '300px',
          height: '100px',
          border: '1px solid #ccc',
          backgroundColor: 'lightblue'
        },
        bgc: 'lightblue',
        arr: [
          {
            width: '300px',
            height: '100px',
            border: '1px solid #ccc',
            backgroundColor: 'lightblue'
          }
        ]
      },
      methods: {
        addClass(e) {
          this.arr.push(e.target.value)
        }
      }
    })
  </script>
</body>

</html>
```







## 4. 条件渲染

> 🎯 目标：熟练掌握条件渲染的应用，以及两种方式的区别 💯



条件渲染一般用来控制元素的隐藏和展示，有两种方式：

1. v-if
2. v-show



###  4.1 条件渲染 v-show



**知识点：**

1. 写法：`v-show="表达式"`。根据表达式之真假值，切换元素的 `display`
2. 适用于：切换频率较高的场景。
3. 特点：不展示的`DOM`元素依然在，仅仅是使用 display: none 样式隐藏掉了，不破坏`DOM`结构。



**知识点**

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>搭建 Vue 开发环境</title>
</head>

<body>

  <div id="app">
    <h1>当前的和为：{{sum}}</h1>
    <button @click="sum++">点我+1</button>
    
    <h2 v-show="sum === 1">Angular</h2>
    <h2 v-show="sum === 2">React</h2>
    <h2 v-show="sum === 3">Vue</h2>
    <h2 v-show="[1, 2, 3].indexOf(sum) === -1">未知</h2>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        sum: 0
      }
    })
  </script>
</body>

</html>
```







###  4.2 条件渲染 v-if

**知识点：**

1. 写法：

   ```
   1. v-if="表达式" 
   2. v-else-if="表达式"
   3. v-else
   ```

2. 适用于：切换频率较低的场景。

3. 特点：不展示的`DOM`元素直接被移除。



**注意事项：**

1. 注意1：`v-if`可以和:`v-else-if`、`v-else`一起使用，但要求结构不能被“打断”。
2. 注意2：如果使用`v-if`，可能会出现无法获取元素的问题。



**落地代码：**

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>搭建 Vue 开发环境</title>
</head>

<body>

  <div id="app">
    <div id="demo">
      <h1>当前的和为：{{sum}}</h1>
      <button @click="sum++">点我+1</button>
      
      <h2 v-if="sum === 1">Angular</h2>
      <h2 v-else-if="sum === 2">React</h2>
      <h2 v-else-if="sum === 3">Vue</h2>
      <h2 v-else>未知</h2>
    </div>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        sum: 0
      }
    })
  </script>
</body>

</html>
```







## 5. 数据代理、数据劫持



###  5.1 复习什么数据代理



1. 为什么要有数据代理

   ```
   new Vue({
    data: {}
   })
   
   function Vue(options) {
   
     this._data = options.data
   
   }
   
   <div>{{ _data.name }}</div>
   
   使用 vm 对 _data 对象中的属性进行操作(读/写)
   ```

   

2. Vue 如何做数据代理

   ```
   Object.defineProperty()
   
   只要代理完成后，vm 身上就有了 _data 中的数据
   
   <div>{{ name }}</div>
   
   在 vm 身上的数据有
   
   getter
   setter
   ```

   





###  5.2 什么是数据劫持



**知识点：**

1. 目的：捕获数据的修改。

2. 具体实现方式：

   - `Vue`收集到数据后，会将所有层级的属性，全都改为`getter`、`setter`的形式，随后放在`_data`中。
   - 当`_data`中的数据放生变化时，对应的`setter`会执行，在`setter`中会：**`① 修改数据、② 更新界面。❗❗`**



<img src="/images/数据代理_数据劫持.png" style="zoom:25%;" /> 



**提问 ？**

当对 vm 身上的 数据进行了变更，会触发 setter，setter 又会更改 _data 里面对应的数据，那么为什么又要对 _data 里面对应的数据添加 setter 呢  ？ 



答案：就是为了捕获数据的改变，捕获数据改变后视图更新，



```js
let _data = {
  name: '韦东奕',
  school: '北京大学'
}

// _data.name = '22222'
// _data.name = '333'
// _data.name = '444'


// 假如把 _data 里面的 name 更改了，如果不使用 Object.defineProperty 如何监听是否改变
// 可以发现，如果不通过 Object.defineProperty 去进行监听，使用其他方式检测不到数据改变的
// setTimeout(() => {
//   if (_data.name !== '韦东奕') {
//     console.log('数据改变了')
//   }
// }, 1000)

Object.defineProperty(_data, 'name', {
  get () {
    return '韦东奕'
  },

  set() {
    console.log('你改变数据了~~~~')
  }
})

_data.name = '22222'
_data.name = '333'
_data.name = '444'
```







###  5.3 总结 数据代理 数据劫持

**数据代理**：

1. 目的：让程序员更加方便的读取到`_data`中属性。
2. 原理：`Object.defineProperty`。
3. 体现：`vm`身上有`_data`里的所有属性，且有每一个属性，都有自己的`proxyGetter`、`proxySetter`。

> - 当修改`vm`上的属性时，该属性对应的`proxySetter`就会调用，去修改`_data`中对应的属性。
> - 当读取`vm`上的属性时，该属性对应的`proxyGetter`就会调用，去读取`_data`中对应的属性。



**数据劫持(`_data`里的那点事)**：

1. 目的：为了捕获到数据的改变，进而重新解析模板，更新界面。

2. 原理：`Object.defineProperty`。

3. 体现：`_data`身上的每一个属性不直接给值，都变为：`reactiveSetter`、`reactiveGetter`形式。


  > - 当修改`_data`上的属性时，该属性对应的`reactiveSetter`就会调用。且在`reactiveSetter`中Vue会：维护数据、更新页面。
  > - 当读取`_data`上的属性时，该属性对应的`reactiveGetter`就会调用，返回对应的值。





### 5.4 数据劫持源码(拓展阅读 )

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>

  <script>

    // ------------------------------------------------------
    // ----------------  以下代码为数据代理 -------------------
    // ------------------------------------------------------

    // 创建 Vue 构造函数，options 是用户传递的配置项
    function Vue(options) {
      // 通过 $options 属性保存配置选项选项的数据
      // this.$options = options || {}
      // 收集配置想中数据
      this._data = options.data || {}
      // 将 data 中的成员转换成 getter 和 setter，并注入到 Vue 实例中
      this._proxyData(this._data)

      new Observer(this._data)
    }

    // 将 data 中的数据转换成 getter 和 setter 注入到 vue 实例 vm 身上
    Vue.prototype._proxyData = function (_data) {
      // 遍历 data 中的所有的属性，将 data 中每个属性，都放到 vm 上
      Object.keys(_data).forEach(key => {
        // 把 data 中的数据都注入到实例中
        Object.defineProperty(this, key, {
          enumerable: true,
          configurable: true,
          get() {
            return _data[key]
          },
          set(newValue) {
            if (newValue === _data[key]) return
            _data[key] = newValue
          }
        })
      })
    }


    // ------------------------------------------------------
    // ----------------  以下代码为数据劫持 -------------------
    // ------------------------------------------------------
    function Observer(data) {
      // 方法作用：遍历 data 中所有的属性，因此参数是 data 对象
      this.walk(data)
    }

    // 遍历 data 中所有的属性，因此参数是 data 对象
    Observer.prototype.walk = function (data) {
      // 判断需要监听的 data 是否为对象
      if (!data || typeof data !== 'object') return

      // 遍历 data 对象的所有属性
      Object.keys(data).forEach(key => {
        this.defineReactive(data, key, data[key])
      })
    }

    // 大名鼎鼎的数据劫持!!!!
    Observer.prototype.defineReactive = function (obj, key, val) {
      let that = this
      // 如果 data 中的数据是一个对象
      // 即如果 val 是对象，就需要把 val 内部的属性转换成响应式数据
      this.walk(val)

      Object.defineProperty(obj, key, {
        enumerable: true,
        configurable: true,
        get() {
          return val
        },
        set(newValue) {
          if (newValue === val) return
          val = newValue

          // 对 data 中的属性重新赋值，例如：赋值成一个对象
          // 将动态设置的对象中属性值转换为 getter / setter
          that.walk(val)
        }
      })
    }


    const vm = new Vue({
      data: {
        name: '亚瑟',
        age: 10,
        obj: { name: '亚瑟' }
      }
    })

    console.log(vm)
  </script>
</body>

</html>
```









## 6. Vue 检测对象的变化的原理



**知识点：**



**由于 `JavaScript` 的限制，`Vue` 不能检测数组和对象的变化。**



对于对象而言，`Vue` 无法检测属性中的添加或移除，

1. 因为 `Vue` 只会在**初始化实例时对属性 执行 `getter/setter` 转化**
2. 也就是说 **属性必须在 `data` 对象上存在才能让 Vue 将它转换为响应式的**



例如：以下代码，在 methods 中写了新增和删除，Vue 都是检测不到的！

```js
const vm = new Vue({
  el: '#app',
  data: {
    obj: {
      name: '安琪拉',
      gender: '女'
    }
  },
  methods: {
    addAge () {
      // 新增一个属性检测不到，Vue 检测不到
      // this.obj['age'] = 10

      // 删除一个属性也检测不到，Vue 也检测不到
      delete this.obj['gender']

    }
  }
})
```



那么 `Vue` 如何给新增或者删除的元素进行响应式处理呢 ❓

```
Vue.set()

vm.$set()
```



```js
Vue.set(vm.obj, 'age', 10)

vm.$set(vm.obj, 'age', 10)

this.$set(this.obj, 'age', 10)
```









## 7. Vue 检测数组改变的原理



**知识点：**



**由于 `JavaScript` 的限制，`Vue` 不能检测数组和对象的变化。**



Vue 不能检测以下数组的变动：

1. 当你利用索引直接设置一个数组项时，例如：`vm.items[indexOfItem] = newValue`
2. 当你修改数组的长度时，例如：`vm.items.length = newLength`

```js
const vm = new Vue({
  el: '#app',
  data: {
    hobby: ['吃饭', '睡觉', '打 Jerry']
  },
  methods: {
    addAge () {
      // 不是响应性的
      // this.hobby[0] = '打王者'

      // 不是响应式的
      // this.hobby.length = 100

      this.$set(this.hobby, 0, '打王者')
    }
  }
})
```









### 7.1 变更方法



变更方法，顾名思义，会变更调用了这些方法的原始数组，Vue 通过包裹数组的`7`个变更方法实现，本质就是做了两件事：

1. 调用原生对应的方法对数组进行更新。
2. 重新解析模板，进而更新页面。



结论：在`Vue`的世界里，修改数组中的某一项一定要用如下方法：

> `push()`、`pop()`、`shift()`、`unshift()`、`splice()`、`sort()`、`reverse()`



**落地代码：**

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>搭建 Vue 开发环境</title>
</head>

<body>

  <div id="app">
    <h1>人员信息</h1>

    <ul>
      <li>姓名：{{name}}</li>
      <li>年龄：{{age}}</li>
      <li>爱好：{{hobby}}</li>
    </ul>

    <button @click="changeName">改名</button>
    <button @click="changeAge">改年龄</button>
    <button @click="changeFirstHobby">改第1个爱好</button>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        name: 'Tom',
        age: 18,
        hobby: ['吃饭', '数据', '打 Jerry']
      },
      methods: {
        changeName() {
          this.name = '学习'
        },
        changeAge() {
          this.age = 18
        },
        changeFirstHobby() {
          // this.hobby[0] = '学习' // 不奏效

          this.hobby.splice(0, 1, '学习') // 奏效

          // 验证到底调用的是原生的，还是 Vue 包裹的
          console.log(this.hobby.push === Array.prototype.push)
        },
      }
    })
  </script>
</body>

</html>
```



说白了就是**可以利用一个拦截器覆盖 Array.prototype，每当使用 Array 原型上的方法操作数组时，其实执行的都是拦截器中提供的方法。然后，在拦截器中使用原始Array的原型方法去操作数组。**









### 7.2 set 方法的使用



```js
const vm = new Vue({
  el: '#app',
  data: {
    hobby: ['吃饭', '睡觉', '打 Jerry']
  },
  methods: {
    addAge () {
      // 不是响应性的
      // this.hobby[0] = '打王者'

      // 不是响应式的
      // this.hobby.length = 100

      this.$set(this.hobby, 0, '打王者')
    }
  }
})

Vue.set(vm.hobby, 0, '打王者')
vm.$set(vm.hobby, 0, '打王者')
```








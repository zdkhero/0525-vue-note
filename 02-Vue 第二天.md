# Vue 第二天备课笔记



## 1. Vue 事件处理



### 1.1 事件处理-事件绑定

> 🎯 目标：熟练掌握 Vue 中事件绑定的方式以及注意事项

**知识点：**

1. 使用`v-on:xxx` 或 `@xxx` 绑定事件，`xxx`是事件名，同原生`DOM`事件名。
2. 事件的回调函数，一定要配置在`methods`中！（按照规矩来，`data`中写数据、`methods`中写方法）。
3. `methdos`中的函数最终也会出现在`vm`上 (`但不存在数据代理`，因为数据需要需要进行监视，而方法不需要，而且都放在了 data 中都会进行数据劫持，增加 get 、和 set 属性，也会造成性能的损耗)



**❗注意事项：**

由Vue管理的函数（截止目前，我们只学了：`data`函数、`methods`中的函数），请务必写成普通函数，这样才能保证：`this`是`vm`，一旦写成了箭头函数，`this`就不再是`vm`了。



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
    <!-- 使用 v-on:xx 的方式绑定事件，xx 为事件名，和原生 DOM 事件名是一样的 -->
    <button v-on:click="add">+ 1</button>
    
    <!-- v-on: 的简写方式是 @ -->
    <button @click="welcome">欢迎新同学</button>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    new Vue({
      el: '#app',
      // 模板中事件的回调函数，一定要写到 methods 配置项中
      methods: {
        welcome() {
          alert('你好,')
        }
      }
    })
  </script>
</body>

</html>
```





### 1.2 事件处理-事件对象

> 🎯 目标：明白事件对象是什么，在 Vue 中应该怎么使用

**知识点：**

1. 事件对象是用来记录一些事件发生时的相关信息的对象，事件对象只有事件发生时才会产生，并且在事件处理函数内部访问
2. Vue 同样也是，在触发事件回调时，事件处理程序会主动的给我们传入一个参数 —— `event`（事件对象）。



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
    <button v-on:click="welcome">你好，欢迎新同学</button>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    new Vue({
      el: '#app',
      methods: {
        welcome(event) {
          alert(event.target.innerText)
        }
      }
    })
  </script>
</body>

</html>
```





###  1.3 事件处理-事件传参

> 🎯 目标：掌握 Vue 中事件传参的方式 



**知识点：**

1. **不传递**参数，这样写：`@click="show"` ，`show`方法会收到一个`event`（事件对象）。
2. **传一个**参数，这样写：`@click="show(6)"` ，`show`方法只会收到一个`6`。
3. **传多个**参数，这样写：`@click="show(6,7,8)"` ，`show`方法会收到：`6、7、8`。
4. 传参，还想用事件对象，这样写：`@click="show($event,6)"`，`show`方法会收到：`event`、`6`。
5. 传递的参数也可以是`data`中的数据，例如`@click="show(address,tel)"`。



**落地代码：**

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>事件传参</title>
</head>

<body>

  <div id="app">
    <!-- 写了括号，不传参数，默认是 undefined -->
    <button v-on:click="welcome()">按钮</button>

    <!-- 传递一个参数 -->
    <button v-on:click="welcome1('666')">按钮</button>

    <!-- 传递多个参数 -->
    <button v-on:click="welcome2('666', '简直 6 的飞起')">按钮</button>

    <!-- 传参，还想用事件对象 -->
    <button v-on:click="welcome3($event, '666', '简直 6 的飞起')">按钮</button>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    new Vue({
      el: '#app',
      methods: {
        welcome(params) {
          alert(`同学，你真 ${params}`)
        },

        welcome1(params) {
          alert(`同学，你真 ${params}`)
        },

        welcome2(params1, params2) {
          alert(`同学，你真 ${params1}, ${params2}`)
        },

        welcome3(event, params1, params2) {
          console.log(event.target.innerText)
        }
      }
    })
  </script>
</body>

</html>
```









###  1.4 事件处理-事件修饰符

> 🎯 目标：能够说出事件修饰符使用的场景

**使用场景：**

在事件处理函数中调用 event.preventDefault() 阻止默认行为或 event.stopPropagation() 阻止冒泡是非常常见的需求。尽管我们可以在方法中轻松实现这点，但更好的方式是：方法只有纯粹的数据逻辑，而不是去处理 DOM 事件细节，因此，vue 提供了事件修饰符的概念，来辅助程序员更方便的对事件的触发进行控制。



1. `prevent`修饰符，可以阻止默认行为。

2. `stop`修饰符，可以阻止冒泡。

3. `once`修饰符，让事件只能触发一次。

4. 事件修饰符还可以串联使用

   ```vue
   <a href="https://www.baidu.com" @click.stop.prevent="show4">按钮4</a>
   ```



**落地代码：**

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>事件传参</title>
</head>

<body>

  <div id="app">
    <!-- 使用事件对象的方式阻止默认行为 -->
    <a href="https://www.baidu.com" @click="handler">百度一下，你就知道</a> <br />

    <!-- 使用事件修饰符 prevent 来阻止默认行为 -->
    <a href="https://www.baidu.com" @click.prevent>百度一下，你就知道</a>

    <div @click="parent">
      <!-- 使用事件修饰符 stop 来阻止冒泡 -->
      <button @click.stop="son">阻止事件冒泡</button>
    </div>

    <!-- 使用事件修饰符 once 让事件只触发一次 -->
    <button @click.once="oneHandler">让事件只触发一次</button>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    new Vue({
      el: '#app',
      methods: {
        handler(e) {
          e.preventDefault()
        },
        parent() {
          console.log('parent')
        },
        son(e) {
          // e.stopPropagation()
          console.log('son')
        },
        oneHandler () {
          console.log('once 让事件只触发一次')
        }
      }
    })
  </script>
</body>

</html>
```







### 1.5 事件处理-按键修饰符

> 🎯 目标：能够说出按键修饰符使用的场景以及常用的几个按键

**知识点：**

1. <font color="red">**Vue中的按键别名（牢牢掌握）：**</font>

   - 回车 => `enter`
   - 删除 => `delete` （退格 、 删除 按键）
   - 退出 => `esc`
   - 空格 => `space`
   - 换行 => `tab` （必须配合`keydown`去使用）
   - 上 => `up`
   - 下 => `down`
   - 左 => `left`
   - 右 => `right`

   

2. 若要绑定的按键，不是上述9个中的，有两种做法（了解即可）：

   - 使用`event.keyCode`去指定具体的按键（不推荐），例如：`@keyup.13` 绑定回车
   - 使用**JS中**最原始的按键名`event.key`，去绑定事件，但有要求：
     - 一个单词组成的，直接写，例如：`@keyup.a`
     - 多个单词组成的，用短杠命名法，例如：`@keyup.caps-lock`

   

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
    input {
      width: 300px;
    }
  </style>
</head>

<body>

  <div id="app">
    <!-- 通过原生事件以及判断键值码的方式来进行处理 -->
    <input type="text" @keyup="handler" placeholder="按住回车键，可以打印出东西"> <br />

    <!-- 使用 enter 按键修饰符 -->
    <input type="text" @keyup.enter="handler1" placeholder="按住回车键，可以打印出东西"> <br />

    <!-- 使用 delete 或者 退格键 按键修饰符 -->
    <input type="text" @keyup.delete="handler1" placeholder="按住删除键，可以打印出东西"> <br />

    <input type="text" @keyup.up="handler1" placeholder="按住上键，可以打印出东西"> <br />
    <input type="text" @keyup.down="handler1" placeholder="按住下键，可以打印出东西"> <br />
    <input type="text" @keyup.left="handler1" placeholder="按住左键，可以打印出东西"> <br />
    <input type="text" @keyup.right="handler1" placeholder="按住右键，可以打印出东西"> <br />

    <input type="text" @keyup.a="handler1" placeholder="按住 a 键，可以打印出东西"> <br />
    <input type="text" @keyup.13="handler1" placeholder="按住回车键，可以打印出东西"> <br />
    <input type="text" @keyup.b="handler1" placeholder="按住 b 键，可以打印出东西"> <br />

    <input type="text" @keyup.caps-lock="handler1" placeholder="按住 CapsLk 键，可以打印出东西"> <br />
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    new Vue({
      el: '#app',
      methods: {
        handler (e) {
          if (e.keyCode === 13) {
            console.log(e.target.value)
          }
        },

        handler1 (e) {
          console.log(e.target.value)
        }
      }
    })
  </script>
</body>

</html>
```





## 6. Vue 计算属性

> 🎯 目标：熟练掌握计算属性的使用



### 6.1 计算属性-姓名案例

**案例如下**

![](./images/姓名案例.gif)



**插值语法实现**

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
    姓氏：<input type="text" v-model="firstName" /> <br /> <br />
    名字：<input type="text" v-model="lastName" /> <br /> <br />
    <!-- 通过插值的写法能够实现功能，但是会让模板变的复杂而且非常不容易维护 -->
    全名：<span>{{ firstName[0].toUpperCase() + firstName.slice(1) }} {{ lastName }}</span>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        firstName: 'zhang',
        lastName: 'san'
      }
    })
  </script>
</body>

</html>
```





**methods实现**

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
    <h2>你喜欢看什么动画片</h2>
    <input type="text" v-model="donghuapian" />
    <hr />
    姓氏：<input type="text" v-model="firstName" /> <br /> <br />
    名字：<input type="text" v-model="lastName" /> <br /> <br />
    全名：<span>{{ getFullName() }}</span>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        firstName: 'zhang',
        lastName: 'san',
        donghuapian: ''
      },
      methods: {
        // 通过方法进行实现，简化了模板中的代码
        // 但是，存在效率、性能上的问题 ！
        // 数据发生改变，模板就是重新进行渲染，
        // 因此，当 data 中的数据每一项发生了改变， getFullName 会被频繁的触发，会被频繁的调用
        getFullName() {
          console.log('getFullName')
          const { firstName, lastName } = this
          return firstName[0].toUpperCase() + firstName.slice(1) + ' ' + lastName[0].toUpperCase() + lastName.slice(1)
        }
      }
    })
  </script>
</body>

</html>
```







### 6.2 计算属性的概念

> 🎯 目标：能够说出计算属性的使用场景



**知识点**

1. 定义：要用的属性（数据）不存在，要根据已有属性（数据）计算得来
2. 场景：模板内的表达式非常便利，但是设计它们的初衷是用于简单运算的
                 在模板中放入太多的逻辑会让模板过重且难以维护，因此对于任何复杂逻辑，你都应当使用**计算属性**
3. 原理：底层通过`Objcet.defineProperty`实现的
4. 优势：与`methods`实现效果相比，内部有缓存机制，效率更高，且调试方便。



### 6.3 计算属性 _getter 的使用

> 🎯 目标：熟练 _getter  的使用，能够说出计算属性的优点



**知识点**

1. get  函数何时调用

   - 初次读取 计算属性 的时候调用的

   - 当 计算属性 所依赖的数据发生变化时会被再次调用

     

2. 与 methods 的实现方式相比，内部有缓存机制(复用)，效果更高、且调试方便

    

3. 注意：

   - 计算属性最终会出现在 vm 上，在模板中直接使用即可
   - 如果计算属性后期需要被修改，那必须写 set 函数去响应修改



**落地代码**

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
    <input type="text" placeholder="请输入你喜欢的动画片" v-model="donghuapian">
    <hr />
    姓氏：<input type="text" v-model="firstName" /> <br /> <br />
    名字：<input type="text" v-model="lastName" /> <br /> <br />
    全名：<span>{{ fullName }}</span>
    全名：<span>{{ fullName }}</span>
    全名：<span>{{ fullName }}</span>
    全名：<span>{{ fullName }}</span>
    全名：<span>{{ fullName }}</span>
    全名：<span>{{ fullName }}</span>
    全名：<span>{{ fullName }}</span>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        firstName: 'zhang',
        lastName: 'san',
        donghuapian: ''
      },
      methods: {
        getFullName() {
          const { firstName, lastName } = this
          return firstName[0].toUpperCase() + firstName.slice(1) + ' ' + lastName[0].toUpperCase() + lastName.slice(1)
        }
      },

      // 计算属性：要存在属性不存在，因此我们要根据已有的属性进行计算，产生一个新的属性
      // 计算属性触发的时机：
      //  1. 初始化的时候会调用计算属性
      //  2. 计算属性依赖的数据发生改变的时候触发

      // 计算属性最大的优势：具有缓存
      computed: {
        fullName: {
          // 只要我们调用 fullName 这个属性，就会自动调用、自动执行这个 get 方法
          get() {
            console.log('fullName')
            const { firstName, lastName } = this
            return firstName[0].toUpperCase() + firstName.slice(1) + ' ' + lastName[0].toUpperCase() + lastName.slice(1)
          }
        }
      }
    })
  </script>
</body>

</html>
```





### 6.4 计算属性-_setter 的使用

> 🎯 目标：熟练 _setter 的使用场景



**知识点**

1. set 函数何时被调用

   - 当计算属性被修改时被调用

   

2. 什么叫修改修改计算属性 ？

   - 例如： this.fullName = 'qiankun' 这叫修改， console.log(this.fullName ) 这叫读取



**落地代码**

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
    <input type="text" placeholder="请输入你喜欢的动画片" v-model="donghuapian">
    <hr />
    姓氏：<input type="text" v-model="firstName" /> <br /> <br />
    名字：<input type="text" v-model="lastName" /> <br /> <br />
    全名：<span>{{ fullName }}</span> <br /> <br />
    <button @click="changeFullName">更改全名</button>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        firstName: 'zhang',
        lastName: 'san',
        donghuapian: ''
      },
      methods: {

        changeFullName () {
          this.fullName = 'Li Si'
        }
      },

      // 计算属性：要存在属性不存在，因此我们要根据已有的属性进行计算，产生一个新的属性
      // 计算属性触发的时机：
      //  1. 初始化的时候会调用计算属性
      //  2. 计算属性依赖的数据发生改变的时候触发

      // 计算属性最大的优势：具有缓存
      computed: {
        fullName: {
          // 只要我们调用 fullName 这个属性，就会自动调用、自动执行这个 get 方法
          get() {
            console.log('fullName')
            const { firstName, lastName } = this
            return firstName[0].toUpperCase() + firstName.slice(1) + ' ' + lastName[0].toUpperCase() + lastName.slice(1)
          },

          // 当我们需要对 fullName 属性进行修改时，需要 set 函数
          set (val) {
            const arr = val.split(' ')
            this.firstName = arr[0].toLowerCase()
            this.lastName = arr[1].toLowerCase()
          }
        }
      }
    })
  </script>
</body>

</html>
```







### 6.5 计算属性-简写方式

> 🎯 目标：熟练 计算属性 的简写方式

1. 什么时候才能简写？—— 计算属性不会修改时（不需要`set`函数的时候），才能用简写形式。

2.  写成简写方式后，在 vm 身上的计算属性是一个属性还是函数？ 是属性，值为 计算属性内部返回的值

3. 语法实例：

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
       <input type="text" placeholder="请输入你喜欢的动画片" v-model="donghuapian">
       <hr />
       姓氏：<input type="text" v-model="firstName" /> <br /> <br />
       名字：<input type="text" v-model="lastName" /> <br /> <br />
       全名：<span>{{ fullName }}</span>
     </div>
   
     <script src="../vue.js"></script>
     <script>
       Vue.config.productionTip = false
   
       const vm = new Vue({
         el: '#app',
         data: {
           firstName: 'zhang',
           lastName: 'san',
           donghuapian: ''
         },
         computed: {
           fullName() {
             const { firstName, lastName } = this
             
             // 如果是简写方式，内部必须有返回值
             return firstName[0].toUpperCase() + firstName.slice(1) + ' ' + lastName
           }
         }
       })
     </script>
   </body>
   
   </html>
   ```







## 7. 天气案例-开发小技巧

> 🎯 目标：了解 methods 开发中的技巧



**知识点：**

1. 如果回调函数的逻辑很简短，那么可以写在引号里
2. 如果页面中没有使用到 data 中的数据，开发者工具状态可能不会发生改变



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
    <!-- <h2>{{ age }}</h2> -->

    <!-- 计算属性写法 -->
    <!-- <h2>{{ weatherValue }}</h2> -->

    <!-- 通过三元表达式进行渲染 -->
    <!-- <h2>今天天气很{{ isHot ? '炎热' : '凉爽' }}</h2> -->

    <button @click="changeWeather">切换天气</button>

    <!-- 如果在事件处理程序中，业务逻辑很短，可以直接写到引号里 -->
    <button @click="isHot = !isHot">切换天气</button>

    <!-- 如果 业务逻辑超过了一句以上，建议还是放到一个函数中 -->
    <button @click="isHot = !isHot; age+=1">切换天气</button>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        isHot: true,
        age: 1
      },
      methods: {
        changeWeather () {
          this.isHot = !this.isHot
        }
      },
      computed: {
        weatherValue() {
          return this.isHot ? '今天天气很炎热' : '今天天气很凉爽'
        }
      }
    })

  </script>
</body>

</html>
```







## 8. Vue 监听器



###  8.1 监听器基本使用

> 🎯 目标：能够说出 监听器 的使用场景和基础语法

**知识点：**

Vue 本身就能监听数据的改变，例如：在将数据改变后，页面中使用了数据的地方就会发生改变 ，但是有时候程序员需要手动的监听某个数据的改变，然后来响应数据的变化。特别需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的，这时候，就需要使用到 Vue 给提供的 watch 属性



**语法：**

1. 在 vm 实例中配置 watch ，用来声明 watch 监听器

2.  watch 中的属性为需要监听的属性，值是一个对象

3.  其中handler 为固定写法，当被监视的属性变化时, 回调函数（handler）自动调用，至于回调函数中做什么，要看具体需求。

4.  handler 方法本身携带了两个参数

   - newVal：数据改变之后的值
   - oldVal: 数据改变之前的值

   

```js
watch:{
  // a 为需要监听的数据
  a: {
    // handler 为固定写法，当被监视的属性变化时, 回调函数（handler）自动调用，至于回调函数中做什么，要看具体需求。
    handler(newValue,oldValue) { 
      /*********/
    }
  }
}
```



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
    img {
      width: 200px;
      height: 200px;
    }
  </style>
</head>

<body>

  <div id="app">
    <h2>今天天气很{{ isHot ? '炎热' : '凉爽' }}</h2>

    <!-- Vue 本身就能够监听数据的改变，在数据改变后能够立马驱动视图的更新 -->
    <h2>今天天气很{{ a.b.c.d.e ? '炎热' : '凉爽' }}</h2>

    <img :src="dogUrl" /> <br />

    <button @click="changeWeather">切换天气</button>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        isHot: true,
        dogUrl: 'https://img0.baidu.com/it/u=2712974893,3591500984&fm=253&fmt=auto&app=138&f=JPEG',
        a: {
          b: {
            c: {
              d: {
                e: true
              }
            }
          }
        }
      },
      methods: {
        changeWeather() {
          this.isHot = !this.isHot
        }
      },

      // 侦听器
      watch: {
        // 侦听器，侦听，就是监听某个数据的改变
        isHot: {
          // 只要 isHot 数据发生了改变，就会立马执行 handler 方法

          // handler 方法本身携带了两个参数
          // 1. newVal：数据改变之后的值
          // 2. oldVal: 数据改变之前的值
          handler(newVal, oldVal) {
            // console.log('新值: ' + newVal)
            // console.log('旧值：' + oldVal)

            const dog1 = 'https://img0.baidu.com/it/u=2712974893,3591500984&fm=253&fmt=auto&app=138&f=JPEG'
            const dog2 = 'https://img2.baidu.com/it/u=3263682967,163100295&fm=253&fmt=auto&app=138&f=JPEG'
            
            this.dogUrl = newVal ? dog1 : dog2
          }
        }
      }
    })

  </script>
</body>

</html>
```







###  8.2 watch 监听器 几个细节

> 🎯 目标：熟练 watch 监听器的一些知识点

**知识点：**

1.  被监视的可以是：属性（`data`），也可以是计算属性（`computed`）。
2.  监视的属性必须存在，才能进行监视，若监视了不存在的属性，也不会报错！
3.  在 vm 身上存在 $watch 属性，也可以通过 vm.$watch 进行监听



```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>搭建 Vue 开发环境</title>
  <style>
    img {
      width: 200px;
      height: 200px;
    }
  </style>
</head>

<body>

  <div id="app">
    <h2>今天天气很{{ isHot ? '炎热' : '凉爽' }}</h2>

    <img :src="dogUrl" /> <br />

    <button @click="changeWeather">切换天气</button>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        isHot: true,
        dogUrl: 'https://img0.baidu.com/it/u=2712974893,3591500984&fm=253&fmt=auto&app=138&f=JPEG',
      },
      methods: {
        changeWeather() {
          this.isHot = !this.isHot
        }
      },

      computed: {
        weatherValue() {
          return this.isHot ? '炎热' : '凉爽'
        }
      },

      //#region 
      // 侦听器
      // watch: {
      //   // 侦听器，侦听，就是监听某个数据的改变
      //   isHot: {
      //     handler(newVal, oldVal) {

      //       const dog1 = 'https://img0.baidu.com/it/u=2712974893,3591500984&fm=253&fmt=auto&app=138&f=JPEG'
      //       const dog2 = 'https://img2.baidu.com/it/u=3263682967,163100295&fm=253&fmt=auto&app=138&f=JPEG'

      //       this.dogUrl = newVal ? dog1 : dog2
      //     }
      //   },

      //   weatherValue: {
      //     // 1. watch 除了可以监听 data 中的数据以外，还可以监听计算属性
      //     handler (newVal) {
      //       if (newVal === '炎热') {
      //         alert('热热热 ！')
      //       } else {
      //         alert('凉凉凉 ！')
      //       }
      //     }
      //   },


      //   // 2. watch 可以监听一个不存在属性，不会报错，但是这样监听没有任何的意义
      //   // a: {
      //   //   handler () {
      //   //     console.log('aaaa')
      //   //   }
      //   // }
      // }
      //#endregion
    })

    console.log(vm)

    // 可以通过 vm.$watch 来监听一个数据的变化，这种方法了解即可
    // 在以后开发中几乎用不到
    vm.$watch('isHot', {
      handler(newVal, oldVal) {

        const dog1 = 'https://img0.baidu.com/it/u=2712974893,3591500984&fm=253&fmt=auto&app=138&f=JPEG'
        const dog2 = 'https://img2.baidu.com/it/u=3263682967,163100295&fm=253&fmt=auto&app=138&f=JPEG'

        this.dogUrl = newVal ? dog1 : dog2
      }
    })

  </script>
</body>

</html>
```





###  8.3 监听器立即监听

> 🎯 目标：掌握监听器如何立即监听的属性

**知识点：**

使用**普通函数的形式**定义的 watch 侦听器，只有等数据发生后才会执行，如果我们想在**页面首次加载后立即对数据进行监听**，需要使用到给 watch 添加另外一个属性 immediate



1. 使用场景：让`Vue`初次渲染时，数据还没有发生变化，就调用一下监视的回调函数（`handler`）
2. 具体配置：`immediate:true`



**落地代码：**

```html
<script>
Vue.config.productionTip = false

const vm = new Vue({
    
  // coding ....
  // coding ....
  // coding ....
 
  // 侦听器
  watch: {
    // 侦听器，侦听，就是监听某个数据的改变 
    isHot: {
      handler(newVal, oldVal) {
        const dog1 = 'https://img0.baidu.com/it/u=2712974893,3591500984&fm=253&fmt=auto&app=138&f=JPEG'
        const dog2 = 'https://img2.baidu.com/it/u=3263682967,163100295&fm=253&fmt=auto&app=138&f=JPEG'

        this.dogUrl = newVal ? dog1 : dog2
      },
      
      // 在页面首次加载后立即对数据进行监听，也就是立即执行 watch
      immediate: true
    }
  }
})

</script>
```





###  8.4 监听器深度监视

> 🎯 目标：掌握监听器如何进行深度监听

**知识点：**

如果使用 watch 对对象进行监听，当对象中的属性值发生了变化后，监听器无法被监听，这个时候就需要使用 deep 选项

1. `Vue`中的`watch`默认不监测**对象内部属性**的改变。
2. 配置`deep:true`可以监测**对象内部属性**的改变。



**注意事项：**

1. `Vue`底层，是可以监测对象内部值的改变，但给我们提供的`watch`默认不可以！
2. 使用`watch`时，要根据数据的具体结构，来决定是否采用深度监视。



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
    img {
      width: 200px;
      height: 200px;
    }
  </style>
</head>

<body>

  <div id="app">
    <p>姓名：{{ hero.name }}</p>
    <p>年龄：{{ hero.age }}</p>
    <p>武器：{{ hero.arms }}</p>
    <button @click="changeAge">改变年龄</button>

    <button @click="hero = { name: '猪八戒', age: 10, arms: '九齿钉耙' }">替换对象</button>
  </div>

  <script src="../vue.js"></script>
  <script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        a: 'a',
        hero: {
          name: '孙悟空',
          age: 1000,
          arms: '金箍棒'
        }
      },
      methods: {
        changeAge() {
          this.hero.age += 1
        }
      },
      watch: {
        // 如果直接对进行监听，那么只有当对象改变(直接替换对象)的时候才会被监听到
        // hero: {
        //   handler(newVal, oldVal) {
        //     console.log(newVal, oldVal)
        //   }
        // }

        // 可以直接写成对象点的方式，但是需要外层加上引号
        // 这种用法可以监听到对象中具体的属性
        // 'hero.age': {
        //   handler (newVal, oldVal) {
        //     console.log(newVal, oldVal)
        //   }
        // }

        // 添加 deep: true
        // 对象内部的属性的值发生了改变，watch 就会被触发
        hero: {
          handler(newVal, oldVal) {
            console.log(newVal, oldVal)
          },
          deep: true
        }
      }
    })

  </script>
</body>

</html>
```





###  8.5 监听器简写形式

> 🎯 目标：掌握监听器的简写方法

**知识点：**

1. 明确：当不需要【立即监视】、【深度监视】的时候，才可以用简写形式。

2. 示例代码（`isHot`函数，就相当于完整写法中的`handler`）：

   ```js
   watch:{
   	isHot(){
       /*********/
     }
   }
   ```





**落地代码：**

```html
<script>
    Vue.config.productionTip = false

    const vm = new Vue({
      el: '#app',
      data: {
        isHot: true,
        dogUrl: 'https://img0.baidu.com/it/u=2712974893,3591500984&fm=253&fmt=auto&app=138&f=JPEG',
      },
      methods: {
        changeWeather() {
          this.isHot = !this.isHot
        }
      },

      // 侦听器
      watch: {
         
        // 完整写法
        // isHot: {
        //   handler(newVal, oldVal) {

        //     const dog1 = 'https://img0.baidu.com/it/u=2712974893,3591500984&fm=253&fmt=auto&app=138&f=JPEG'
        //     const dog2 = 'https://img2.baidu.com/it/u=3263682967,163100295&fm=253&fmt=auto&app=138&f=JPEG'

        //     this.dogUrl = newVal ? dog1 : dog2
        //   }
        // },

	    // 简洁写法 
        isHot(newVal, oldVal) {
          const dog1 = 'https://img0.baidu.com/it/u=2712974893,3591500984&fm=253&fmt=auto&app=138&f=JPEG'
          const dog2 = 'https://img2.baidu.com/it/u=3263682967,163100295&fm=253&fmt=auto&app=138&f=JPEG'

          this.dogUrl = newVal ? dog1 : dog2
        }

      }
    })

  </script>
```




















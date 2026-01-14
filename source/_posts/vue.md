---
title: vue
date: 2026-01-14 10:20:38
tags: 
  - frontEnd
  - vue
categories:
  - frontEnd
  - vue
---

#### git


passphrase for key '/c/Users/ycc/.ssh/id_ed25519' : 123



#### MongoDB


##### objectId


+ 前4个字节表示时间戳
+ 接下来的3个字节是机器标识码
+ 紧接的两个字节由进程id组成（PID）
+ 最后三个字节是随机数



#### linux


##### path


```html
# 如果/etc/bashrc文件不可编辑，需要修改为可编辑
chmod -v u+w /etc/bashrc

vim /etc/bashrc

# 在最后一行加上
export PATH=$PATH:/home/uusama/mysql/bin

生效时间：新开终端生效，或者手动source /etc/bashrc生效
生效期限：永久有效
生效范围：对所有用户有效
```



#### ts


```tsx
接口
interface Iperson{
    firstName : string
    lastName : string
}
类
class Person{
    firstName : string
    lastName : string
    constructor(firstName : string,lastName : string){
        this.firstName = firstName
        this.lastName = lastName
    }
}
```



#### Vue3


##### api


###### `{{}}`


```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```



###### v-click


methods:{}



```html
$event获取原生事件

<button @click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>

methods: {
  warn(message, event) {
    // 现在可以访问到原生事件
    if (event) {
      event.preventDefault()
    }
    alert(message)
  }
}
事件修饰符
<!-- 阻止单击事件继续冒泡 -->
<a @click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a @click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form @submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div @click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div @click.self="doThat">...</div>

<!-- 点击事件将只会触发一次 -->
<a @click.once="doThis"></a>

<!-- 滚动事件的默认行为 (即滚动行为) 将会立即触发，   -->
<!-- 而不会等待 `onScroll` 完成，                    -->
<!-- 以防止其中包含 `event.preventDefault()` 的情况  -->
<div @scroll.passive="onScroll">...</div>  //提升性能
按键修饰符
<input @keyup.enter="submit" />
.enter
.tab
.delete (捕获“删除”和“退格”键)
.esc
.space
.up
.down
.left
.right
.ctrl
.alt
.shift
win键
.meta
.exact (单一控制)
鼠标
.left
.right
.middle
```



###### v-model


双向绑定



```html
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg" />

<input v-model.number="age" type="text" />

<input v-model.trim="msg" />
```



###### v-bind:attr


```html
动态参数
<a v-bind:[attributeName]="url"> ... </a>
attributeName最终的参数的使用

缩写
<!-- 完整语法 -->
<a v-bind:href="url"> ... </a>

<!-- 缩写 -->
<a :href="url"> ... </a>

<!-- 动态参数的缩写 -->
<a :[key]="url"> ... </a>

非prop的atttr禁用
app.component('date-picker', {
  inheritAttrs: false,
  template: `
    <div class="date-picker">
      <input type="datetime-local" v-bind="$attrs" />
    </div>
  `
})
<!-- date-picker 组件使用非 prop 的 attribute -->
<date-picker data-status="activated"></date-picker>

<!-- 渲染后的 date-picker 组件 -->
<div class="date-picker">
  <input type="datetime-local" data-status="activated" />
</div>

多个根节点的attr继承
<custom-layout id="custom-layout" @click="changeValue"></custom-layout>
// 这将发出警告
app.component('custom-layout', {
  template: `
    <header>...</header>
    <main>...</main>
    <footer>...</footer>
  `
})

// 没有警告，$attrs 被传递到 <main> 元素
app.component('custom-layout', {
  template: `
    <header>...</header>
    <main v-bind="$attrs">...</main>
    <footer>...</footer>
  `
})
```



###### v-if


###### v-for


```html
带index的数组遍历
v-for="(item, index) in items"  <===> v-for="(item, index) of items" 
带index的对象遍历
v-for="(value, name, index) in myObject"
建议带key
v-for="item in items" :key="item.id"
```



###### props


父组件传递数据给子组件



###### v-once


能执行一次性地插值，当数据改变时，插值处的内容不会更新



###### v-html


使用原始html，即解析为html，而字符串



###### v-on


```html
缩写
<!-- 完整语法 -->
<a v-on:click="doSomething"> ... </a>

<!-- 缩写 -->
<a @click="doSomething"> ... </a>

<!-- 动态参数的缩写 -->
<a @[event]="doSomething"> ... </a>
```



###### data


`data` 选项是一个函数,`$data` 的形式存储在组件实例中



###### methods


###### 防抖和节流


```vue
lodash库      _.debounce函数

app.component('save-button', {
  created() {
    // 使用 Lodash 实现防抖
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // 移除组件时，取消定时器
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... 响应点击 ...
    }
  },
  template: `
    <button @click="debouncedClick">
      Save
    </button>
  `
})
```



###### computed:{a1()}


ex :   {{a1}}



计算属性基于响应式依赖



如果不希望有缓存，请用 `method` 来替代



```javascript
settter 
默认只有getter
computed: {
  fullName: {
    // getter
    get() {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set(newValue) {
      const names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```



###### watch:{b1()}


ex: v-model ="b1"



###### :class


```html
三元组模板简化语法

<div :class="[isActive ? activeClass : '', errorClass]"></div>
<div :class="[{ active: isActive }, errorClass]"></div>
$attrs 定义哪些部分将接收这个 class

<div id="app">
  <my-component class="baz"></my-component>
</div>

const app = Vue.createApp({})

app.component('my-component', {
  template: `
    <p :class="$attrs.class">Hi!</p>
    <span>This is a child component</span>
  `
})
```



###### :style


```html
<div :style="styleObject"></div>
data() {
  return {
    styleObject: {
      color: 'red',
      fontSize: '13px'
    }
  }
}
```



###### $emit()


子组件调用父组件声明的方法



```html
父组件
<blog-post ... @enlarge-text="postFontSize += 0.1"></blog-post>
子组件
// method 1
<button @click="$emit('enlargeText')">
  Enlarge text
</button>
//method 2
<button @click="getLarger">
  Enlarge text
</button>
import {defineEmits} from 'vue'
const emit = defineEmits(["enlargeText"])
const getLarger = ()=>{
	emit('enlargeText')
	// emit('enlargeText',$event)  $event 传参到父组件 
}
```



多个v-model绑定



```html
<user-name
  v-model:first-name="firstName"
  v-model:last-name="lastName"
></user-name>

app.component('user-name', {
  props: {
    firstName: String,
    lastName: String
  },
  emits: ['update:firstName', 'update:lastName'],
  template: `
    <input 
      type="text"
      :value="firstName"
      @input="$emit('update:firstName', $event.target.value)">

    <input
      type="text"
      :value="lastName"
      @input="$emit('update:lastName', $event.target.value)">
  `
})
```



验证抛出事件



```javascript
app.component('custom-form', {
  emits: {
    // 没有验证
    click: null,

    // 验证 submit 事件
    submit: ({ email, password }) => {
      if (email && password) {
        return true
      } else {
        console.warn('Invalid submit event payload!')
        return false
      }
    }
  },
  methods: {
    submitForm(email, password) {
      this.$emit('submit', { email, password })	
    }
  }
})
```



$event访问到被抛出的值



```html
父组件
<blog-post ... @enlarge-text="onEnlargeText"></blog-post>
methods: {
  onEnlargeText(enlargeAmount) {
    this.postFontSize += enlargeAmount
  }
}
子组件
<button @click="$emit('enlarge-text', 0.1)">
  Enlarge text
</button>
```



###### slot


###### is


##### 组合api


```tsx
setup 
入口函数，初始化时执行一次

ref 
定义一个响应式数据
const count = ref(0)
count.value++
{{count}}

reactive
定义一个响应式对象,返回proxy对象user ,target对象obj
const obj ={
    name:"xioami",
    age : 20
}
const user = reactive(obj)
user.name
{{user.name}}
操作proxy对象user页面会变化，target对象obj不会
```



##### computed methods


computed :缓存结果



methods: 每次渲染重新计算



##### 生命周期：






beforecreated



created (data渲染)



berforemouted (  #el存在,模板渲染)



mouted （模板中数据渲染）



beforeupdated



updated



beforedestory



destory



#### 响应式


<350



350-640



640-1000



##### flex


:设为Flex布局以后，子元素的float、clear和vertical-align属性将失效



###### flex-direction


flex-direction: row | row-reverse | column | column-reverse;  
row（默认值）：主轴为水平方向，起点在左端。  
row-reverse：主轴为水平方向，起点在右端。  
column：主轴为垂直方向，起点在上沿。  
column-reverse：主轴为垂直方向，起点在下沿。



###### flex-wrap


flex-wrap: nowrap | wrap | wrap-reverse;  
nowrap（默认）：不换行  
wrap：换行，第一行在上方。  
wrap-reverse：换行，第一行在下方。



###### flex-flow


flex-flow:  ;  
flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap



###### justify-content


justify-content: flex-start | flex-end | center | space-between | space-around;  
主轴上的对齐方式    左对齐        右对齐      居中      项目有间隔       项目有间隔，与边框也有间隔



###### align-items


align-items: flex-start | flex-end | center | baseline | stretch;  
项目在交叉轴上如何对齐





###### align-content


align-content: flex-start | flex-end | center | space-between | space-around | stretch;



定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用





+ flex-start：与交叉轴的起点对齐。
+ flex-end：与交叉轴的终点对齐。
+ center：与交叉轴的中点对齐。
+ space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
+ space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
+ stretch（默认值）：轴线占满整个交叉轴



###### order


定义项目的排列顺序。数值越小，排列越靠前，默认为0



###### flex-grow & flex-shrink


定义项目的放大比例(defalut 0 )  & 缩小比例(defult 1)



###### flex-basis


```css
flex-basis: <length> | auto; /* default auto */ 空间大小
```



###### flex


flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto



###### align-self


align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch



##### css


+  分组选择器

```plain
h1,h2,p
{
    color:green;
}
```



+  嵌套选择器
    - p{ }: 为所有 **p** 元素指定一个样式。
    - .marked{ }: 为所有 **class="marked"** 的元素指定一个样式。
    - .marked p{ }: 为所有 **class="marked"** 元素内的 **p** 元素指定一个样式。
    - p.marked{ }: 为所有 **class="marked"** 的 **p** 元素指定一个样式。
+  优先级  
   第一等级：代表内联样式，style=""，权值为1000  
   第二等级：代表ID选择器，如#id，权值为100  
   第三等级：代表类，伪类和属性选择器，如.content，权值为10  
   第四等级：代表标签选择器和伪元素选择器，如div p，权值为1 


> Vue + Element-UI 简单的信息录入表格案例
>

* [环境搭建](#环境搭建)
* [自定义组件](#自定义组件)
	* [方法绑定](#方法绑定)
	* [项目打包](#项目打包)

### 环境搭建

> 首先系统需要安装 Node.js 提供一个 Javascript 运行环境：Node 是一个服务器端 JavaScript 解释器。
>
> 系统命令行输入`node -v`查看 Node.js 是否安装成功。

利用 npm 命令安装 Vue：

```shell
 npm install vue
 npm install --global vue-cli
 npm install webpack -g
 # 进入到存放目录的目录下进行项目初始化
 vue init webpack demo_vue 
```

初始化项目选项：

![](http://gudtwingo.gitee.io/image/2020/04/Vue/VueStart.png)

将项目导入 IDEA IntelliJ，命令行中输入`npm run dev`运行项目。（IDEA 中需要安装 Vue.js 插件）

![](http://gudtwingo.gitee.io/image/2020/04/Vue/VueStartSuccess.png)

> 在 IDEA 中打开项目目录中的文件时可能会报错，错误原因是 Js 的版本太低了，在配置中修改 Js 的版本即可。

配置 Element-UI 依赖，打开 package.json，找到 devDependencies 并在最后加上`"element-ui": “^2.2.1”`，再通过`cnpm install`进行安装，安装完成后`npm run dev`启动。打开 main.js 在里面添加三行内容。

```js
import ElementUI from 'element-ui' // 导入依赖
import 'element-ui/lib/theme-chalk/index.css' // 避免后期打包样式不同: ...'./App'; 之前
// ...
Vue.use(ElementUI) // 注册
// ...
```

新建一个组件 NewContact.vue：

```vue
<template>
  <el-row>
    <el-button type="success">1</el-button>
  </el-row>
</template>
<script>

</script>

<style>

</style>
```

打开项目的初始组件 HelloWorld.vue 对其进行修改，并且打开 router/index.js 添加自定义的路由配置：

```vue
<template>
<!-- 路由标签 -->
    <router-link to="newcontact"><h1>{{ msg }}</h1></router-link>
</template>
```

```js
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld' // 引入组件: @ 代表根目录
import NewContact from '@/components/NewContact'
Vue.use(Router)
export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
    {
      path: '/newcontact',// 与 router-link to 相对应，路由到 /newcontact
      name: 'NewContact',
      component: NewContact
    }
  ]
})
```

页面效果：点击 Rout to NewContact 路由到 NewContact 组件对应的页面。

### 自定义组件

NewContact 组件中代码如下：

> `@Click`用于绑定函数；
> `:data`用于绑定数据；
> 表格绑定数据情况下·prop·用于数据传递；
> `slot-scope="del"`定义作用域，用于获取 table 的状态。

```vue
<template>
  <el-row>
    姓名：{{info.name}}
    <el-input v-model="info.name" placeholder="请输入姓名"></el-input>
    年龄：{{info.age}}
    <el-input v-model="info.age" placeholder="请输入年龄"></el-input>
    性别：{{info.sex}}
    <el-select v-model="info.sex" placeholder="请选择">
      <el-option v-for="item in options" :key="item" :value="item"></el-option>
    </el-select>
    </el-select>
    <el-button @click="create">创建</el-button>
    <template>
      <el-table :data="tabledata" align="left">
        <el-table-column prop="name" label="姓名"></el-table-column>
        <el-table-column prop="age" label="年龄"></el-table-column>
        <el-table-column prop="sex" label="性别"></el-table-column>
        <el-table-column label="操作">
          <template slot-scope="de">
            <el-button size="mini" type="danger" @click="del(de.$index)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </template>
  </el-row>
</template>
<script>
  export default {
    name: "NewContact",
    data() {
      return {
        info: {
          name: '',
          age: '',
          sex: ''
        },
        options: [
          '女', '男',
        ],
        tabledata: [
          {name: 'test01', age: 11, sex: 'man'},
          {name: 'test02', age: 22, sex: 'women'},
          {name: 'test03', age: 33, sex: 'men'}
        ]
      }
    }
  }
</script>
<style>
</style>
```

此时访问页面：localhost:8080/#/newcontact 显示如下：

![](http://gudtwingo.gitee.io/image/2020/04/Vue/VueDemo01.png)

#### 方法绑定

```vue
<script>
data(){
  export default {
    name: "NewContact",
    data() {
		// ...
	}
    methods: {
    	create(){
            // v-model 双向绑定：用户填写的信息自动绑定到对应的对象中
     		this.tabledata.push(this.info) // 给 tabledata 添加一个对象（之前我们创建的 info ）
      		this.info =  {name: '', age: '', sex: ''} // 点击创建后，让option还原，而不是停留在所选的项
    	},
   	 	del(index){
            // splice(修改位置, 移除元素个数)
      		this.tabledata.splice(index,1) // 删除点击的对象，index 是 de.$index传过来的
    	}
  }
</script>
```

添加完自定义的方法之后就可以正常的进行添加和删除信息的操作了。

#### 本地存储数据

在 src 文件夹下新建一个 store 文件夹，即与 App.vue、components 同属一个层级，在里面新建一个 store.js。

```js
const STORAGE_KEY = 'info'//名字随便起
export default {//向往输出，以便组件接收
	fetch() {//我们定义的获取数据的方法
		return JSON.parse(window.localStorage.getItem(STORAGE_KEY)
		 || '[]')
	},
	save(items) {//我们定义的保存数据的方法
		window.localStorage.setItem(STORAGE_KEY, JSON.stringify(items))
	}
}
```

`getItem`和`setItem`是 window.localStorage 的获取和保存数据的方法，获取的数据使用 JSON.stringify 和 JSON.parse 把数据转成字符串和解析，方便我们写入 tabledata。

```vue
<script>
data(){
  export default {
    name: "NewContact",
    data() {
		// ...
	}
    methods: {
		// ...
    	}
	watch: { // watch 是 vue 的监听，一旦监听对象有变化就会执行相应操作
	tabledata: { // 被监听的对象
      handler(items){
          Storage.save(items)
      }, // 监听对象变化后所做的操作，保存数据
      deep: true // 深度监听，防止数据层级过多监听不到  
    }
  }
</script>
```

测试结果：

![](http://gudtwingo.gitee.io/image/2020/04/Vue/VueDemo02.png)

#### 项目打包

打开 /config/index.js 进行一点小修改。

```js
build: {
    index: path.resolve(__dirname, '../dist/index.html'),

    assetsRoot: path.resolve(__dirname, '../dist'),
    assetsSubDirectory: 'static',
    assetsPublicPath: './', // 加了个 . 避免生产路径的错误

    productionSourceMap: false, // 修改为 false 
}
```

打包成功后在项目路径下生成一个 dist 文件夹，打开 index.html 即可运行此项目。
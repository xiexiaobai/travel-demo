# Travel
> Vue 2.5 开发移动端旅游网站项目整体流程与记录。

项目整体总结与记录。欢迎 `Star` 和 `Fork`。

## 效果预览
扫描二维码：

![](https://img-1257191344.cos.ap-chengdu.myqcloud.com/%E6%97%85%E6%B8%B8%E7%BD%91%E4%BA%8C%E7%BB%B4%E7%A0%81.png)

![](https://upload-images.jianshu.io/upload_images/12904618-501ca21cf363890e.gif?imageMogr2/auto-orient/strip)

## 项目涉及到技术栈：
- Vue：Vue、Vue-router、Vuex、Vue-cli
- 插件：vue-awesome-swiper、better-scroll、axios
- CSS的预处理框架：stylus
- api：后台数据接口

## 项目特点
- 组件化自适应布局
- 代码，简洁，易维护
- 兼容大部分浏览器
- 实现性能优化

## 项目具体结构
### 首页部分
- iconfont 的引入和使用
- 图片轮播组件的使用
- 图标区域轮播组件的使用
- axios获取接口数据
- 组件间数据传递

### 城市选择页部分
- 字母表布局
- better-scroll 的使用
- 函数节流实现列表性能优化
- 搜索逻辑实现
- Vuex 实现数据共享
- LocalStorage 实现页面数据存储
- keep-alive 优化路由性能

### 详情页部分
- banner 布局
- 动态路由配置
- 公用画廊组件拆分
- 实现 fixed header 渐隐渐显效果
- 递归组件实现详情列表
- transition slot 插槽实现 animation 简单动画效果

# 项目相关
## 项目相关 npm 依赖包
- fastClick: 用来处理移动端 `click` 事件 300毫秒延迟
- stylus: CSS 预处理框架
- stylus-loader
- vue-awesome-swiper: 轮播插件
- axios: 实现 `ajax`

- better-scroll: scroll插件
- vuex: 实现数据共享

## 设置样式变量
通过 variable.styl 设置样式变量，抽离出公用样式。以方便维护

# 首页
## HomeSwiper 组件
### 使用 vue-awesome-swiper 轮播插件
使用 2.6.7 版本
```
npm install vue-awesome-swiper@2.6.7 --save
```
具体参考 [vue-awesome-swiper](https://github.com/surmon-china/vue-awesome-swiper)

轮播图当中的 `CSS` 样式重点
该样式主要是防止网速过慢时页面布局的抖动，其含义是，`wrapper` 宽度 `100%`，高度由宽度的 `27%` 自动撑开。
```CSS
.wrapper {
  overflow: hidden;
  width: 100%;
  height: 0;
  padding-bottom: 27%;  
}
```
或者写成
```CSS
.wrapper {
  width: 100%;
  height: 27vw;
}
```

## HomeIcons 组件
### iconsList 分页
同样使用 `swiper` 进行分页，并利用以下方式实现自动构建多页切换的功能
```JavaScript
computed: {
  //根据数据项目的不同，自动构建icons多页切换功能
  pages () {
    const pages = []
    this.iconsList.forEach((item, index) => {
      const page = Math.floor(index / 8)
      if (!pages[page]) {
        pages[page] = []
      }
      pages[page].push(item)
    })
    return pages
  }
}
```
### ellipsis()样式封装
将 `ellipsis` 封装在 `mixins.styl` 文件中
```JavaScript
ellipsis()
  overflow: hidden
  white-space: nowrap
  text-overflow: ellipsis
```

## Recommend / Weekend 组件
设置 `min-width` 是为了让 `ellipsis()` 生效
```css
.item-info {
  flex: 1;
  padding: .1rem;
  min-width: 0;
}
```

## index-ajax
使用 `axios` 进行 ajax 请求
```
npm install axios --save
```
### .gitignore 设置
添加 `staitc/mock`，防止被推送到仓库

### 设置 mock数据 开发环境转发代理
设置 `config` 文件夹下的 `index.js`

设置 `module.exports` 下 `dev` 的 `proxyTable` 代理

webpack-dev-server 工具会自动将 `/api` 替换成 `/static/mock`
```JavaScript
proxyTable: {
  '/api': {
    target: 'http://localhost:8080',
    pathRewrite: {
      '^/api': '/static/mock'
    }
  }
}
```


# 城市页
## router-link
通过路由实现页面间跳转，在外层添加 `router-link`。`to` 后面跟需要跳转的 path 。
```HTML
    <router-link to="/city">
      <div class="header-right">
        {{this.city}}
        <span class="iconfont icon-jiantou"></span>
      </div>
    </router-link>
```

然后在 router 文件夹的相应 `index.js` 路由配置文件中进行 path、name 和 `component` 的声明，并进行 `import from`。即完成了路由配置。
```JavaScript
import Vue from 'vue'
import Router from 'vue-router'
import Home from '@/pages/home/Home'
import City from '@/pages/city/City'

Vue.use(Router)

export default new Router({
  routes: [{
      path: '/',
      name: 'Home',
      component: Home
    }, {
      path: '/city',
      name: 'City',
      component: City
    }]
})
```

## city-List
修改一像素边框 `.border-topbottom` 的颜色
```
.border-topbottom
  &:before
    border-color: #ccc
  &:after
    border-color: #ccc
```

将页面固定住，后续搭配 `better-scroll` 插件实现类似于原生 app 的页面上下拖动效果
```CSS
.list {
  overflow: hidden;
  position: absolute;
  top: 1.58rem;
  left: 0;
  right: 0;
  bottom: 0;
}
```

### better-scroll 插件
```
npm install better-scroll --save
```
将 HTML DOM 结构调整成文档中规定的结构，在外层取 `wrapper`，引用插件之后，在 `mounted ()` 生命周期钩子里面新建一个这个 DOM 引用的实例。

```JavaScript
import Bscroll from 'better-scroll'
export default {
  name: 'CityList',
  //生命周期函数 挂载之后执行
  mounted () {
    //引用 wrapper DOM
    this.scroll = new Bscroll(this.$refs.wrapper)
  }
}
```

具体用法，请查看文档  [better-scroll](https://github.com/ustbhuangyi/better-scroll/blob/master/README_zh-CN.md)

### alphabet
是一个显示在右的 a-z 字母缩略指引

## city-ajax
按照 `index-ajax` 一样的方式进行 `axios` 数据获取
- 包括 热门城市、字母表排序城市列表、Alphabet 在内的部分都通过 `axios` 获取数据

在 `v-for` 循环输出 cities 的时候，需要注意，cities 是一个 `Object`
```JavaScript
props: {
  hot: Array,
  cities: Object
}
```
因此后面用 `v-for="(item, key) of cities"`，和 `v-for="innerItem of item"` 做循环输出
```HTML
<div class="area" v-for="(item, key) of cities" :key="key">
  <div class="title border-topbottom">{{key}}</div>
  <div class="item-list">
    <div class="item border-bottom" v-for="innerItem of item" :key="innerItem.id">{{innerItem.name}}</div>
  </div>
</div>
```

## city-components
兄弟组件间联动，这里没有采用 `bus`。
而是采用 `Alphabet.vue`(子组件) 传递给 `City.vue`(父组件) ，然后再通过 `City.vue`(父组件) 传递给 `List.vue`(子组件)。


在 `Alphabet.vue` 的 template 的循环展示中绑定 `@click` ，并在 methods 中使用 `$emit` 向外( `City.vue` 父组件 )发送 `change` 事件。

```HTML
<template>
  <ul class="list">
    <li class="item"
        v-for="(item, key) of cities"
        :key="key"
        @click="handleLetterClick"
    >
      {{key}}
    </li>
  </ul>
</template>
```

```JavaScript
methods: {
  handleLetterClick (e) {
    this.$emit('change', e.target.innerText)
  }
}
```


在 `City.vue` 的 template 中设置 `@change="handleLetterClick"` 监听 change 事件。
```HTML
<city-alphabet :cities="cities" @change="handleLetterClick"></city-alphabet>
```

在 `methods` 中定义事件 `handleLetterClick`，传递 `letter` 参数。
```JavaScript
methods: {
  handleLetterClick (letter) {
    this.letter = letter
  }
}
```

并在 `data` 中定义数据 `letter`。
```JavaScript
data () {
  return {
    cities: {},
    hotCities: [],
    letter: ''  // Alphabet 通过 change 事件传递过来的数据
  }
}
```

并传递给 `List.vue`。
```HTML
<city-list :cities="cities" :hot="hotCities" :letter="letter"></city-list>
```

然后在 `List.vue` 子组件 props 接收 `letter`
```JavaScript
props: {
  hot: Array,
  cities: Object,
  letter: String  // 接收 letter
}
```

通过侦听器 watch，侦听 `letter` 的变化。在此之前先用 `ref` 引用找到相应的 DOM
```HTML
<div class="area" v-for="(item, key) of cities" :key="key" :ref="key">
  <div class="title border-topbottom">{{key}}</div>
  <div class="item-list">
    <div class="item border-bottom" v-for="innerItem of item" :key="innerItem.id">{{innerItem.name}}</div>
  </div>
</div>
```

使用 `better-scroll` 中的 `scrollToElement` 方法进行点击跳转效果的实现
```JavaScript
watch: {
  letter () {
    if (this.letter) {
      const element = this.$refs[this.letter][0]
      this.scroll.scrollToElement(element)
    }
  }
}
```

## alphabet 滑动逻辑
上下滑动时，取字母位置逻辑：
- 获取 A 字母距离顶部高度
- 滑动时，取当前位置距离顶部高度
- 计算差值，得到当前手指位置与 A 字母顶部差值
- 除以每个字母高度，得出当前字母，触发 change 事件给外部

在 `Alphabet.vue` 中进行代码的编写
```HTML
<template>
  <ul class="list">
    <li class="item"
        v-for="item of letters"
        :key="item"
        :ref="item"
        @touchstart="handleTouchStart"
        @touchmove="handleTouchMove"
        @touchend="handleTouchEnd"
        @click="handleLetterClick"
    >
      {{item}}
    </li>
  </ul>
</template>

<script>
export default {
  name: 'CityAlphabet',
  props: {
    cities: Object
  },
  // 计算属性中定义 letters 是一个数组，从 cities 数据中遍历得到数据
  computed: {
    letters () {
      const letters = []
      for (let i in this.cities) {
        letters.push(i)
      }
      return letters
    }
  },
  data () {
    return {
      touchStatus: false  // 标识位
    }
  },
  methods: {
    handleLetterClick (e) {
      this.$emit('change', e.target.innerText)
    },
    handleTouchStart () {
      this.touchStatus = true
    },
    handleTouchMove (e) {
      if (this.touchStatus) {
        const startY = this.$refs['A'][0].offsetTop       // A 字母距离 header区域下沿 高度
        const touchY = e.touches[0].clientY - 79          // 手指距离 header区域下沿 高高度
        const index = Math.floor((touchY - startY) / 20)  // 当前字母下标
        if (index >= 0 && index < this.letters.length) {
          this.$emit('change', this.letters[index])       // 也通过 $emit 向外发送事件
        }
      }
    },
    handleTouchEnd () {
      this.touchStatus = false
    }
  }
}
</script>
```

实现效果解析图
![](https://upload-images.jianshu.io/upload_images/12904618-452638a279c6ce49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 函数节流优化
使用函数节流优化 `handleTouchMove`，提高性能
```JavaScript
handleTouchMove (e) {
  if (this.touchStatus) {
    // 使用函数节流优化性能
    if (this.timer) {
      clearTimeout(this.timer)
    }
    this.timer = setTimeout(() => {
      const startY = this.startY  
      const touchY = e.touches[0].clientY - 79  
      const index = Math.floor((touchY - startY) / 20)
      if (index >= 0 && index < this.letters.length) {
        this.$emit('change', this.letters[index])     
      }
    }, 16)
  }
}
```


## city-search 搜索功能逻辑
在 `template` 的 `input` 中做 `v-model="keyword"` 双向绑定。
```HTML
<template>
  <div>
    <div class="search">
      <input v-model="keyword" class="search-input" type="text" placeholder="输入城市名或拼音">
    </div>
    <div class="search-content">
      <ul>
        <li v-for="item of list">{{item.name}}</li>
      </ul>
    </div>
  </div>
</template>
```

在 `data ()` 中定义 `keyword`、`list` 和 `timer`。

在侦听器 `watch` 中侦听 `keyword` 的改变。

并使用函数节流进行优化。
```HTML
<script>
export default {
  name: 'CitySearch',
  props: {
    cities: Object
  },
  data () {
    return {
      keyword: '',
      list: [],
      timer: null
    }
  },
  watch: {
    keyword () {
      if (this.timer) {
        clearTimeout(this.timer)
      }
      this.timer = setTimeout(() => {
        const result = []
        for (let i in this.cities) {
          this.cities[i].forEach((value) => {
            if (value.spell.indexOf(this.keyword) > -1 ||
                value.name.indexOf(this.keyword) > -1) {
                  result.push(value)
            }
          })
        }
        this.list = result
      }, 100)
    }
  }
}
</script>
```


### 输入逻辑优化
#### 清空 input
由于数据是双向绑定的，所以在 `watch` 当中添加条件判断，当 `!this.keyword` 时，清空 `list`。
```JavaScript
if (!this.keyword) {
  this.list = []
  return
}
```

这样就实现了清空 `input` 搜索栏时，同时清空下面搜索结果的逻辑。

#### 没有找到匹配
添加 `li` ，其内容为 `没有找到匹配` 。同时用 `v-show` 指令，完成在没有匹配时候(`!list.length`)。显示该 `li` 内容，即 **没有找到匹配** 的功能。
```HTML
<li class="search-item border-bottom" v-show="!list.length">没有找到匹配</li>
```

#### search-content 显示与否
同样的使用 `v-show` 指令，决定是否显示 `class="search-content"` 这个 div 元素。决定的值为 `keyword`，这容易理解。
```HTML
<div class="search-content" ref="search" v-show="keyword">
  <ul>
    <li class="search-item border-bottom" v-for="item of list">{{item.name}}</li>
    <li class="search-item border-bottom" v-show="!list.length">没有找到匹配</li>
  </ul>
</div>
```


### 给 search-item 添加 better-scroll
给搜索结果页面也添加 `better-scroll` 使其多结果超出页面显示时，可以进行同样的 `better-scroll` 插件效果的滑动。

首先引入 `better-scroll`
``` JavaScript
import Bscroll from 'better-scroll'
```

使用 `ref` 引用 `search-content` 的元素
```HTML
<div class="search-content" ref="search">
  <ul>
    <li class="search-item border-bottom" v-for="item of list">{{item.name}}</li>
  </ul>
</div>
```

同样使用 `mounted` 生命周期钩子，传递的内容是 `this.$refs.search`
```JavaScript
mounted () {
  this.scroll = new Bscroll(this.$refs.search)
}
```

这样搜索结果页面结果过多超出页面时，也可以拥有 `better-scroll` 的滑动效果。


## 使用 Vuex 实现数据共享
需要实现 city 页面的数据传递给 index 首页。由于 `City.vue` 和 `Home.vue` 没有公用父级组件，这样就无法通过一个公用的父级组件进行数据的中转。这里我们使用 `Vuex` 数据层框架来实现。
[Vuex官方文档](https://vuex.vuejs.org/zh/)

### 安装并配置 Vuex
```
npm install vuex --save
```

创建 `store` 文件夹，建立 `index.js`，`state` 里放置全局公用数据 `city`。
```JavaScript
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    city: '重庆'
  },
  mutations: {
    changeCity (state, city) {
      state.city = city
    }
  }
})
```


在 `main.js` 的根实例下，将 `store` 传递进去。在其他子组件中使用 `this.$store` 进行派发。
```JavaScript
import store from './store'  //引入 store

new Vue({
  el: '#app',
  router: router,
  store: store,  //传递进入根实例的 store
  components: { App },
  template: '<App/>'
})
```

在 `List.vue` 和 `Search.vue` 组件中包含城市循环输出项的元素标签上定义 `@click="handleCityClick(item.name)"`。

并在相应的 `methods` 中执行 `Vuex` 的 `commit` 方法( 数据共享 ) 和 `Vue-router` 的 `push` 方法( 页面跳转 )
```JavaScript
methods: {
  handleCityClick (city) {
    this.$store.commit('changeCity', city)
    this.$router.push('/')
  }
}
```

### localStorage
使用 `localStorage` 实现城市保存的功能，在 `store` 的 `index.js` 文件中配置  `localStorage`
```JavaScript
export default new Vuex.Store({
  state: {
    city: localStorage.city || '重庆'
  },
  mutations: {
    changeCity (state, city) {
      state.city = city
      localStorage.city = city
    }
  }
})
```

有可能当用户使用隐身模式或禁用 `localStorage`，会导致浏览器报错。所以建议使用 `try catch` 进行优化
```JavaScript
let defalutCity = '重庆'
try {
  if (localStorage.city) {
    defaultCity = localStorage.city
  }
} catch (e) {}

export default new Vuex.Store({
  state: {
    city: defaultCity
  },
  mutations: {
    changeCity (state, city) {
      state.city = city
      try {
        localStorage.city = city
      } catch (e) {}
    }
  }
})
```


## keep-alive 优化
当查看 network 时候，可以看到从首页到城市选择页切换过程中每次切换都会发送 `ajax` 请求。所以我们对此进行优化。
![](https://upload-images.jianshu.io/upload_images/12904618-47ee5023f3389f10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 `App.vue` 中给 `<router-view/>` 外部添加一个 `<keep-alive>` 标签。其含义是路由的内容被加载过一次之后，就把路由的内容放置到内存中，下一次再使用路由的时候，无需重新加载组件、执行钩子函数。只需要从内存中拿出以前的内容显示就可以了。

![](https://upload-images.jianshu.io/upload_images/12904618-fb76f7866aa5c2d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### activated 生命周期钩子
结合 keep-alive 新增的 `activated` 生命周期钩子，实现每次点击曾经选中过的城市，不发送 `ajax`，城市选择变化的时候再进行 `ajax` 请求的优化。

![](https://upload-images.jianshu.io/upload_images/12904618-bb8ebd7a2578bbb9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 详情页
## :to 实现动态路由
使用 `tag` 将 `router-link` 标签替换成 `li`，从而不用修改样式就可以达到之前样式的效果。

然后按照下图所示进行动态路由的实现。即点击相应的列表选择选择动态跳转页面。
![](https://upload-images.jianshu.io/upload_images/12904618-29efb73abf75d048.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Banner 布局
### .banner-info 渐变效果
```CSS
.banner-info {
  background-image: linear-gradient(top, rgba(0, 0, 0, 0), rgba(0, 0, 0, .8))
}
```

## 全局画廊组件
新建 `common` 用来放置全局组件，建立 `gallary` 的 `Gallary.vue` 画廊组件，并在 `build/webpack.base.conf.js` 中进行路径别名指向的设置
```JavaScript
resolve: {
  extensions: ['.js', '.vue', '.json'],
  alias: {
    'vue$': 'vue/dist/vue.esm.js',
    '@': resolve('src'),
    'styles': resolve('src/assets/styles'),
    'common': resolve('src/common'),
  }
}
```

在 `Banner.vue` 中引入画廊组件，并在 `components` 中进行注册
```JavaScript
import CommonGallary from 'common/gallary/Gallary'
```

### Gallary.vue

![](https://upload-images.jianshu.io/upload_images/12904618-9f8bbf88a9a0fb7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

画廊组件内部也使用了 `awesome-swiper` ，所以同样使用 `swiper` 标签。`swiperOption` 设置的几个参数分别是，分页器样式，设置为分数形式的分页；还有解决点击进入画廊之后 `swiper` 无法进行滑动的 bug 问题。
```JavaScript
      swiperOption: {
        pagination: '.swiper-pagination',
        paginationType: 'fraction',  //设置分页器 样式为分式
        observeParents: true,  //swiper 插件监听到自身或父级元素DOM变化时，自动自我刷新。解决 swiper 刷新宽度计算 bug 的问题
        observer: true
      }
```

使用 `props` 接收外部传递过来的 imgs 参数。默认为空。并设置相应点击事件，并使用 `$emit` 传出。
```JavaScript
  methods: {
    handleGallaryClick () {
      this.$emit('close')
    }
  }
```

其中还需要注意样式相关的问题。在 `Gallary.vue` 中的分页器会因为 `.swiper` 标签自带的 `overflow: hidden` 而隐藏。使用 `>>>` 让 `.swiper-container` 继承 `.container` 的 `overflow` 属性即可。

![](https://upload-images.jianshu.io/upload_images/12904618-8e458bbf2da5962d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### Banner.vue 调用全局画廊
![](https://upload-images.jianshu.io/upload_images/12904618-e2d8e78dbd3531d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用 `@close="handleGallaryClose"` 接收 `close` 事件，订阅为 `handleGallaryClose` 事件。并在 `banner` 上创建 `handleBannerClick` 事件。实现点击进入画廊，再点击画廊退出的逻辑。
```HTML
<common-gallary :imgs="imgs" v-show="showGallary" @close="handleGallaryClose"></common-gallary>
```


## detail 页 header 渐变效果

### 模板内容

![](https://upload-images.jianshu.io/upload_images/12904618-c92ec78df518cb02.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 逻辑实现
通过 `showAbs` 、 `v-show` 和 `opacity` 完成该效果的实现。
利用 `activated` 钩子监听 `scroll` 触发 `this.handleScroll`。并在 `methods` 的 `handleScroll` 中完成渐隐渐现的算法逻辑。（通过 `document.documentElement.scrollTop` 计算 `opacity` 属性即可实现该动画效果）
![](https://upload-images.jianshu.io/upload_images/12904618-f5ed912a4a6a1d65.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 布局相关
`.header-fixed` 使用 `fixed` 定位到浏览器最上方。
![](https://upload-images.jianshu.io/upload_images/12904618-c82ae35d77e8dde1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 对全局事件解绑
之前在 `activated` 中监听 `scroll` 实际上带来了一些问题。因为如果在一个组件内部模板的某个标签上使用 `@click`，不会给其他标签和组件带来任何影响。但如果在组件中使用 `window` 这个全局对象的属性绑定，就会出现诸多 bug。因为相当于这个事件并不是绑定在该组件之中，而是绑定到了全局的 `window` 对象上。所以对其他的组件也产生了影响。

这个时候使用 `deactivated ` 这个生命周期钩子（页面即将被隐藏或替换成其他页面时）来解除全局事件的绑定。

![](https://upload-images.jianshu.io/upload_images/12904618-5e86dd2a7f77e7da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 递归组件实现详情列表
之所以在组件当中需要一个 `name` 属性，也是为了方便在组件自身调用自身出现递归的时候便于调用。下面可以看到，在下一个 `div` 标签中做一个 `v-if` 判断，如果存在 `item.children`。就把 `item.children` 当做 `list` 再传递给自身，进行递归调用。
![](https://upload-images.jianshu.io/upload_images/12904618-b71f1b673e6a0975.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在 `Detaile.vue` 中写入一些数据，分为三级。传入递归组件（子组件）中。

![](https://upload-images.jianshu.io/upload_images/12904618-16291ad023256f6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<br>
由于递归会自己调用自己，样式也会随之进行调整，可以看到以下效果。

![](https://upload-images.jianshu.io/upload_images/12904618-2dd273aa9988a2a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## detail - ajax
同理 `Home` 与 `City` 也 aixos 获取。在父组件进行 ajax 获取，再传递给每个子组件。

![](https://upload-images.jianshu.io/upload_images/12904618-f2e2428e45fb2de4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

每个子组件则通过 `props` 获取到相应的数据。

![](https://upload-images.jianshu.io/upload_images/12904618-b4031f41302a3135.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Detail 页禁用 keepalive
在 `App.vue` 的根实例中，在 `router-view` 之外的 `keep-alive` 包裹上加上 `exclude="Detail"` 即可。所以这也是 `name` 属性的又一个用途。

![](https://upload-images.jianshu.io/upload_images/12904618-e5514433b5268551.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 解决 exclude 带来的 bug
由于在 App.vue 中使用了 `keep-alive exclude="Detail"`，那么在 `Detail` 下的 `Header.vue` 中就不会执行 `activated` 钩子, 但是会执行 `created` 生命周期钩子。此时会出现`Detail` 页 `header` 头部渐隐渐现的效果不显现了。所以将监听 `scroll` 的事件写入到 `created` 中。修复此 bug。
![](https://upload-images.jianshu.io/upload_images/12904618-642f7452695b9552.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 解决滚动行为 bug
在 `router` 下面的 `index.js` 下添加。解决滚动行为的 bug。使每次做路由切换时，让新显示的页面回到最顶部。
![](https://upload-images.jianshu.io/upload_images/12904618-e53c956ab6cd44ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## animation 简单动画效果
在 `common` 公用组件当中新建 `fade` 文件夹，并创建 `FadeAnimation.vue`。用来实现简单的动画效果。

![](https://upload-images.jianshu.io/upload_images/12904618-16b7eb9ce8dabd7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

并在 `Banner.vue` 组件模板中的 `common-gallary` 外部加上 `fade-animation` 标签，相当于内部使用了插槽。从而实现 `FadeAnimation.vue` 中的动画效果。

![](https://upload-images.jianshu.io/upload_images/12904618-6675327d78f98673.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

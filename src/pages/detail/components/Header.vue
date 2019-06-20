<template>
  <div>
    <router-link
      tag="div"
      to="/"
      class="header-abs"
      v-show="showAbs"
    >
      <div class="iconfont icon-fanhui abs-fanhui"></div>
    </router-link>
    <div
      class="header-fixed"
      v-show="!showAbs"
      :style="opacityStyle"
    >
      <router-link to="/">
        <div class="iconfont icon-fanhui fix-fanhui"></div>
      </router-link>
      景点详情
    </div>
  </div>
</template>

<script>
export default {
  name: 'DetailHeader',
  data () {
    return {
      showAbs: true,
      opacityStyle: {
        opacity: 0
      }
    }
  },
  methods: {
    handleScroll () {
      //特别注意，scrollTop中的每一对 () 都是来自网络的一种取法。已兼容安卓浏览器和UA为苹果的浏览器
      const top = (window.parent.document.documentElement.scrollTop || window.parent.document.body.scrollTop) || (document.body.scrollTop + document.documentElement.scrollTop) || (window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop || 0)
      if (top > 60) {
        let opacity = top / 140
        opacity = opacity > 1 ? 1 : opacity
        this.opacityStyle = { opacity: opacity }
        this.showAbs = false
      } else {
        this.showAbs = true
      }
      // console.log(document.documentElement.scrollTop)
    }
  },
  // 每一次页面展示的时候执行 activated 生命周期钩子
  activated () {
    window.addEventListener('scroll', this.handleScroll)
  },
  // 使用 deactivated 生命周期钩子（页面即将被隐藏或替换成其他页面时） 对全局事件解绑
  deactivated () {
    window.removeEventListener('scroll', this.handleScroll)
  },
  created () {
  // 特别注意 如果在 App.vue 中使用了 keep-alive exclude="Detail"
  // 那么就不会执行 activated , 但是执行 created 生命周期钩子
  window.addEventListener('scroll', this.handleScroll)
}
}
</script>

<style lang="stylus" scoped>
  @import '~styles/varibles.styl'
  .header-abs
    position: absolute
    left: .2rem
    top: .2rem
    width: .8rem
    height: .8rem
    line-height: .8rem
    border-radius: .4rem
    text-align: center
    background: rgba(0, 0, 0, .8)
    .abs-fanhui
      font-size: .4rem
      color: #fff
  .header-fixed
    z-index: 2
    position: fixed
    top: 0
    left: 0
    right: 0
    height: $headerHeight
    line-height: $headerHeight
    text-align: center
    color: #fff
    background: $bgColor
    font-size: .32rem
    .fix-fanhui
      position: absolute
      top: 0
      left: 0
      width: .64rem
      text-align: center
      font-size: .4rem
      color: #fff
</style>

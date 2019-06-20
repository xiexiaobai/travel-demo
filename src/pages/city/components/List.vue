<template>
  <div class="list better-scroll-wrapper" ref="wrapper">
    <div class="better-scroll-content">
      <div class="area">
        <div class="title border-topbottom">当前城市</div>
        <div class="button-list">
          <div class="button-wrapper">
            <div class="button">{{this.$store.state.city}}</div>
          </div>
        </div>
      </div>
      <div class="area">
        <div class="title border-topbottom">热门城市</div>
        <div class="button-list">
          <div
            class="button-wrapper"
            v-for="item of hot"
            :key="item.id"
            @click="handleCityClick(item.name)"
          >
            <div class="button">{{item.name}}</div>
          </div>
        </div>
      </div>
      <div class="area" v-for="(item, key) of cities" :key="key" :ref="key">
        <div class="title border-topbottom">{{key}}</div>
        <div class="item-list">
          <div
            class="item border-bottom"
            v-for="innerItem of item"
            :key="innerItem.id"
            @click="handleCityClick(innerItem.name)"
          >
            {{innerItem.name}}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Bscroll from 'better-scroll'
export default {
  name: 'CityList',
  props: {
    hot: Array,
    cities: Object,
    letter: String  // 接收父组件 City 传递过来的 letter，一开始是由 Alphabet 传递的
  },
  methods: {
    handleCityClick (city) {
      this.$store.commit('changeCity', city)
      this.$router.push('/')
    }
  },
  //生命周期函数 挂载之后执行
  mounted () {
    //引用 wrapper DOM
    this.scroll = new Bscroll(this.$refs.wrapper)
  },
  //听过侦听器来侦听 letter 的变化
  watch: {
    letter () {
      if (this.letter) {
        const element = this.$refs[this.letter][0]
        this.scroll.scrollToElement(element)
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
  @import '~styles/varibles.styl'
  .border-topbottom
    &:before
      border-color: #ccc
    &:after
      border-color: #ccc
  .border-bottom
    &:before
      border-color: #ccc
  .list
    overflow: hidden
    position: absolute
    top: 1.58rem
    left: 0
    right: 0
    bottom: 0
    // background: red
    .title
      line-height: .54rem
      background: #eee
      padding-left: .2rem
      color: #666
      font-size: .26rem
    .button-list
      overflow: hidden
      padding: .1rem .6rem .1rem .1rem  //为了预留出右侧 Alphabet 字母索引空间
      .button-wrapper
        float: left
        width: 33.33%
        .button
          margin: .1rem
          padding: .1rem 0
          text-align: center;
          border: .02rem solid #ccc
    .item-list
      .item
        line-height: .76rem
        color: #666
        padding-left: .2rem
</style>

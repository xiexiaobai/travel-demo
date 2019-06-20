<template>
  <div class="container" @click="handleGallaryClick">
    <div class="wrapper">
      <swiper :options="swiperOption">
        <swiper-slide v-for="(item, index) in imgs" :key="index">
         <img class="gallary-img" :src="item" alt="">
        </swiper-slide>
       <div class="swiper-pagination" slot="pagination"></div>
      </swiper>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CommonGallary',
  props: {
    imgs: {
      type: Array,
      default () {
        return []
      }
    }
  },
  data () {
    return {
      swiperOption: {
        pagination: '.swiper-pagination',
        paginationType: 'fraction',  //设置分页器 样式为分式
        observeParents: true,  //swiper 插件监听到自身或父级元素DOM变化时，自动自我刷新。解决 swiper 刷新宽度计算 bug 的问题
        observer: true
      }
    }
  },
  methods: {
    handleGallaryClick () {
      this.$emit('close')
    }
  }
}
</script>

<style lang="stylus" scoped>
  .container >>> .swiper-container
    overflow: inherit
  .container
    display: flex
    flex-direction: column
    justify-content: center
    z-index: 99
    position: fixed
    left: 0
    right: 0
    top: 0
    bottom: 0
    background: #000
    .wrapper
      height: 0
      width: 100%
      padding-bottom: 66.4%   //宽高比
      .gallary-img
        width: 100%
      .swiper-pagination
        color: #fff
        bottom: -1rem
</style>

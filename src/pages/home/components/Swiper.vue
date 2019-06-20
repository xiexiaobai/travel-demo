<template>
  <!-- wrapper是为了防止布局抖动 -->
  <div class="wrapper">
    <swiper :options="swiperOption" v-if="showSwiper">
      <swiper-slide v-for="item of list" :key="item.id">
       <img class="swiper-img" :src="item.imgUrl" alt="">
      </swiper-slide>
     <div class="swiper-pagination" slot="pagination"></div>
    </swiper>
 </div>
</template>

<script>
export default {
  name: 'HomeSwiper',
  props: {
    list: Array
  },
  data () {
    return {
      swiperOption: {
        pagination: '.swiper-pagination',  //分页圆点 .swiper-pagination
        loop: true,   //开启循环轮播
        autoplay: 2500,
        speed: 1000,
      }
    }
  },
  computed: {
    //计算属性 解决数据传递过来时，轮播图显示最后一张的 bug
    showSwiper () {
      return this.list.length
    }
  }
}
</script>

<style lang="stylus" scoped>
  // 样式穿透
  .wrapper >>> .swiper-pagination-bullet-active
    background: #fff !important
  .wrapper
    // overflow: hidden
    width: 100%
    height: 0
    padding-bottom: 27%  // 200 比 750 的宽高比
    background: #eee
    .swiper-img
      width: 100%
</style>

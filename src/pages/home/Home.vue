<template>
  <div>
    <home-header></home-header>
    <home-swiper :list="swiperList"></home-swiper>
    <home-icons :list="iconList"></home-icons>
    <home-recommend :list="recommendList"></home-recommend>
    <home-weekend :list="weekendList"></home-weekend>
  </div>
</template>

<script>
import axios from 'axios'
import HomeHeader from './components/Header'
import HomeSwiper from './components/Swiper'
import HomeIcons from './components/Icons'
import HomeRecommend from './components/Recommend'
import HomeWeekend from './components/Weekend'
import { mapState } from 'vuex'
export default {
  name: 'Home',
  components: {
    HomeHeader: HomeHeader,
    HomeSwiper: HomeSwiper,
    HomeIcons: HomeIcons,
    HomeRecommend: HomeRecommend,
    HomeWeekend: HomeWeekend
  },
  data () {
    return {
      lastCity: '',  //临时缓存变量
      swiperList: [],
      iconList: [],
      recommendList: [],
      weekendList: []
    }
  },
  // 添加 city 参数让 首页改变时候重新加载 ajax 请求
  computed: {
    ...mapState(['city'])
  },
  methods: {
    getHomeInfo () {
      axios.get('/api/index.json?city=' + this.city)   // this.city 从 computed 中获取
        .then(this.getHomeInfoSucc)
    },
    getHomeInfoSucc (res) {
      res = res.data
      if (res.ret && res.data) {
        const data = res.data
        this.swiperList = data.swiperList
        this.iconList = data.iconList
        this.recommendList = data.recommendList
        this.weekendList = data.weekendList
      }
    }
  },
  // 挂载到页面之后的生命周期函数
  mounted () {
    this.lastCity = this.city
    this.getHomeInfo()
  },
  // keep-alive 新增生命周期函数，结合 lastCity 临时缓存变量使页面性能最优
  activated () {
    if (this.lastCity !== this.city) {
      this.lastCity = this.city
      this.getHomeInfo()
    }
  }
}
</script>

<style lang="css">
</style>

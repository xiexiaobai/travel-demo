<template>
  <ul class="list">
    <li class="item"
        v-for="item of letters"
        :key="item"
        :ref="item"
        @touchstart.prevent="handleTouchStart"
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
      touchStatus: false,  // 标识位
      startY: 0,
      timer: null
    }
  },
  //通过 updated 生命周期钩子，进行代码优化，不用再每次 handleTouchMove 事件触发时进行计算
  updated () {
    this.startY = this.$refs['A'][0].offsetTop
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
        // 使用函数节流优化性能
        if (this.timer) {
          clearTimeout(this.timer)
        }
        this.timer = setTimeout(() => {
          const startY = this.startY                        // A 字母距离 header区域下沿 高度
          const touchY = e.touches[0].clientY - 79          // 手指距离 header区域下沿 高高度
          const index = Math.floor((touchY - startY) / 20)  // 当前字母下标
          if (index >= 0 && index < this.letters.length) {
            this.$emit('change', this.letters[index])       // 也通过 $emit 向外发送事件
          }
        }, 16)
      }
    },
    handleTouchEnd () {
      this.touchStatus = false
    }
  }
}
</script>

<style lang="stylus" scoped>
  @import '~styles/varibles.styl'
  .list
    display: flex
    flex-direction: column
    justify-content: center
    position: absolute
    right: 0
    top: 1.58rem
    bottom: 0
    width: .4rem
    .item
      line-height: .4rem
      text-align: center
      color: $bgColor
</style>

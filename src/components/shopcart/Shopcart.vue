<template>
    <div class="shopcart">
        <div class="content">
            <div class="content-left">
                <div class="logo-wrapper">
                    <div class="logo" :class="{'highlight':totalCount>0}">
                        <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
                    </div>
                    <div class="num" v-show="totalCount">{{totalCount}}</div>
                </div>
                <div class="price border-right" :class="{'highlight':totalPrice>0}">￥{{totalPrice}}元</div>
                <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
            </div>
            <div class="content-right">
                <div class="pay" :class="payClass">
                    {{payDesc}}
                </div>
            </div>
        </div>
        <div class="ball-container">
            <transition-group name="drop"
              v-on:before-enter="beforeEnter"
              v-on:enter="enter"
              v-on:after-enter="afterEnter">
                <div v-for="(ball,index) in balls" :key="index+0" v-show="ball.show" class="ball">
                  <div class="inner inner-hook"></div>
                </div>
            </transition-group>
        </div>
    </div>
</template>
<script>
export default {
  name: 'Shopcart',
  data () {
    return {
      balls: [{
        show: false
      }, {
        show: false
      }, {
        show: false
      }, {
        show: false
      }, {
        show: false
      }],
      dropBalls: []
    }
  },
  methods: {
    cartAdd (el) {
      for (let i = 0; i < this.balls.length; i++) {
        let ball = this.balls[i]
        if (!ball.show) {
          ball.show = true
          ball.el = el
          this.dropBalls.push(ball)
          return
        }
      }
    },
    beforeEnter (el) {
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect()
          let x = rect.left - 32
          let y = -(window.innerHeight - rect.top - 22)
          // class="ball" is y direction transform
          el.style.display = ''
          el.style.webkitTransform = `translate3d(0,${y}px, 0)`
          el.style.transform = `translate3d(0,${y}px, 0)`
          // class="inner" is x direction transform
          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.webkitTransform = `translate3d(${x}px,0, 0)`
          inner.style.transform = `translate3d(${x}px,0, 0)`
        }
      }
    },
    enter (el) {
      /* eslint-disable no-unused-vars */
      let rf = el.offsetHeight
      this.$nextTick(() => {
        el.style.webkitTransform = 'translate3d(0, 0, 0)'
        el.style.transform = 'translate3d(0, 0, 0)'
        let inner = el.getElementsByClassName('inner-hook')[0]
        inner.style.webkitTransform = 'translate3d(0, 0, 0)'
        inner.style.transform = 'translate3d(0, 0, 0)'
      })
    },
    afterEnter (el) {
      // delete the first ball in dropBalls
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    }
  },
  props: {
    selectFoods: {
      type: Array,
      default () {
        return []
      }
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  },
  computed: {
    totalPrice () {
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    totalCount () {
      let count = 0
      this.selectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    payDesc () {
      if (this.totalPrice === 0) {
        return `￥${this.minPrice}元起送`
      } else if (this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice
        return `还差￥${diff}起送`
      } else {
        return '去结算'
      }
    },
    payClass () {
      if (this.totalPrice < this.minPrice) {
        return 'not-enough'
      } else {
        return 'enough'
      }
    }
  }
}
</script>
<style lang="stylus" scoped>
  .shopcart
    position: fixed
    width: 100%
    height: 0.96rem
    bottom: 0
    left: 0
    z-index: 10
    .content
      display: flex
      background: #141d27
      font-size: 0
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          position: relative
          top: -.1rem
          margin: 0 .24rem
          padding: .12rem
          width: 1.16rem
          height: 1.16rem
          box-sizing: border-box
          vertical-align: top
          border-radius: 50%
          background: #141d27
          .logo
            width: 100%
            height: 100%
            border-radius: 50%
            background: #2b343c
            text-align: center
            &.highlight
              background: rgb(0,160,220)
            .icon-shopping_cart
              line-height: .96rem
              font-size: .48rem
              color: #80858a
              &.highlight
                color: #ffffff
          .num
            position: absolute
            top: 0
            right: 0
            width: .48rem
            height: .32rem
            line-height: .32rem
            text-align: center
            border-radius: .32rem
            font-size: .18rem
            font-weight: 700
            color: #ffffff
            background: rgb(240,20,20)
            box-shadow: 0 .04rem .08rem 0 rgba(0,0,0,0.4)
        .price
          display: inline-block
          vertical-align: top
          line-height: .48rem
          padding-right: .24rem
          margin-top: .24rem;
          box-sizing: border-box
          font-size: .32rem
          font-weight: 700
          color: rgba(255,255,255,0.4)
          &.highlight
            color: #ffffff
        .desc
          display: inline-block
          vertical-align: top
          color: rgba(255,255,255,0.4)
          margin: .24rem 0 0 .24rem
          font-size: .2rem
          line-height: .48rem
      .content-right
        flex: 0 0 2.1rem
        width: 2.1rem
        .pay
          font-size: .24rem
          vertical-align: top
          color: rgba(255,255,255,0.4)
          height: .96rem
          line-height: .96rem
          text-align: center
          &.not-enough
            background: #2b333b
          &.enough
            background: #00b43c
            color: #ffffff
    .ball-container
      .drop-enter-active, .drop-leave-active
        transition: all 0.4s cubic-bezier(.49, -0.29, 0.75, 0.41)
      .ball
        position: fixed
        left: .64rem
        bottom: .44rem
        z-index: 200
        .inner
          width: .32rem
          height: .32rem
          background: rgb(0,160,220)
          border-radius: 50%
          transition: all 0.4s linear
</style>

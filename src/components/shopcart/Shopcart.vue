<template>
  <div>
    <div class="shopcart">
      <div class="content" @click="toggleList">
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
        <div class="content-right" @click.stop.prevent="pay">
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
      <transition name="fold"
        v-on:before-enter="foldBeforeEnter"
        v-on:enter="foldEnter"
        v-on:before-leave="foldBeforeLeave"
        v-on:leave="foldLeave">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li class="food border-bottom" v-for="(food,index) in selectFoods" :key="index">
                <span class="name">{{food.name}}</span>
                <div class="price">
                  <span>￥{{food.price}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cart-control :food="food"></cart-control>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
      </div>
      <transition name="fade">
        <div class="list-mask" @click="hideList" v-show="listShow">
        </div>
      </transition>
  </div>
</template>
<script>
import BScroll from 'better-scroll'
import CartControl from '@/components/cartcontrol/cartcontrol'
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
      dropBalls: [],
      fold: true
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
    pay () {
      if (this.totalPrice < this.minPrice) {
        return
      }
      window.alert(`支付${this.totalPrice}元`)
    },
    hideList () {
      this.fold = true
    },
    empty () {
      this.selectFoods.forEach((food) => {
        food.count = 0
      })
    },
    beforeEnter (el) {
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect()
          let x = rect.left - 32
          let y = -(window.innerHeight - rect.top - 22)
          // class="ball" y direction transform
          el.style.display = ''
          el.style.webkitTransform = `translate3d(0,${y}px, 0)`
          el.style.transform = `translate3d(0,${y}px, 0)`
          // class="inner" x direction transform
          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.webkitTransform = `translate3d(${x}px,0, 0)`
          inner.style.transform = `translate3d(${x}px,0, 0)`
        }
      }
    },
    foldBeforeEnter (el) {
      el.style.webkitTransform = `translate3d(0, 0, 0)`
      el.style.transform = `translate3d(0, 0, 0)`
    },
    foldBeforeLeave (el) {
      el.style.webkitTransform = `translate3d(0, -100%, 0)`
      el.style.transform = `translate3d(0, -100%, 0)`
    },
    enter (el) {
      /* eslint-disable no-unused-vars */
      let rf = el.offsetHeight
      this.$nextTick(() => {
        // style reset
        el.style.webkitTransform = 'translate3d(0, 0, 0)'
        el.style.transform = 'translate3d(0, 0, 0)'
        let inner = el.getElementsByClassName('inner-hook')[0]
        inner.style.webkitTransform = 'translate3d(0, 0, 0)'
        inner.style.transform = 'translate3d(0, 0, 0)'
      })
    },
    foldEnter (el) {
      this.$nextTick(() => {
        // style reset
        el.style.webkitTransform = 'translate3d(0, -100%, 0)'
        el.style.transform = 'translate3d(0, -100%, 0)'
      })
    },
    foldLeave (el) {
      this.$nextTick(() => {
        // style reset
        el.style.webkitTransform = 'translate3d(0, 0, 0)'
        el.style.transform = 'translate3d(0, 0, 0)'
      })
    },
    afterEnter (el) {
      // delete the first ball in dropBalls
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    },
    toggleList () {
      if (!this.totalCount) {
        this.fold = true
        return
      }
      this.fold = !this.fold
    }
  },
  components: {
    CartControl
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
    },
    listShow: {
      get: function () {
        return !this.fold
      },
      set: function () {
        if (!this.totalCount) {
          this.fold = true
          return false
        }
        let show = !this.fold
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.listContent, {
                click: true
              })
            } else {
              this.scroll.refresh()
            }
          })
        }
        return show
      }
    } /*
    listShow () {
      if (!this.totalCount) {
        this.fold = true
      }
      let show = !this.fold
      if (show) {
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.listContent, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      }
      return show
    } */
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
        transition: all 0.6s cubic-bezier(.49, -0.29, 0.75, 0.41)
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
          transition: all 0.6s linear
    .shopcart-list
      position: absolute
      left: 0
      top: 0
      z-index: -1
      width: 100%
      &.fold-enter-active, &.fold-leave-active
        transition: all 0.5s
      .list-header
        height: .8rem
        line-height: .8rem
        padding: 0 .36rem
        overflow: hidden
        background: #f3f5f7
        .title
          float: left
          font-size: .28rem
          color: rgb(7,17,27)
        .empty
          float: right
          font-size: .24rem
          color: rgb(0,160,220)
      .list-content
        padding: 0 0.36rem
        max-height: 4.34rem
        overflow: hidden
        background: #ffffff
      .food
        position: relative
        padding: 0.24rem 0
        box-sizing: border-box
        .name
          line-height: .48rem
          font-size: .28rem
          color: rgb(7,17,27)
        .price
          position: absolute
          right: 1.8rem
          bottom: .24rem
          line-height: .48rem
          font-weight: 700
          font-size: .28rem
          color: rgb(240,20,20)
        .cartcontrol-wrapper
          position: absolute
          right: 0
          bottom: .12rem
  .list-mask
    position: fixed
    top: 0
    left: 0
    width: 100%
    height: 100%
    z-index: 9
    opacity: 1
    background: rgba(7,17,27,0.6)
    backdrop-filter: blur(0.2rem)
    &.fade-enter-active, &.fade-leave-active
      transition: all 0.5s
    &.fade-enter, &.fade-leave-to
      opacity: 0
      background: rgba(7,17,27,0)
</style>

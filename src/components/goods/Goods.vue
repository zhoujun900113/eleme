<template>
    <div class="goods">
      <div class="menu-wrapper" ref="menuwrapper">
        <ul>
          <li v-for="(item,index) in goods" :key="index" class="menu-item" :class="{'current':currentIndex===index}" @click="selectMenu(index)"
          >
            <span class="text border-bottom">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
            </span>
          </li>
        </ul>
      </div>
      <div class="foods-wrapper" ref="foodswrapper">
        <ul>
          <li v-for="(item,index) in goods" :key="index" class="food-list food-list-hook">
            <h1 class="title">{{item.name}}</h1>
            <ul>
              <li v-for="(food,index) in item.foods" :key="index" class="food-item border-bottom">
                <div class="icon">
                  <img :src="food.icon">
                </div>
                <div class="content">
                  <h2 class="name">{{food.name}}</h2>
                  <p class="desc">{{food.description}}</p>
                  <div class="extra">
                    <span class="count">月售{{food.sellCount}}份</span>
                    <span>好评率{{food.rating}}%</span>
                  </div>
                  <div class="price">
                    <span class="now">￥{{food.price}}</span>
                    <span class="old" v-show="food.oldPrice"></span>
                  </div>
                  <div class="cartcontrol-wrapper">
                    <cart-control :food="food" @cartAdd="cartAdd"></cart-control>
                  </div>
                </div>
              </li>
            </ul>
          </li>
        </ul>
      </div>
      <shopcart ref="shopcart" :selectFoods="selectFoods" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></shopcart>
    </div>
</template>
<script>
import BScroll from 'better-scroll'
import axios from 'axios'
import Shopcart from '@/components/shopcart/Shopcart'
import CartControl from '@/components/cartcontrol/cartcontrol'
export default {
  name: 'Goods',
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0
    }
  },
  components: {
    Shopcart,
    CartControl
  },
  computed: {
    currentIndex () {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i + 1]
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i
        }
      }
      return 0
    },
    selectFoods () {
      let foods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food)
          }
        })
      })
      return foods
    }
  },
  methods: {
    _initScroll () {
      this.menuScroll = new BScroll(this.$refs.menuwrapper, {
        click: true
      })
      this.foodsScroll = new BScroll(this.$refs.foodswrapper, {
        click: true,
        probeType: 3
      })
      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
      })
    },
    _calculateHeight () {
      let foodList = this.$refs.foodswrapper.getElementsByClassName('food-list-hook')
      let height = 0
      this.listHeight.push(height)
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    selectMenu (index, event) {
      let foodList = this.$refs.foodswrapper.getElementsByClassName('food-list-hook')
      let el = foodList[index]
      console.log(index)
      this.foodsScroll.scrollToElement(el, 300)
    },
    cartAdd (el) {
      this.$refs.shopcart.cartAdd(el)
    },
    getGoodsInfo () {
      axios.get('/api/data.json')
        .then(this.handleGetGoodsInfoSucc)
        .catch(() => console.log('get data.json error'))
    },
    handleGetGoodsInfoSucc (res) {
      const data = res.data
      this.goods = data.goods
      this.$nextTick(() => {
        this._initScroll()
        this._calculateHeight()
      })
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  },
  mounted () {
    this.getGoodsInfo()
  }
}
</script>
<style lang="stylus" scoped>
  @import "../../common/stylus/mixin"
  @import "../../common/stylus/base"
  .goods
    display: flex
    position: absolute
    width: 100%
    top: 3.48rem
    bottom: .46rem
    overflow: hidden
    .menu-wrapper
      flex: 0 0 1.6rem
      width: .8rem
      background: #f3f5f7
      .menu-item
        display: table
        width: 1.12rem
        height: 1.08rem
        line-height: .3rem
        font-size: .24rem
        font-weight: 200
        padding: 0 0.24rem
        &.current
          background: #ffffff
          font-weight: 700
          z-index: 10
          .text
            border: none
        .icon
          display: inline-block
          width: .24rem
          height: .24rem
          vertical-align: top
          margin-right: .04rem
          background-size: .24rem .24rem
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          display: table-cell
          width: 1.12rem
          vertical-align: middle
          font-size: 0.12rem
    .foods-wrapper
      flex: 1
      background: #ffffff
      .title
        padding-left: .28rem
        height: .52rem
        line-height: .52rem
        border-left: .02rem solid #d9dde1
        font-size: .12rem
        color: rgb(147,153,159)
        background: #f3f5f7
      .food-item
        display: flex
        margin: .36rem
        padding-bottom: .36rem
        &:last-child
          border-bottom: none
          margin-bottom: 0
        .icon
          flex: 0 0 1.14rem
          margin-right: .2rem
          img
            width: 1.14rem
            height: 1.14rem
        .content
          flex: 1
          .name
            margin: 0.04rem 0 0.16rem 0
            height: .28rem
            line-height: 0.28rem
            font-size: .28rem
            color: rgb(7,17,27)
          .desc, .extra
            line-height: .2rem
            font-size: .2rem
            color: rgb(147,153,159)
          .desc
            margin-bottom: 0.16rem
            line-height: .3rem
          .extra
            .count
              margin-right: .12rem
          .price
            font-weight: 700
            line-height: .48rem
            .now
              margin-right: .16rem
              font-size: .28rem
              color: rgb(240,20,20)
            .old
              text-decoration: line-through
              font-size: .2rem
              color: rgb(147,153,159)
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom: .24rem
</style>

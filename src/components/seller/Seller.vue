<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc border-bottom">
          <star :size="36" :score="seller.score"></star>
          <span class="text">({{seller.ratingCount}})</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block border-right">
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>
              <span class="unit">元</span>
            </div>
          </li>
          <li class="block border-right">
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>
              <span class="unit">元</span>
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>
              <span class="unit">分钟</span>
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleFavorite">
          <span class="icon-favorite" :class="{active: active}"></span>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content-wrapper border-bottom">
          <p class="content">{{seller.bulletin}}</p>
        </div>
        <ul v-if="seller.supports" class="supports">
          <li class="support-item border-bottom" v-for="(item,index) in seller.supports" :key="index">
            <span class="icon" :class="classMap[seller.supports[index].type]"></span>
            <span class="text">{{seller.supports[index].description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <li class="pic-item" v-for="(item,index) in seller.pics" :key="index">
              <img :src="item">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="infos">
        <h1 class="title border-bottom">商家信息</h1>
        <div class="content-wrapper border-bottom">
          <ul v-if="seller.infos" class="supports">
            <li class="support-item border-bottom" v-for="(item,index) in seller.infos" :key="index">
              <span class="text">{{item}}</span>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import Star from '@/components/star/star'
import Split from '@/components/split/split'
import {saveToLocal, loadFromLocal} from 'common/js/store'
import BScroll from 'better-scroll'

export default {
  name: 'Seller',
  data () {
    return {
      active: (() => {
        return loadFromLocal(this.seller.id, 'favorite', false)
      })()
    }
  },
  computed: {
    favoriteText () {
      return this.active ? '已收藏' : '收藏'
    }
  },
  props: {
    seller: {
      type: Object
    }
  },
  components: {
    Star,
    Split
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  },
  watch: {
    seller () {
      this.$nextTick(() => {
        this._initPicScroll()
      })
    }
  },
  methods: {
    toggleFavorite () {
      this.active = !this.active
      saveToLocal(this.seller.id, 'favorite', this.active)
    },
    _initPicScroll () {
      if (this.seller.pics) {
        let picWidth = 120
        let margin = 6
        let width = this.seller.pics.length * (picWidth + margin) - margin
        this.$refs.picList.style.width = width + 'px'
        // better-scroll左右滚动
        this.$nextTick(() => {
          if (!this.picScroll) {
            this.picScroll = new BScroll(this.$refs.picWrapper, {
              scrollX: true,
              eventPassthrough: 'vertical'
            })
          } else {
            this.picScroll.refresh()
          }
        })
      }
    }
  },
  mounted () {
    // this._initPicScroll()
    this.$nextTick(() => {
      this.sellerScroll = new BScroll(this.$refs.seller, {
        click: true
      })
    })
  }
}
</script>
<style lang="stylus" scoped>
@import "../../common/stylus/mixin"
  .seller
    position: absolute
    top: 3.48rem
    left: 0
    bottom: 0
    width: 100%
    overflow: hidden
    .overview
      padding: .36rem
      .title
        margin-bottom: 0.16rem
        line-height: .28rem
        color: rgb(7,17,27)
        font-size: .28rem
      .desc
        padding-bottom: .36rem
        font-size: 0
        .star
          display: inline-block
          margin-right: .16rem
          vertical-align: top
        .text
          margin-right: .24rem
          line-height: .36rem
          display: inline-block
          vertical-align: top
          font-size: .2rem
          line-height: .36rem
          color: rgb(77,85,93)
      .remark
        display: flex
        padding-top: .36rem
        .block
          flex: 1
          text-align: center
          h2
            font-size: .2rem
            line-height: .2rem
            color: rgb(147,153,159)
          .content
            margin-top: .08rem
            color: rgb(7,17,27)
            line-height: .48rem
            .stress
              font-size: .48rem
            .unit
              font-size: .2rem
      .favorite
        position: absolute
        width: 1rem
        right: .22rem
        top: .36rem
        text-align: center
        .icon-favorite
          display: block
          margin-bottom: .08rem
          line-height: .48rem
          font-size: .48rem
          color: #d4d6d9
          &.active
            color: rgb(240,20,20)
        .text
          line-height: .2rem
          font-size: .2rem
          color: rgb(77,85,93)
    .bulletin
      padding: .36rem .36rem 0 .36rem
      .title
        margin-bottom: 0.16rem
        line-height: .28rem
        color: rgb(7,17,27)
        font-size: .28rem
      .content-wrapper
        padding: 0 .24rem .32rem .24rem
        .content
          font-size: .24rem
          line-height: .48rem
          color: rgb(240,20,20)
      .supports
        .support-item
          padding: 0.32rem .24rem
          font-size: 0
          &.support-item:last-child::before
            border-bottom: none
          .icon
            display: inline-block
            width: .32rem
            height: .32rem
            vertical-align: top
            margin-right: .12rem
            background-size: .32rem .32rem
            background-repeat: no-repeat
            &.decrease
              bg-image('decrease_4')
            &.discount
              bg-image('discount_4')
            &.guarantee
              bg-image('guarantee_4')
            &.invoice
              bg-image('invoice_4')
            &.special
              bg-image('special_4')
          .text
            line-height: .32rem
            font-size: .24rem
            color: rgb(7,17,27)
    .pics
      padding: .36rem
      .title
        margin-bottom: 0.16rem
        line-height: .28rem
        color: rgb(7,17,27)
        font-size: .28rem
      .pic-wrapper
        width: 100%
        overflow: hidden
        white-space: nowrap
        .pic-list
          font-size: 0
          .pic-item
            display: inline-block
            margin-right: .12rem
            width: 2.4rem
            height: 1.8rem
            img
              width: 100%
              height: 100%
    .infos
      padding: .36rem .36rem 0 .36rem
      .title
        padding-bottom: 0.24rem
        line-height: .28rem
        color: rgb(7,17,27)
        font-size: .28rem
      .content-wrapper
        .content
          font-size: .24rem
          line-height: .48rem
          color: rgb(240,20,20)
        .supports
          .support-item
            padding: 0.32rem .24rem
            font-size: 0
            &.support-item:last-child::before
              border-bottom: none
            .text
              line-height: .32rem
              font-size: .24rem
              color: rgb(7,17,27)
</style>

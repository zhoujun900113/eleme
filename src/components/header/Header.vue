<template>
    <div class="header">
      <div class="content-wrapper">
        <div class="avatar">
          <img :src="seller.avatar" />
        </div>
        <div class="content">
          <div class="title">
            <span class="brand"></span>
            <span class="name">{{seller.name}}</span>
          </div>
          <div class="description">
            {{seller.description}}/{{seller.deliveryTime}}分钟送达
          </div>
          <div v-if="seller.supports" class="support">
            <span class="icon" :class="classMap[seller.supports[0].type]"></span>
            <span class="text">{{seller.supports[0].description}}</span>
          </div>
        </div>
        <div class="support-count" v-if="seller.supports" @click="showDetail">
          <span class="count">{{seller.supports.length}}个</span>
          <i class="sell-icon icon-keyboard_arrow_right"></i>
        </div>
      </div>
      <div class="bulletin-wrapper" @click="showDetail">
        <span class="bulletin-title"></span>
        <span class="bulletin-text">{{seller.bulletin}}</span>
        <i class="sell-icon icon-keyboard_arrow_right"></i>
      </div>
      <div class="background">
        <img :src="seller.avatar"/>
      </div>
      <transition>
      <div v-show="detailShow" class="detail">
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1 class="name">{{seller.name}}</h1>
            <div class="star-wrapper">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="title">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul v-if="seller.supports" class="supports">
              <li class="support-item" v-for="(item,index) in seller.supports" :key="index">
                <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                <span class="text">{{seller.supports[index].description}}</span>
              </li>
            </ul>
            <div class="title">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <div class="bulletin">
              <p class="content">{{seller.bulletin}}</p>
            </div>
          </div>
        </div>
        <div class="detail-close" @click="hideDetail">
          <i class="icon-close"></i>
        </div>
      </div>
      </transition>
    </div>
</template>
<script>
import Star from '@/components/star/star'
export default {
  name: 'Header',
  props: {
    seller: Object
  },
  data () {
    return {
      detailShow: false
    }
  },
  methods: {
    showDetail () {
      this.detailShow = true
    },
    hideDetail () {
      this.detailShow = false
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  },
  components: {
    Star
  }
}
</script>
<style lang="stylus" scoped>
  @import "../../common/stylus/mixin"
  @import "../../common/stylus/base"

  .header
    width: 100%
    height: 2.68rem
    color: #ffffff
    background: rgba(7,17,27,0.5)
    overflow: hidden
    .content-wrapper
      position: relative
      padding: .48rem .24rem .36rem .48rem
      font-size: 0
      .avatar
        display: inline-block
        width: 1.28rem
        height: 1.28rem
        vertical-align: top
        img
          width: 100%
          height: 100%
          border-radius: .02rem
      .content
        display: inline-block
        margin-left: .32rem
        font-size: .28rem
        .title
          margin .04rem 0 .16rem 0
          .brand
            display: inline-block
            width: .6rem
            height: .36rem
            background-size: .6rem .36rem
            bg-image('brand')
            background-repeat: no-repeat
            vertical-align: middle;
          .name
            margin-left: .12rem
            font-size: .32rem
            line-height: .36rem
            font-weight: bold
            vertical-align: middle;
        .description
          margin-bottom: .2rem
          line-height: .24rem
          font-size: .24rem
        .support
          .icon
            display: inline-block
            width: .24rem
            height: .24rem
            margin-right: .08rem
            margin-bottom: .04rem
            background-size: .24rem .24rem
            background-repeat: no-repeat
            vertical-align: middle;
            &.decrease
              bg-image('decrease_1')
            &.discount
              bg-image('discount_1')
            &.guarantee
              bg-image('guarantee_1')
            &.invoice
              bg-image('invoice_1')
            &.special
              bg-image('special_1')
          .text
            font-size: .2rem
            line-height: .24rem
            vertical-align: middle;
      .support-count
        position: absolute
        right: .12rem
        bottom: 0.26rem
        width: 0.8rem
        height: .48rem
        line-height: .48rem
        border-radius: .48rem
        vertical-align: middle
        background-color: rgba(0,0,0,0.2)
        text-align: center
        .count
          font-size: .1rem
        .icon-keyboard_arrow_right
          margin-left: .02rem
          line-height: .48rem
          font-size: .1rem
    .bulletin-wrapper
      position: relative
      height: .56rem
      line-height: .56rem
      padding: 0 .44rem 0 .12rem
      white-space: nowrap
      overflow: hidden
      text-overflow: ellipsis
      background: rgba(7,17,27,0.2)
      .bulletin-title
        display: inline-block
        width: .44rem
        height: .24rem
        background-size: .44rem .24rem
        bg-image('bulletin')
        background-repeat: no-repeat
      .bulletin-text
        font-size: .1rem
        margin: 0 .04rem
        vertical-align: top
      .icon-keyboard_arrow_right
        position: absolute
        font-size: .1rem
        right: .12rem
        top: .16rem
    .background
      position: absolute
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: -1
      filter: blur(.1rem)
      img
        width: 100%
        height: 100%
    .v-enter, .v-leave-to
        opacity: 0
        background: rgba(7,17,27,0)
    .v-enter-active, .v-leave-active
        transition:all 2s ease
    .detail
      position: fixed
      z-index: 100
      top: 0
      left: 0
      width: 100%
      height: 100%
      overflow: auto
      background: rgba(7,17,27,0.8)
      .detail-wrapper
        min-height: 100%
        width: 100%
        .detail-main
          padding-top: 1.28rem
          padding-bottom: 1.28rem
          .name
            line-height: .32rem
            text-align: center
            font-size: .32rem
            font-weight: 700
          .star-wrapper
            margin-top: .32rem
            padding: 0.2rem 0
            text-align: center
          .title
            display: flex
            width: 80%
            margin: .28rem auto .24rem auto
            .line
              flex: 1
              position: relative
              top: -.12rem
              border-bottom: 0.01rem solid rgba(255,255,255,0.2)
            .text
              padding: 0 .24rem
              font-size: 0.14rem
              font-weight: 700
          .supports
            width: 80%
            margin: 0 auto
            .support-item
              padding: 0 .24rem
              margin-bottom: .24rem
              font-size: 0
              &.last-child
                margin-bottom: 0
              .icon
                display: inline-block
                width: .32rem
                height: .32rem
                vertical-align: top
                margin-right: .12rem
                background-size: .32rem .32rem
                background-repeat: no-repeat
                &.decrease
                  bg-image('decrease_2')
                &.discount
                  bg-image('discount_2')
                &.guarantee
                  bg-image('guarantee_2')
                &.invoice
                  bg-image('invoice_2')
                &.special
                  bg-image('special_2')
              .text
                line-height: .32rem
                font-size: .24rem
          .bulletin
            width: 80%
            margin: 0 auto
            .content
              font-size: .24rem
              padding: 0 .24rem
              line-height: .48rem
      .detail-close
        position: relative
        width: .64rem
        height: .64rem
        margin: -1.28rem auto 0 auto
        clear: both
        font-size: .64rem
</style>

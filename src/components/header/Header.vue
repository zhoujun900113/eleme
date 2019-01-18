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
        <div class="support-count" v-if="seller.supports">
          <span class="count">{{seller.supports.length}}个</span>
          <i class="sell-icon icon-keyboard_arrow_right"></i>
        </div>
      </div>
      <div class="bulletin-wrapper"></div>
    </div>
</template>
<script>
export default {
  name: 'Header',
  props: {
    seller: Object
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
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
    background: #999
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
</style>

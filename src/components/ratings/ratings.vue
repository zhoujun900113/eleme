<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="overview">
        <div class="overflow-left border-right">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overflow-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <rating-select
      @typeSelect="typeSelect"
      @contentOnly="contentOnly"
      :selectType="selectType"
      :onlyContent="onlyContent"
      :desc="desc"
      :ratings="ratings">
      </rating-select>
      <div class="rating-wrapper">
        <ul>
          <li v-for="(rating,index) in ratings"
          v-show="needShow(rating.rateType,rating.text)"
          :key="index"
          class="rating-item border-bottom">
            <div class="avatar">
              <img :src="rating.avatar">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                <span class="icon-thumb_up"></span>
                <span class="item" v-for="(item,index) in rating.recommend" :key="index">{{item}}</span>
              </div>
              <div class="time">{{rating.rateTime | formatDate}}</div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
import Star from '@/components/star/star'
import BScroll from 'better-scroll'
import {formatDate} from '@/common/js/date'
import Split from '@/components/split/split'
import RatingSelect from '@/components/ratingselect/ratingselect'

const ALL = 2

export default {
  name: 'Ratings',
  props: {
    seller: {
      type: Object
    },
    ratings: {
      type: Array
    }
  },
  data () {
    return {
      showFlag: false,
      selectType: ALL,
      onlyContent: true,
      desc: {
        all: '全部',
        positive: '满意',
        negative: '不满意'
      }
    }
  },
  filters: {
    formatDate (time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm')
      // return date.toLocaleDateString().replace(/\//g, '-') + ' ' + date.toTimeString().substr(0, 8)
    }
  },
  components: {
    Star,
    Split,
    RatingSelect
  },
  mounted () {
    this.$nextTick(() => {
      this.scroll = new BScroll(this.$refs.ratings, {
        click: true
      })
    })
  },
  methods: {
    needShow (type, text) {
      if (this.onlyContent && !text) {
        return false
      }
      if (this.selectType === ALL) {
        return true
      } else {
        return type === this.selectType
      }
    },
    typeSelect (type) {
      this.selectType = type
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    contentOnly (contentOnly) {
      this.onlyContent = contentOnly
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    }
  }
}
</script>
<style lang="stylus" scoped>
  .ratings
    position: absolute
    top: 3.48rem
    left: 0
    bottom: 0
    width: 100%
    overflow: hidden
    .overview
      display: flex
      padding: .36rem 0
      .overflow-left
        flex: 0 0 2.74rem
        width: 2.74rem
        text-align: center
        padding: .12rem 0
        @media screen and (max-width: 320px)
          flex: 0 0 2.4rem
          width: 2.4rem
        .score
          margin-bottom: .12rem
          line-height: .56rem
          font-size: .48rem
          color: rgb(255,153,0)
        .title
          margin-bottom: .16rem
          line-height: .24rem
          font-size: .24rem
          color: rgb(7,17,27)
        .rank
          line-height: .2rem
          font-size: .2rem
          color: rgb(147,153,159)
      .overflow-right
        flex: 1
        padding: .12rem 0 .12rem .48rem
        @media screen and (max-width: 320px)
          padding-left: .12rem
        .score-wrapper
          margin-bottom: .16rem
          font-size: 0
          .title
            display: inline-block
            vertical-align: top
            font-size: .24rem
            line-height: .36rem
            color: rgb(7,17,27)
          .star
            display: inline-block
            vertical-align: top
            margin: 0 0.12rem
          .score
            display: inline-block
            vertical-align: top
            font-size: .24rem
            line-height: .36rem
            color: rgb(255,153,0)
      .delivery-wrapper
        font-size: 0
        .title
          font-size: .24rem
          line-height: .36rem
          color: rgb(7,17,27)
        .delivery
          font-size: .24rem
          line-height: .36rem
          color: rgb(147,153,159)
          margin: 0 0.24rem
    .rating-wrapper
      padding: 0 .36rem
      .rating-item
        display: flex
        padding: .36rem 0
        .avatar
          flex: 0 0 .56rem
          width: .56rem
          margin-right: .24rem
          img
            width: .56rem
            border-radius: 50%
        .content
          position: relative
          flex: 1
          .name
            margin-bottom: .08rem
            line-height: .24rem
            font-size: .2rem
            color: rgb(7,17,27)
          .star-wrapper
            margin-bottom: 0.12rem
            font-size: 0
            .star
              display: inline-block
              margin-right: .12rem
              vertical-align: top
            .delivery
              display: inline-block
              vertical-align: top
              font-size: .2rem
              color: rgb(147,153,159)
          .text
            margin-bottom: .16rem
            font-size: .24rem
            color: rgb(7,17,27)
            line-height: .36rem
          .recommend
            line-height: .32rem
            font-size: 0
            .icon-thumb_up, .item
              display: inline-block
              margin: 0 0.16rem 0.08rem 0
              font-size: 0.18rem
            .icon-thumb_up
              color: rgb(0,160,220)
            .item
              padding: 0 0.12rem
              border: 0.02rem solid rgba(7,17,27,0.1)
              border-radius: 0.02rem
              color: rgb(147,153,159)
              background: #ffffff
          .time
            position: absolute
            font-size: .2rem
            top: 0
            right: 0
            line-height: .24rem
            color: rgb(147,153,159)
</style>

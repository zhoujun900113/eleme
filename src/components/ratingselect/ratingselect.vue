<template>
  <div class="ratingselect">
    <div class="rating-type border-bottom">
      <span
      @click="select(2,$event)"
      class="block positive"
      :class="{'active':selectType===2}">
        {{desc.all}}
        <span class="count">{{ratings.length}}</span>
      </span>
      <span
      @click="select(0,$event)"
      class="block positive"
      :class="{'active':selectType===0}">
        {{desc.positive}}
        <span class="count">{{positives.length}}</span>
      </span>
      <span
      @click="select(1,$event)"
      class="block negative"
      :class="{'active':selectType===1}">
        {{desc.negative}}
        <span class="count">{{negatives.length}}</span>
      </span>
    </div>
    <div
    @click="toggleContent"
    class="switch border-bottom"
    :class="{'on':onlyContent===true}">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>
<script>
const POSITIVE = 0
const NEGATIVE = 1
const ALL = 2
export default {
  name: 'RatingSelect',
  methods: {
    select (type, event) {
      this.typeSelect = type
      this.$emit('typeSelect', this.typeSelect)
    },
    toggleContent () {
      this.contentOnly = !this.contentOnly
      this.$emit('contentOnly', this.contentOnly)
    }
  },
  data () {
    return {
      typeSelect: this.selectType,
      contentOnly: this.onlyContent
    }
  },
  computed: {
    positives () {
      return this.ratings.filter((rating) => {
        return rating.rateType === POSITIVE
      })
    },
    negatives () {
      return this.ratings.filter((rating) => {
        return rating.rateType === NEGATIVE
      })
    }
  },
  props: {
    ratings: {
      type: Array,
      default () {
        return []
      }
    },
    selectType: {
      type: Number,
      default: ALL
    },
    onlyContent: {
      type: Boolean,
      default: false
    },
    desc: {
      type: Object,
      default () {
        return {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      }
    }
  }
}
</script>
<style lang="stylus" scoped>
  .ratingselect
    .rating-type
      padding: .36rem 0
      margin: 0 .36rem
      .block
        display: inline-block
        padding: .16rem .24rem
        line-height: .32rem
        border-radius: .02rem
        margin-right: .16rem
        color: rgb(77,85,93)
        &.active
          color: #ffffff
        .count
          margin-left: 0.04rem
          font-size: .16rem
        &.positive
          background: rgba(0,160,220,0.2)
          &.active
            background: rgb(0,160,220)
        &.negative
          background: rgba(77,85,93,0.2)
          &.active
            background: rgb(77,85,93)
    .switch
      padding: .24rem .36rem
      line-height: .48rem
      color: rgb(147,153,159)
      font-size: 0
      &.on
        .icon-check_circle
          color: #00c850
      .icon-check_circle
        display: inline-block
        vertical-align: top
        font-size: .48rem
        margin-right: .08rem
      .text
        display: inline-block
        vertical-align: top
        font-size: .24rem
</style>

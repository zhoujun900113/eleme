<template>
  <div id="app">
    <Header class="border-bottom" :seller="seller"></Header>
    <div class="tab border-bottom">
      <router-link to="/goods" class="tab-item">
        <div class="tab-item-title">商品</div>
      </router-link>
      <router-link to="/ratings" class="tab-item">
        <div class="tab-item-title">评价</div>
      </router-link>
      <router-link to="/seller" class="tab-item">
        <div class="tab-item-title">商家</div>
      </router-link>
    </div>
    <keep-alive>
      <router-view :seller="seller" :ratings="ratings"></router-view>
    </keep-alive>
  </div>
</template>

<script>
import axios from 'axios'
import {urlParse} from 'common/js/util'
import Header from '@/components/header/Header'
export default {
  name: 'App',
  data: function () {
    return {
      seller: {
        id: (() => {
          let queryParam = urlParse()
          return queryParam.id
        })()
      },
      ratings: []
    }
  },
  components: {
    Header
  },
  methods: {
    getSellInfo () {
      axios.get('/api/data.json')
        .then(this.handleGetSellInfoSucc)
        .catch(() => console.log('get data.json error'))
    },
    handleGetSellInfoSucc (res) {
      const data = res.data
      this.seller = Object.assign({}, data.seller, {'id': this.seller.id})
      this.ratings = data.ratings
    }
  },
  mounted () {
    this.getSellInfo()
  }
}
</script>

<style lang="stylus" scoped>
  #app
    .tab
      display: flex
      width: 100%
      height: .8rem
      line-height: .8rem
      a
        color: rgb(77,85,93)
      .router-link-active
        color: rgb(240,20,20)
      .tab-item
        flex: 1
        .tab-item-title
          text-align: center
          font-size: .28rem
</style>

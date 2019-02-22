<template>
  <div class="cartcontrol">
      <transition>
        <div
        class="cart-decrease icon-remove_circle_outline"
        v-show="food.count"
        @click.stop.prevent="removeCart"></div>
      </transition>
      <div class="cart-count" v-show="food.count">{{food.count}}</div>
      <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
  </div>
</template>
<script>
import Vue from 'vue'
export default {
  name: 'CartControl',
  props: {
    food: {
      type: Object
    }
  },
  methods: {
    addCart () {
      if (!this.food.count) {
        Vue.set(this.food, 'count', 1)
      } else {
        this.food.count++
      }
      this.$emit('cartAdd', event.target)
    },
    removeCart () {
      this.food.count--
    }
  }
}
</script>
<style lang="stylus" scoped>
  .cartcontrol
    font-size: 0
    .v-enter, .v-leave-to
      opacity: 0
      transform: translate3d(.48rem, 0, 0) rotate(180deg)
    .v-enter-active, .v-leave-active
      transition:all 0.4s linear
    .cart-decrease, .cart-add
      display: inline-block
      padding: .12rem
      font-size: .48rem
      line-height: .48rem
      color: rgb(0,160,220)
    .cart-count
      display: inline-block
      font-size: .2rem
      vertical-align: top
      width: .24rem
      padding-top: .12rem
      line-height: .48rem
      text-align: center
      color: rbg(147,153,159)
    .cart-add
      display: inline-block
</style>

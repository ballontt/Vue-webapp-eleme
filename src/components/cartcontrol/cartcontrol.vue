<template>
  <div class="cartcontrol">
    <!--减号按钮-->
    <div class="cart-decrease"
         v-show="food.count>0"
         @click.stop.prevent="decreaseCart"
         transition="move">
      <span class="inner icon-remove_circle_outline"></span>
    </div>
    <!--数量-->
    <div class="cart-count "
         v-show="food.count>0">{{food.count}}</div>
    <!--加号按钮-->
    <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue';

  export default {
    props: {
      // 接收父组件传来的food参数
      food: {
        type: Object
      }
    },
    methods: {
      addCart(event) {
        if (!event._constructed) {
          return;
        }
        if (this.food.count === undefined) {
          Vue.set(this.food, 'count', 1);
        } else {
          this.food.count ++;
        }
        // 向父组件派发事件
        this.$dispatch('cart.add', event.target);
      },
      decreaseCart(event) {
        if (!event._constructed) {
          return;
        }
        if (this.food.count) {
          this.food.count --;
        }
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size 0
    .cart-decrease
      display inline-block
      padding 6px
      transition all 0.3s linear
      &.move-trasition
        opacity 1
        transform translate3D(0,0,0)
      &.move-enter, &.move-leave
        opacity 0
        transform translate3D(24px,0,0)
        .inner
          transform rotate(180deg)
      .inner
        display inline-block
        line-height 24px
        font-size 24px
        color rgb(0,160,220)
        transition all 0.3s linear
        transform rotate(0)
    .cart-count
      display inline-block
      vertical-align top
      width 12px
      padding-top 6px
      line-height 24px
      text-align center
      font-size 10px
      color rgb(147,153,159)
    .cart-add
      display inline-block
      padding 6px
      line-height 24px
      font-size 24px
      color rgb(0,160,220)

</style>

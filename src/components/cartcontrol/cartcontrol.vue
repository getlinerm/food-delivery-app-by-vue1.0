<template>
  <!--加减号按钮-->
  <div class="cartcontrol">
    <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="decreaseCart" transition="move">
      <span class="inner icon-remove_circle_outline"></span>
    </div>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="addCart($event)"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue';

  export default {
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart(event) {
        if (!event._constructed) {
          return;
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1);
        } else {
          this.food.count++;
        }
        this.$dispatch('cart.add', event.target);
      },
      decreaseCart(event) {
        if (!event._constructed) {
          return;
        }
        if (this.food.count) {
          this.food.count--;
        }
      }
    }
  };
</script>

<style lang="scss" rel="stylesheets/scss">
  .cartcontrol {
    font-size: 0.0rem;
    .cart-decrease {
      display: inline-block;
      padding: 0.16rem;
      transition: all 0.4s linear;
      &.move-transition{
        opicity: 1;
        transform: translate3d(0,0,0);
        .inner{
          display: inline-block;
          line-height: 0.64rem;
          font-size: 0.64rem;
          color: rgb(0, 160, 220);
          transition: all 0.4s linear;
          transform: rotate(0);
        }
      }
      &.move-enter, &.move-leave{
        opicity: 0;
        transform: translate3d(0.64rem,0,0);
        .inner{
          transform: rotate(180deg);
        }
      }
    }
    .cart-count {
      display: inline-block;
      width: 0.32rem;
      line-height: 0.64rem;
      vertical-align: top;
      padding-top: 0.16rem;
      text-align: center;
      font-size: 0.266667rem;
      color: rgb(147, 153, 159);
    }
    .cart-add {
      display: inline-block;
      padding: 0.16rem;
      line-height: 0.64rem;
      font-size: 0.64rem;
      color: rgb(0, 160, 220);
    }
  }
</style>

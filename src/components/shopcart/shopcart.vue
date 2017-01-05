<template>
  <!--购物车组件-->
  <div class="shopcart">
    <div class="content" @click="toggleList">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'highlight':totalCount>0}">
            <span class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></span>
          </div>
          <div class="num" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'highlight':totalPrice>0}">￥{{totalPrice}}元</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right" @click.stop.prevent="pay">
        <div class="pay" :class="payClass">
          {{payDesc}}
        </div>
      </div>
    </div>
    <div class="ball-container">
      <div transition="drop" v-for="ball in balls" v-show="ball.show" class="ball">
        <div class="inner inner-hook"></div>
      </div>
    </div>
    <div class="shopcart-list" v-show="listShow" transition="fold">
      <div class="list-header">
        <h1 class="title">购物车</h1>
        <span class="empty" @click="empty">清空</span>
      </div>
      <div class="list-content" v-el:list-content>
        <ul>
          <li class="food" v-for="food in selectFoods">
            <span class="name">{{food.name}}</span>
            <div class="price">
              <span>￥{{food.price*food.count}}</span>
            </div>
            <div class="cartcontrol-wrapper">
              <cartcontrol :food="food"></cartcontrol>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
  <div class="list-mask" @click="hideList" v-show="listShow" transition="fade"></div>
</template>

<script type="text/ecmascript-6">
  import cartcontrol from '../cartcontrol/cartcontrol.vue';
  import BScroll from 'better-scroll';

  export default{
    props: {
      selectFoods: {
        type: Array
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
    data() {
      return {
        balls: [
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          }
        ],
        dropBalls: [],
        fold: true
      };
    },
    computed: {
      totalPrice() {
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        });
        return total;
      },
      totalCount() {
        let count = 0;
        this.selectFoods.forEach((food) => {
          count += food.count;
        });
        return count;
      },
      payDesc() {
        if (this.totalPrice === 0) {
          return `￥${this.minPrice}元起送`;
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice;
          return `还差￥${diff}元起送`;
        } else {
          return '去结算';
        }
      },
      payClass() {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough';
        } else {
          return 'enough';
        }
      },
      listShow() {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        }
        let show = !this.fold;
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$els.listContent, {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        }
        return show;
      }
    },
    methods: {
      drop(el) {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if (!ball.show) {
            ball.show = true;
            ball.el = el;
            this.dropBalls.push(ball);
            return;
          }
        }
      },
      toggleList() {
        if (!this.totalCount) {
          return;
        }
        this.fold = !this.fold;
      },
      empty() {
        this.selectFoods.forEach((food) => {
          food.count = 0;
        });
      },
      hideList() {
        this.fold = true;
      },
      pay() {
        if (this.totalPrice < this.minPrice) {
          return;
        }
        window.alert(`去结算${this.totalPrice}`);
      }

    },
    transitions: {
      drop: {
        beforeEnter(el) {
          let count = this.balls.length;
          while (count--) {
            let ball = this.balls[count];
            if (ball.show) {
            	let px2rem = window.innerWidth / 10;
              let rect = ball.el.getBoundingClientRect();
              let x = rect.left / px2rem - 0.48;
              let y = -(window.innerHeight / px2rem - rect.top / px2rem - 0.853333 - 0.213333);
              console.log(rect.left, rect.top);
              el.style.display = '';
              el.style.webkitTransform = `translate3d(0,${y}rem,0)`;
              el.style.transform = `translate3d(0,${y}rem,0)`;
              let inner = el.getElementsByClassName('inner-hook')[0];
              inner.style.webkitTransform = `translate3d(${x}rem,0,0)`;
              inner.style.transform = `translate3d(${x}rem,0,0)`;
            }
          }
        },
        enter(el) {
          /* eslint-disable no-unused-vars */
          let rf = el.offsetHeight;
          this.$nextTick(() => {
            el.style.webkitTransform = 'translate3d(0,0,0)';
            el.style.transform = 'translate3d(0,0,0)';
            let inner = el.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform = 'translate3d(0,0,0)';
            inner.style.transform = 'translate3d(0,0,0)';
          });
        },
        afterEnter(el) {
          let ball = this.dropBalls.shift();
          if (ball) {
            ball.show = false;
            el.style.display = 'none';
          }
        }
      }
    },
    components: {
      cartcontrol
    }
  };
</script>

<style lang="scss" rel="stylesheets/scss">
  .shopcart {
    position: fixed;
    left: 0.0rem;
    bottom: 0.0rem;
    z-index: 50;
    width: 100%;
    height: 1.28rem;
    .content {
      display: flex;
      background: #141d27;
      color: rgba(255, 255, 255, 0.4);
      .content-left {
        flex: 1;
        font-size: 0.0rem;
        .logo-wrapper {
          display: inline-block;
          margin: -0.266667rem 0.32rem 0.0rem 0.32rem;
          padding: 0.16rem;
          width: 1.493333rem;
          height: 1.493333rem;
          vertical-align: top;
          border-radius: 50%;
          background: #141d27;
          position: relative;
          .logo {
            width: 100%;
            height: 100%;
            border-radius: 50%;
            background: #2b343c;
            text-align: center;
            &.highlight {
              background: rgb(0, 160, 220);
            }
            .icon-shopping_cart {
              line-height: 1.173333rem;
              font-size: 0.64rem;
              color: #80858a;
              &.highlight {
                color: #fff;
              }
            }
          }
          .num {
            position: absolute;
            right: 0px;
            top: 0px;
            width: 0.64rem;
            height: 0.426667rem;
            border-radius: 0.213333rem;
            line-height: 0.426667rem;
            text-align: center;
            font-size: 0.24rem;
            font-weight: 700;
            color: #fff;
            background: rgb(240, 20, 20);
            box-shadow: 0.0rem 0.053333rem 0.106667rem 0.0rem rgba(0, 0, 0, 0.4);
          }
        }
        .price {
          display: inline-block;
          vertical-align: top;
          line-height: 0.64rem;
          margin-top: 0.32rem;
          padding-right: 0.32rem;
          border-right: 0.013333rem solid rgba(255, 255, 255, 0.1);
          font-size: 0.426667rem;
          font-weight: 700;
          &.highlight {
            color: #fff;
          }
        }
        .desc {
          display: inline-block;
          vertical-align: top;
          line-height: 0.64rem;
          margin: 0.32rem 0.0rem;
          font-size: 0.266667rem;
          padding-left: 0.32rem;
        }
      }
      .content-right {
        flex: 0 0 2.8rem;
        width: 2.8rem;
        .pay {
          height: 1.28rem;
          line-height: 1.28rem;
          text-align: center;
          font-size: 0.32rem;
          font-weight: 700;
          background: #2b333b;
          &.not-enough {
            background: #2b333b;
          }
          &.enough {
            background: #00b43c;
            color: #fff;
          }
        }
      }
    }
    .ball-container {
      .ball {
        position: fixed;
        left: 0.853333rem;
        bottom: 0.586667rem;
        z-index: 200;
        &.drop-transition {
          transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41);
          .inner {
            width: 0.426667rem;
            height: 0.426667rem;
            border-radius: 50%;
            background: rgb(0, 160, 220);
            transition: all 0.4s linear;
          }
        }
      }
    }
    .shopcart-list {
      position: absolute;
      top: 0;
      left: 0;
      z-index: -1;
      width: 100%;
      &.fold-transition {
        transition: all 0.5s;
        transform: translate3d(0, -100%, 0);
      }
      &.fold-enter, &.fold-leave {
        transform: translate3d(0, 0, 0);
      }
      .list-header {
        height: 1.066667rem;
        line-height: 1.066667rem;
        padding: 0 0.48rem;
        background: #f3f5f7;
        border-bottom: 0.026667rem solid rgba(7, 7, 27, 0.1);
        .title {
          float: left;
          font-size: 0.373333rem;
          color: rgb(7, 17, 27);
        }
        .empty {
          float: right;
          font-size: 0.32rem;
          color: rgb(0, 160, 220);
        }
      }
      .list-content {
        padding: 0.0rem 0.48rem;
        max-height: 5.786667rem;
        background: #fff;
        overflow: hidden;
        .food {
          position: relative;
          padding: 0.32rem 0.0rem;
          border-bottom: 1px solid rgba(7, 17, 27, 0.1);
          .name {
            line-height: 0.64rem;
            color: rgb(7, 17, 27);
            font-size: 0.373333rem;
          }
          .price {
            position: absolute;
            right: 2.4rem;
            bottom: 0.32rem;
            line-height: 0.64rem;
            font-size: 0.373333rem;
            font-weight: 700;
            color: rgb(240, 20, 20);
          }
          .cartcontrol-wrapper {
            position: absolute;
            right: 0;
            bottom: 0.16rem;
          }
        }
      }
    }
  }

  .list-mask {
    position: fixed;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    z-index: 40;
    backdrop-filter: blur(0.266667rem);
    &.fade-transition {
      transition: all 0.5s;
      opcity: 1;
      background: rgba(7, 17, 27, 0.6);
    }
    &.fade-enter, &.fade-leave {
      opcity: 0;
      background: rgba(7, 17, 27, 0.1);
    }
  }
</style>






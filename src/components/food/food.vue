<template>
  <div v-show="showFlag" class="food" transition="move" v-el:food>
    <div class="food-content">
      <div class="image-header">
        <img :src="food.image">
        <div class="back" @click="hide">
          <i class="icon-arrow_lift"></i>
        </div>
      </div>

      <div class="content">
        <h1 class="title">{{food.name}}</h1>
        <div class="detail">
          <span class="sell-count">月售{{food.sellCount}}份</span>
          <span class="rating">好评率{{food.rating}}%</span>
        </div>
        <div class="price">
          <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
        </div>
        <div class="cartcontrol-wrapper">
          <cartcontrol :food="food"></cartcontrol>
        </div>
        <div @click.stop.prevent="addFirst" class="buy" v-show="!food.count || food.count===0" transition="fade">加入购物车
        </div>
      </div>

      <split v-show="food.info"></split>

      <div class="info" v-show="food.info">
        <h1 class="title">商品信息</h1>
        <p class="text">{{food.info}}</p>
      </div>
      <split></split>
      <div class="rating">
        <h1 class="title">商品评价</h1>
        <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc"
                      :ratings="food.ratings"></ratingselect>
        <div class="rating-wrapper">
          <ul v-show="food.ratings && food.ratings.length">
            <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings" class="rating-item">
              <div class="user">
                <span class="name">{{rating.username}}</span>
                <img class="avatar" :src="rating.avatar">
              </div>
              <div class="time">{{rating.rateTime | formatDate}}</div>
              <p class="text">
                <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>{{rating.text}}
              </p>
            </li>
          </ul>
          <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontrol from '../cartcontrol/cartcontrol.vue';
  import Vue from 'vue';
  import split from '../split/split.vue';
  import ratingselect from '../ratingselect/ratingselect.vue';
  import {formatDate} from '../../common/js/date';

  const ALL = 2;

  export default {
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: false,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      };
    },
    methods: {
      show() {
        this.showFlag = true;
        this.selectType = ALL;
        this.onlyContent = true;
        // 保证DOM已被渲染，从而使得高度计算正确
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$els.food, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
      },
      hide() {
        this.showFlag = false;
      },
      addFirst(event) {
        console.log('click');
        if (!event._constructed) {
          return;
        }
        this.$dispatch('cart.add', event.target);
        Vue.set(this.food, 'count', 1);
      },
      needShow(type, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return type === this.selectType;
        }
      }
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    },
    events: {
      'ratingtype.select'(type) {
        this.selectType = type;
        // eventloop
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      'content.toggle'(onlyContent) {
        this.onlyContent = onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      }
    }
  };
</script>

<style lang="scss" rel="stylesheets/scss">
  .food {
    position: fixed;
    left: 0;
    top: 0;
    bottom: 1.293333rem;
    z-index: 30;
    width: 100%;
    background: #fff;
    &.move-transition {
      transition: all 0.2s;
      transform: translate3d(0, 0, 0);
    }
    &.move-enter, &.move-leave {
      transform: translate3d(100%, 0, 0);
    }
    .image-header {
      position: relative;
      width: 100%;
      height: 0;
      padding-top: 100%;
      img {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
      }
      .back {
        position: absolute;
        top: 0.266667rem;
        left: 0;
        .icon-arrow_lift {
          display: block;
          padding: 0.266667rem;
          font-size: 0.533333rem;
          color: #fff;
        }
      }
    }
    .content {
      position: relative;
      padding: 0.48rem;
      .title {
        font-size: 0.373333rem;
        font-weight: 700;
        color: rgb(7, 17, 27);
        line-height: 0.373333rem;
        margin-bottom: 0.213333rem;
      }
      .detail {
        margin-bottom: 0.48rem;
        line-height: 0.266667rem;
        height: 0.266667rem;
        font-size: 0;
        .sell-count, .rating {
          font-size: 0.266667rem;
          color: rgb(147, 153, 159);
        }
        .sell-count {
          margin-right: 0.32rem;
        }
      }
      .price {
        font-weight: 700;
        line-height: 0.32rem;
        .now {
          margin-right: 0.213333rem;
          font-size: 0.373333rem;
          color: rgb(240, 20, 20);
        }
        .old {
          text-decoration: line-through;
          font-size: 0.266667rem;
          color: rgb(147, 153, 159);
        }
      }
      .cartcontrol-wrapper {
        position: absolute;
        right: 0.32rem;
        bottom: 0.32rem;
      }
      .buy {
        position: absolute;
        right: 0.48rem;
        bottom: 0.48rem;
        z-index: 20;
        line-height: 0.64rem;
        height: 0.64rem;
        padding: 0.0rem 0.32rem;
        font-size: 0.266667rem;
        color: #fff;
        background: rgb(0, 160, 220);
        border-radius: 0.32rem;
        &.fade-transition {
          tranon: all 0.2s;
          opcity: 1;
        }
        &.fade-entrt, &.fade-leave {
          opcity: 0;
        }
      }
    }
    .info {
      padding: 0.48rem;
      .title {
        line-height: 0.373333rem;
        margin-bottom: 0.16rem;
        font-size: 0.373333rem;
        color: rgb(7, 17, 27);
      }
      .text {
        line-height: 0.64rem;
        padding: 0.0rem 0.213333rem;
        font-size: 0.32rem;
        color: rgb(77, 85, 93);
      }
    }
    .rating {
      padding-top: 0.48rem;
      .title {
        line-height: 0.373333rem;
        margin-left: 0.48rem;
        font-size: 0.373333rem;
        color: rgb(7, 17, 27);
      }
      .rating-wrapper {
        padding: 0.0rem 0.48rem;
        .rating-item {
          position: relative;
          padding: 0.426667rem 0.0rem;
          border-bottom: 0.013333rem solid rgba(7, 17, 27, 0.1);
          .user {
            position: absolute;
            right: 0;
            top: 0.426667rem;
            font-size: 0;
            line-height: 0.32rem;
            .name {
              font-size: 0.266667rem;
              margin-right: 0.16rem;
              display: inline-block;
              vertical-align: top;
              color: rgb(147, 153, 159);
            }
            .avatar {
              border-radius: 50%;
              width: 0.32rem;
              height: 0.32rem;
            }
          }
          .time {
            margin-bottom: 0.16rem;
            line-height: 0.32rem;
            font-size: 0.266667rem;
            color: rgb(147, 153, 159);
          }
          .text {
            line-height: 0.426667rem;
            font-size: 0.32rem;
            color: rgb(7, 17, 27);
            .icon-thumb_up, .icon-thumb_down {
              line-height: 0.426667rem;
              margin-right: 0.106667rem;
              font-size: 0.32rem;
            }
            .icon-thumb_up {
              color: rgb(0, 160, 220);
            }
            .icon-thumb_down {
              color: rgb(147, 153, 159);
            }
          }
        }
        .no-rating {
          padding: 0.426667rem 0.0rem;
          font-size: 0.32rem;
          color: rgb(1, 1, 1);
        }
      }
    }

  }
</style>

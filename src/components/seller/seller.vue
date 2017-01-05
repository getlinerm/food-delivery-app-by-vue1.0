<template>
  <div class="seller" v-el:seller>
    <div class="seller-content">
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc">
          <star :size="0.373333" :space="0.16" :score="seller.score"></star>
          <span class="text">({{seller.ratingCount}})</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block">
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleFavorite">
          <span class="icon-favorite" :class="{'active':favorite}"></span>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content-wrapper">
          <p class="content">{{seller.bulletin}}</p>
        </div>
        <ul v-if="seller.supports" class='supports'>
          <li class="support-item" v-for="item in seller.supports">
            <span class="icon" :class="classMap[seller.supports[$index].type]"></span>
            <span class="text">{{seller.supports[$index].description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" v-el:pic-wrapper>
          <ul class="pic-list" v-el:pic-list>
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="info">
        <h1 class="title">商家信息</h1>
        <ul>
          <li class="info-item" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../star/star.vue';
  import split from '../split/split.vue';
  import BScroll from 'better-scroll';
  import {saveToLocal, loadFromLocal} from '../../common/js/store';

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        favorite: (() => {
          return loadFromLocal(this.seller.id, 'favorite', false);
        })()
      };
    },
    computed: {
      favoriteText() {
        return this.favorite ? '已收藏' : '收藏';
      }
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    watch: {
      'seller'() {
        this._initScroll();
        this._initPics();
      }
    },
    // BScroll依赖DOM渲染完毕，created不可以保证
    ready() {
      this._initScroll();
      this._initPics();
    },
    methods: {
      toggleFavorite(event) {
        if (!event._constructed) {
          return;
        }
        this.favorite = !this.favorite;
        saveToLocal(this.seller.id, 'favorite', this.favorite);
      },
      _initScroll() {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$els.seller, {
            click: true
          });
        } else {
          this.scroll.refresh();
        }
      },
      _initPics() {
        console.log(1);
        if (this.seller.pics) {
          let picWidth = 180;
          let margin = 12;
          let width = (picWidth + margin) * this.seller.pics.length - margin;
          this.$els.picList.style.width = width + 'px';
          this.$nextTick(() => {
            if (!this.picScroll) {
              this.picScroll = new BScroll(this.$els.picWrapper, {
                scrollX: true,
                eventPassthrough: 'vertical'
              });
            } else {
              this.picScroll.refresh();
            }
          });
        }
      }
    },
    components: {
      star,
      split
    }
  };
</script>

<style lang="scss" rel="stylesheets/scss">
  .seller {
    position: absolute;
    top: 4.64rem;
    bottom: 0;
    left: 0;
    width: 100%;
    overflow: hidden;
    .overview {
      padding: 0.48rem;
      position: relative;
      .title {
        margin-bottom: 16px;
        line-height: 0.373333rem;
        color: rgb(7, 17, 27);
        font-size: 0.373333rem;
      }
      .desc {
        padding-bottom: 0.48rem;

        font-size: 0.0rem;
        border-bottom: 0.013333rem solid rgba(7, 17, 27, 0.1);
        font-size: 0;
        .star {
          display: inline-block;
          vertical-align: top;
          margin-right: 0.213333rem;
        }
        .text {
          margin-right: 0.32rem;
          line-height: 0.48rem;
          display: inline-block;
          vertical-align: top;
          font-size: 0.266667rem;
          color: rgb(77, 85, 93);
        }
      }
      .remark {
        display: flex;
        padding-top: 0.48rem;
        .block {
          flex: 1;
          text-align: center;
          border-right: 0.013333rem solid rgba(7, 17, 27, 0.1);
          &:last-child {
            border-right: none;
          }
          h2 {
            margin-bottom: 0.106667rem;
            line-height: 0.266667rem;
            font-size: 0.266667rem;
            color: rgb(147, 153, 159);
          }
          .content {
            line-height: 0.64rem;
            font-size: 0.266667rem;
            color: rgb(7, 17, 27);
            .stress {
              font-size: 0.64rem;
            }
          }
        }
      }
      .favorite {
        position: absolute;
        right: 0.293333rem;
        top: 0.48rem;
        text-align: center;
        font-size: 0;
        width: 1.333333rem;
        .icon-favorite {
          display: block;
          line-height: 0.64rem;
          font-size: 0.64rem;
          color: #d4d6d9;
          margin-bottom: 0.106667rem;
          &.active {
            color: rgb(240, 20, 20);
          }
        }
        .text {
          line-height: 0.266667rem;
          font-size: 0.266667rem;
          color: rgb(77, 85, 93);
        }
      }
    }
    .bulletin {
      padding: 0.48rem 0.48rem 0.0rem 0.48rem;
      .title {
        margin-bottom: 0.213333rem;
        line-height: 0.373333rem;
        color: rgb(7, 17, 27);
        font-size: 0.373333rem;
      }
      .content-wrapper {
        padding: 0.0rem 0.32rem 0.426667rem 0.32rem;
        border-bottom: 0.013333rem solid rgba(7, 17, 27, 0.1);
        .content {
          line-height: 0.64rem;
          font-size: 0.32rem;
          color: rgb(240, 20, 20);
        }
      }
      .supports {
        .support-item {
          height: 1.28rem;
          line-height: 1.28rem;
          padding: 0.426667rem 0.32rem;
          border-bottom: 0.013333rem solid rgba(7, 17, 27, 0.1);
          font-size: 0.0rem;
          &:last-child {
            border: none;
          }
        }
        .icon {
          float: left;
          width: 0.426667rem;
          height: 0.426667rem;
          background-size: 0.426667rem 0.426667rem;
          background-repeat: no-repeat;
          &.discount {
            background-image: url("discount_1@3x.png");
          }
          &.decrease {
            background-image: url("decrease_1@3x.png");
          }
          &.invoice {
            background-image: url("invoice_1@3x.png");
          }
          &.guarantee {
            background-image: url("guarantee_1@3x.png");
          }
          &.special {
            background-image: url("special_1@3x.png");
          }
        }
        .text {
          float: left;
          margin-left: 0.16rem;
          line-height: 0.426667rem;
          font-size: 0.32rem;
          color: rgb(7, 17, 27);
        }
      }
    }
    .pics {
      padding: 0.48rem;
      .title {
        margin-bottom: 0.32rem;
        line-height: 0.373333rem;
        color: rgb(7, 17, 27);
        font-size: 0.373333rem;
      }
      .pic-wrapper {
        width: 100%;
        overflow: hidden;
        white-space: nowrap;
        .pic-list {
          font-size: 0.0rem;
          .pic-item {
            display: inline-block;
            width: 2.4rem;
            height: 2.4rem;
            margin-right: 0.16rem;
            &:last-child {
              margin-right: 0.0rem;
            }
            img{
              width: 2.4rem;
              height: 2.4rem;
            }
          }
        }
      }
    }
    .info {
      padding: 0.48rem 0.48rem 0.0rem 0.48rem;
      .title {
        padding-bottom: 0.32rem;
        line-height: 0.373333rem;
        color: rgb(7, 17, 27);
        font-size: 0.373333rem;
        border-bottom: 0.013333rem solid rgba(7, 17, 27, 0.1);
      }
      .info-item {
        padding: 0.426667rem 0.32rem;
        line-height: 0.426667rem;
        border-bottom: 0.013333rem solid rgba(7, 17, 27, 0.1);
        color: rgb(7, 17, 27);
        font-size: 0.32rem;
        &:last-child {
          border: none;
        }
      }
    }
  }
</style>














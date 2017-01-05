<template>
  <!--头部组件-->
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img :src="seller.avatar">
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
      <div v-if="seller.supports" class="support-count" @click="showDetail()">
        <span class="count">{{seller.supports.length}}个</span><span class="icon-keyboard_arrow_right"></span>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail()">
      <div class="bulletin-title"></div>
      <div class="bulletin-text">{{seller.bulletin}}</div>
      <div class="icon-keyboard_arrow_right"></div>
    </div>
    <div class="background">
      <img :src="seller.avatar">
    </div>

    <div v-show="detailShow" class="detail" transition="fade">
      <div class="detail-wrapper clearfix">
        <div class="detail-main">
          <div class="name">{{seller.name}}</div>
          <div class="star-wrapper">
            <star :size="0.533333" :space="0.533333" :score="seller.score"></star>
          </div>
          <div class="title">
            <div class="line"></div>
            <div class="text">优惠信息</div>
            <div class="line"></div>
          </div>
          <ul v-if="seller.supports" class='supports'>
            <li class="support-item" v-for="item in seller.supports">
              <span class="icon" :class="classMap[seller.supports[$index].type]"></span>
              <span class="text">{{seller.supports[$index].description}}</span>
            </li>
          </ul>
          <div class="title">
            <div class="line"></div>
            <div class="text">商家公告</div>
            <div class="line"></div>
          </div>
          <div class="bulletin">
            <p class="content">{{seller.bulletin}}</p>
          </div>
        </div>
      </div>
      <div class="detail-close" @click="hideDetail()">
        <i class="icon-close"></i>
      </div>
    </div>

  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../star/star';
  export default{
    props: {
      seller: {
        type: Object
      }
    },
    data() {
    	return {
    		detailShow: false,
        classMap: ['decrease', 'discount', 'special', 'invoice', 'guarantee']  // 减价 折扣 特价 有发票 外卖保障
      };
    },
    methods: {
      showDetail() {
        this.detailShow = true;
      },
      hideDetail() {
        this.detailShow = false;
      }
    },
    created() {
    	// this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];  // 减价 折扣 特价 有发票 外卖保障
    },
    components: {
    	star
    }

  };
</script>

<style lang="scss" rel="stylesheets/scss">
  @import "../../common/scss/mixin";
  @import "../../common/scss/icon.css";
  .header{
    position: relative;
    background: rgba(7,17,27,0.5);
    color: #fff;
    font-size: 0.213333rem;
    font-weight: 200;
    width: 10.0rem;
    height: 3.573333rem;
    .content-wrapper{
      position: relative;
      width: 10.0rem;
      height: 2.826667rem;
      padding: 0.64rem 0.32rem 0.48rem 0.64rem;
      .avatar{
        float: left;
        width: 1.706667rem;
        height: 1.706667rem;
        border-radius: 0.053333rem;
        img{
          width: 1.706667rem;
          height: 1.706667rem;
          border-radius: 0.053333rem;
        }
      }
      .content{
        float: left;
        margin: 0.053333rem 0.0rem 0.0rem 0.426667rem;
        width: 5.733333rem;
        height: 1.706667rem;
        overflow-x: hidden;
        .title{
          height: 0.48rem;
          .brand{
            float: left;
            width: 0.8rem;
            height: 0.48rem;
            background-image: url("brand@3x.png");
            background-size: 0.8rem 0.48rem;
            background-repeat: no-repeat;
          }
          .name{
            float: left;
            margin-left: 0.16rem;
            font-size: 0.426667rem;
            line-height: 0.48rem;
            font-weight: bold;
          }
        }
        .description{
          margin: 0.213333rem 0.0rem 0.266667rem 0.0rem;
          line-height: 0.32rem;
          font-size: 0.32rem;
          font-weight:200;
        }
        .support{
          height: 0.32rem;
          .icon{
            float: left;
            width: 0.32rem;
            height: 0.32rem;
            border-radius: 0.053333rem;
            background-size: 0.32rem 0.32rem;
            background-repeat: no-repeat;
            &.discount{
              background-image: url("discount_1@3x.png");
            }
            &.decrease{
              background-image: url("decrease_1@3x.png");
            }
            &.invoice{
              background-image: url("invoice_1@3x.png");
            }
            &.guarantee{
              background-image: url("guarantee_1@3x.png");
            }
            &.special{
              background-image: url("special_1@3x.png");
            }
          }
          .text{
            float: left;
            margin-left: 0.106667rem;
            line-height: 0.32rem;
            font-size: 0.32rem;
          }
        }
      }
      .support-count{
        position:absolute;
        width: 1.133333rem;
        height: 0.64rem;
        left: 8.533333rem;
        top: 1.773333rem;
        border-radius: 0.32rem;
        background: rgba(0,0,0,0.2);
        font-size: 0.266667rem;
        .count{
          line-height: 0.64rem;
          padding-left: 0.213333rem;
        }
        .icon-keyboard_arrow_right{
          line-height: 0.64rem;
          margin-left: 0.053333rem;
          font-size: 0.266667rem;
        }
      }
    }

    .bulletin-wrapper{
      position: absolute;
      background: rgba(7,17,27,0.2);
      width: 10.0rem;
      height: 0.746667rem;
      bottom: 0.0rem;
      padding-left: 0.32rem;
      .bulletin-title{
        width: 0.586667rem;
        height: 0.32rem;
        float: left;
        margin-top: 0.213333rem;
        background-image: url("bulletin@3x.png");
        background-size: 0.586667rem 0.32rem;
        background-repeat: no-repeat;
      }
      .bulletin-text{
        float: left;
        margin-left: 0.106667rem;
        line-height: 0.746667rem;
        font-size: 0.266667rem;
        width: 8.266667rem;
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
      }
      .icon-keyboard_arrow_right{
        float: right;
        margin: 0.24rem 0.4rem 0.0rem 0.0rem;
        font-size: 0.266667rem;
      }
    }
    .background{
      position: absolute;
      top:0;
      left:0;
      width: 10.0rem;
      height: 3.573333rem;
      overflow: hidden;
      z-index: -1;
      img{
        width: 10.0rem;
        filter: blur(0.133333rem);
      }
    }
    .detail{
      position: fixed;
      z-index: 100;
      top:0;
      left: 0;
      width:100%;
      height:100%;
      overflow: auto;
      blur: 0.133333rem;

      &.fade-transition{
        transition: all 0.5s;
        opicity:1;
        background: rgba(7,17,27,0.98);
      }
      &.fade-enter, &.fade-leave{
        opicity:0;
        background: rgba(7,17,27,0);
      }

      .detail-wrapper{
        min-height: 100%;
        width: 10.0rem;
        .detail-main{
          margin-top: 1.706667rem;
          padding-bottom: 1.706667rem;
          .name{
            line-height: 0.426667rem;
            text-align: center;
            font-size: 0.426667rem;
            font-weight:700;
          }
          .star-wrapper{
            margin-top: 0.48rem;
            text-align: center;
          }
          .title{
            display: flex;
            width: 80%;
            margin: 0.746667rem auto 0.64rem auto;
            .line{
              flex: 1;
              position: relative;
              top: -0.16rem;
              border-bottom: 0.013333rem solid rgba(255,255,255,0.2);
            }
            .text{
              padding: 0.0rem 0.32rem;
              font-size: 0.373333rem;
              font-weight: 700;
            }
          }
          .supports{
            width: 80%;

            margin: 0 auto;
            .support-item{
              height: 0.426667rem;
              padding: 0.0rem 0.32rem;
              margin-bottom: 0.32rem;
              &:last-child{
                margin-bottom: 0.0rem;
              }
              .icon{
                float: left;
                width: 0.426667rem;
                height: 0.426667rem;
                background-size: 0.426667rem 0.426667rem;
                background-repeat: no-repeat;
                &.discount{
                  background-image: url("discount_1@3x.png");
                }
                &.decrease{
                  background-image: url("decrease_1@3x.png");
                }
                &.invoice{
                  background-image: url("invoice_1@3x.png");
                }
                &.guarantee{
                  background-image: url("guarantee_1@3x.png");
                }
                &.special{
                  background-image: url("special_1@3x.png");
                }
              }
              .text{
                float: left;
                margin-left: 0.16rem;
                line-height: 0.426667rem;
                font-size: 0.32rem;
              }
            }
          }
          .bulletin{
            width: 80%;
            margin: 0.0rem auto;
            .content{
              padding: 0.0rem 0.16rem;
              line-height: 0.64rem;
              font-size:0.32rem;
              font-weight:200;
              color: #fff;
            }
          }
        }
      }
      .detail-close{
        position: relative;
        width: 0.853333rem;
        height: 0.853333rem;
        margin: -1.706667rem auto 0.0rem auto;
        clear: both;
        font-size: 0.853333rem;
      }
    }
  }

</style>
































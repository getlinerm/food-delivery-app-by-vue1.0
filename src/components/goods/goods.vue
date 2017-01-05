<template>
  <div class="goods">
    <div class="menu-wrapper" v-el:menu-wrapper>
      <ul>
        <li v-for="item in goods" class="menu-item" :class="{'current':currentIndex===$index}"
            @click="selectMenu($index,$event)">
          <span class="text">
            <span v-show="item.type>0" :class="classMap[item.type]" class="icon"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>

    <div class="foods-wrapper" v-el:foods-wrapper>
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li @click="selectFood(food,$event)" v-for="food in item.foods" class="food-item">
              <div class="icon">
                <img :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </div>

            </li>
          </ul>
        </li>
      </ul>
    </div>

    <shopcart v-ref:shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice"
              :min-price="seller.minPrice"></shopcart>
  </div>
  <food :food="selectedFood" v-ref:food></food>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import shopcart from '../shopcart/shopcart.vue';
  import cartcontrol from '../cartcontrol/cartcontrol.vue';
  import food from '../food/food.vue';

  const ERR_OK = 0;
  export default{
    props: {
      seller: {
        type: Object
      }
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
      this.$http.get('/api/goods').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.goods = response.data;
          this.$nextTick(() => {
            // goods接收数据以后在下一个tick初始化其他
            this._initScroll();
            this._calculateHeight();
          });
        }
      });
    },
    data() {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectedFood: {}
      };
    },
    computed: {
      // 获取foodlist的索引，以对应menu的指示
      currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i;
          }
        }
        return 0;
      },
      // 存储购物车的商品
      selectFoods() {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
    },
    methods: {
      selectMenu(index, event) {
        if (!event._constructed) {  //  插件派发的事件
          return;
        }
        let foodList = this.$els.foodsWrapper.getElementsByClassName('food-list-hook');
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el, 300);
      },
      selectFood(food, event) {
        if (!event._constructed) {  //  插件派发的事件
          return;
        }
        this.selectedFood = food;
        this.$refs.food.show();
      },
      _drop(target) {
        // 优化两个动画一并执行
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target);
        });
      },
      _initScroll() {
        this.menuScroll = new BScroll(this.$els.menuWrapper, {
          click: true
        });
        this.foodsScroll = new BScroll(this.$els.foodsWrapper, {
          probeType: 3,
          click: true
        });
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      _calculateHeight() {
        let foodList = this.$els.foodsWrapper.getElementsByClassName('food-list-hook'); // 拿到的元素是原生DOM
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) { // 拿到每种foodlist的高度
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
        }
      }
    },
    components: {
      shopcart,
      cartcontrol,
      food
    },
    events: {
      'cart.add'(target) {
        this._drop(target);
      }
    }
  };
</script>

<style lang="scss" rel="stylesheets/scss">
  .goods {
    display: flex;
    position: absolute;
    width: 100%;
    top: 4.586667rem;
    bottom: 1.226667rem;
    overflow: hidden;
    .menu-wrapper {
      flex: 0 0 2.133333rem;
      width: 2.133333rem;
      background: #f3f5f7;

      /*overflow-y: auto;*/
      .menu-item {
        display: table;
        height: 1.44rem;
        padding: 0.0rem 0.32rem;
        line-height: 0.373333rem;
        border-bottom: 0.013333rem solid rgba(7, 17, 27, 0.1);
        border-left: 0.066667rem solid #f3f5f7;
        &.current {
          position: relative;
          z-index: 10;
          margin-top: -0.013333rem;
          font-weight: 700;
          background: #fff;
          border-left: 0.066667rem solid #3190e8;
        }
        .icon {
          display: inline-block;
          vertical-align: top;
          margin-right: 0.053333rem;
          width: 0.32rem;
          height: 0.32rem;
          background-size: 0.32rem 0.32rem;
          background-repeat: no-repeat;
          &.discount {
            background-image: url("discount_3@3x.png");
          }
          &.decrease {
            background-image: url("decrease_3@3x.png");
          }
          &.invoice {
            background-image: url("invoice_3@3x.png");
          }
          &.guarantee {
            background-image: url("guarantee_3@3x.png");
          }
          &.special {
            background-image: url("special_3@3x.png");
          }
        }
        .text {
          display: table-cell;
          width: 1.493333rem;
          vertical-align: middle;
          font-size: 0.32rem;
        }
      }
    }
    .foods-wrapper {
      flex: 1;
      .title {
        padding-left: 0.373333rem;
        height: 0.693333rem;
        line-height: 0.693333rem;
        border-left: 0.053333rem solid #d9dde1;
        font-size: 0.32rem;
        color: rgb(147, 153, 159);
        background: #f3f5f7;
      }
      .food-item {
        display: flex;
        margin: 0.48rem 0.0rem 0.0rem 0.48rem;
        padding-bottom: 0.48rem;
        border-bottom: 0.013333rem solid rgba(7, 17, 27, 0.1);
        &:last-child {
          border: none;
        }
        .icon {
          flex: 0 0 1.52rem;
          margin-right: 0.266667rem;
          img{
            width:1.52rem;
            height:1.52rem;
          }
        }
        .content {
          flex: 1;
          position: relative;
          .name {
            margin: 0.053333rem 0.0rem 0.213333rem 0.0rem;
            height: 0.373333rem;
            line-height: 0.373333rem;
            font-size: 0.373333rem;
            color: rgb(7, 17, 27);
          }
          .desc, .extra {
            width: 5.133333rem;
            height: 0.266667rem;
            line-height: 0.266667rem;
            font-size: 0.266667rem;
            color: rgb(147, 153, 159);
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
          }
          .desc {
            margin-bottom: 0.213333rem;
            line-height: 0.32rem;
          }
          .extra {
            .count {
              margin-right: 0.32rem;
            }
          }
          .price {
            font-weight: 700;
            line-height: 0.64rem;
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
            right: 0.48rem;
            bottom: -0.16rem;
          }
        }
      }
    }
  }
</style>















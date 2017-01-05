<template>
  <div>
    <v-header :seller="seller"></v-header>
    <div class="tab">
      <div class="tab-item">
        <a v-link="{path:'/goods'}">商品</a>
      </div>
      <div class="tab-item">
        <a v-link="{path:'/ratings'}">评论</a>
      </div>
      <div class="tab-item">
        <a v-link="{path:'/seller'}">商家</a>
      </div>
    </div>

    <router-view :seller="seller" keep-alive></router-view>
  </div>
</template>
<script type="text/ecmascript-6">
  import header from './components/header/header.vue';
  import {urlParse} from './common/js/util';
  const ERR_OK = 0;

  export default{
    data() {
      return {
        seller: {
          id: (() => {
            let queryParam = urlParse();
            return queryParam.id;
          })()
        }
      };
    },
    created() {
      this.$http.get('/api/seller?id=' + this.seller.id).then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.seller = Object.assign({}, this.seller, response.data);
          console.log(this.seller.id);
        }
      });
    },
    components: {
      'v-header': header
    }
  };

</script>
<style lang="scss" rel="stylesheets/scss">
  .tab {
    display: flex;
    width: 100%;
    height: 1.066667rem;
    line-height: 1.066667rem;
    border-bottom: 0.013333rem solid rgba(7, 17, 27, 0.1);
    .tab-item {
      flex: 1;
      text-align: center;
      & > a {
        display: block;
        font-size: 0.373333rem;
        color: rgb(77, 85, 93);
        &.active {
          color: rgb(240, 20, 20);
        }
      }
    }
  }

</style>

<template>
  <div id="app">
    <v-header :seller= 'seller'></v-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to='/goods'>商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to='/ratings' :seller= 'seller'>评论</router-link>
      </div>
      <div class="tab-item">
        <router-link to='/seller'>商家</router-link>
      </div>
    </div>
    <keep-alive>
      <router-view :seller='seller'></router-view>
    </keep-alive>
  </div>
</template>

<script>
  import header from './components/header/header.vue';
  import {urlParse} from './common/js/util';

  const ERR_OK = 0;

  export default {
    data() {
      return {
        seller: {
          id: (() => {
            let queryParam = urlParse();
            console.log(queryParam);
            return queryParam.id;
          })()
        }
      };
    },

    // 获取数据
    created() {
      this.$http.get('api/seller').then((res, req) => {
        res = res.body;
        if (res.errno === ERR_OK) {
          // 保存数据
          // this.seller = res.data;
          // console.log(res.data);
          // 保存id
          this.seller = Object.assign({}, this.seller, res.data);
          console.log(this.seller.id);
        }
      });
    },
    components: {
      'v-header': header
    }
  };
</script>

<style lang='stylus'>
  @import "./common/stylus/mixin.styl";

  #app
    .tab
      display: flex
      width: 100%
      height: 40px
      line-height: 40px
      /* border-bottom: 1px solid rgba(7,17,27,0.1) */
      border-1px(rgba(7,17,27,0.1))

      .tab-item
        flex: 1
        text-align: center
        & > a
          display: block
          font-size: 14px
          color: rgb(77,85,93)
          &.active
            color: rgb(240,20,20)
</style>

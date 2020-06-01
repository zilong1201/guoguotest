<template>
  <div class="seller">
    <div class="seller-content">
      <div class="overview">
        <h1 class='title'>{{seller.name}}</h1>
        <div class="desc border-1px">
          <star :size='36' :score='seller.score'></star>
          <span class='text'>({{seller.ratingCount}})</span>
          <span class='text'>{{seller.sellCount}}</span>
        </div>
        <ul class="remark">
          <li class='block'>
            <h2>起送价</h2>
            <div class="content">
              <span class='stress'>{{seller.minPrice}}元</span>
            </div>
          </li>
          <li class='block'>
            <h2>商家配送</h2>
            <div class="content">
              <span class='stress'>{{seller.deliveryPrice}}元</span>
            </div>
          </li>
          <li class='block'>
            <h2>平均配送时间</h2>
            <div class="content">
              <span class='stress'>{{seller.deliveryTime}}分钟</span>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
  import star from 'components/star/star';

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      star
    }
  };
</script>

<style lang="stylus" scoped>
  @import '../../common/stylus/mixin.styl';

  .seller
    position: absolute
    top: 174px
    left: 0
    bottom: 0
    width: 100%
    overflow: hidden
    .overview
      padding: 18px
      .title
        margin-bottom: 8px
        line-height: 14px
        font-size: 14px
        color: rgb(7,12,27)
      .desc
        padding: 18px
        line-height: 18px
        font-size: 0
        border-1px(rgba(7,17,27,0.1))
        .star
          display: inline-block
          margin-right: 8px
          vartical-align: top
        .text
          margin-right: 12px
          line-height: 18px
          font-size: 10px
          vartical-align: top
          color: rgb(77,85,93)
</style>

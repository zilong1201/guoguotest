<template>
  <div class="shopcart">
    <div class="content" @click='toggleList'>
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class='{"highlight":totalCount>0}'>
            <i class='icon-shopping_cart' :class='{"highlight":totalCount>0}'></i>
          </div>
          <div class="num" v-show='totalCount>0'>{{totalCount}}</div>
        </div>
        <div class='price' :class='{"highlight":totalPrice>0}'>￥{{totalPrice}}元</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right" @click.stop.prevent='pay'>
        <div class="pay" :class='payClass'>{{payDesc}}</div>
      </div>
    </div>
    <transition
      @before-enter='beforEnter'
      @enter = 'enter'
      @after-enter = 'afterEnter'
    >
      <div class="ball-container" v-show='flag'>
          <div v-for='(ball, index) in balls' v-show='ball.show' :key='index' class='ball'>
            <div class="inner inner-hook"></div>
          </div>
      </div>
    </transition>
    <div class="shopcart-list" v-show='listShow'>
      <div class="list-header">
        <h1 class="title">购物车</h1>
        <span class='empty' @click='empty'>清空</span>
      </div>
      <div class="list-content" ref='listContent'>
        <ul>
          <li class='food' v-for='(food, index) in selectFoods' :key='index'>
            <span class="name">{{food.name}}</span>
            <div class="price">
              <span>￥{{food.price*food.count}}</span>
            </div>
            <div class='cartcontrol-wrapper'>
              <cartcontrol :food='food' @drop='drop'></cartcontrol>
            </div>
          </li>
        </ul>
      </div>
    </div>
    <div class="list-mask" @click='hideList' v-show='listShow'></div>
  </div>
</template>

<script>

import BScroll from 'better-scroll';
import cartcontrol from 'components/cartcontrol/cartcontrol';

export default {
  components: {
    cartcontrol
  },
  props: {
    selectFoods: {
      type: Array,
      default () {
        return [
          {
            price: 10,
            count: 3
          },
          {
            price: 10,
            count: 1
          }
        ];
      }
    },
    deliveryPrice: {
      type: Number
    },
    minPrice: {
      type: Number
    },
    screenX: {
      type: Number
    },
    screenY: {
      type: Number
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
      flag: false,
      fold: false
    };
  },
  watch: {},
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
        return 'no-enough';
      } else {
        return 'enough';
      }
    },
    // 控制购物车详情的显示
    listShow: {
      get() {
        return this.fold;
      },
      set() {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        }
        let show = !this.fold;
        return show;
      }
    }
  },
  methods: {
    // 获取点击的兄弟组件，并将小球添加到dropBalls数组中
    drop(el) {
      // console.log(el);
      this.flag = true;
      for (let i = 0; i < this.balls.length; i++) {
        let ball = this.balls[i];
        if (!ball.show) {
          ball.show = true;
          this.ballel = el;
          this.dropBalls.push(ball);
          // console.log(this.ballel.getBoundingClientRect());
          return;
        }
      }
    },
    // 购物车页面显示的事件
    toggleList() {
      if (!this.totalCount) {
        return;
      }
      this.fold = !this.fold;
      // console.log(this.listShow);
      // 添加滑动效果
      this.$nextTick(() => {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.listContent, {
          click: true
          });
        } else {
          this.scroll.refresh();
        }
      });
    },
    // 清空购物车的事件
    empty() {
      this.selectFoods.forEach((food) => {
        food.count = 0;
      });
    },
    // 点击遮罩层取消购物车详情的显示
    hideList() {
      this.fold = false;
    },
    // 支付
    pay() {
      if (this.totalPrice < this.minPrice) {
        return;
      }
      window.alert(`支付￥${this.totalPrice}元`);
    },
    beforEnter(el) {
      // 获取小球的初始位置，即点击的添加的位置
      el.style.bottom = window.innerHeight - this.ballel.getBoundingClientRect().bottom + 'px';
      el.style.left = this.ballel.getBoundingClientRect().left + 'px';
    },
    enter(el) {
      // 获取小球的最终位置，即购物车的位置
      console.log(el.offsetWidth);
      el.style.bottom = '22px';
      el.style.left = '32px';
      // el.style.transform = 'translate3d(0, 0, 0)';
      el.style.transition = 'all 1s ease';
    },
    afterEnter(el) {
      let count = this.balls.length;
      while (count--) {
        let ball = this.dropBalls.shift();
        if (ball) {
          ball.show = false;
          el.style.display = 'none';
          this.flag = false;
        }
      }
    }
  },
  created() {
  },
  mounted() {}
};
</script>
<style lang="stylus" scoped>
  @import "../../common/stylus/mixin.styl";

  .shopcart
    position: fixed
    left: 0
    bottom: 0
    z-index: 50
    width: 100%
    height: 48px
    background-color: #000
    .content
      display: flex
      background: #141d27
      font-size: 0
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          vertical-align: top
          position: relative
          top: -10px
          margin: 0 12px
          padding: 6px
          width: 56px
          height: 56px
          box-sizing: border-box
          border-radius: 50%
          background: #141d27
          .logo
            width: 100%
            height: 100%
            border-radius: 50%
            background: #2b343c
            text-align: center
            &.highlight
              background: rgb(0, 160, 220)
            .icon-shopping_cart
              line-height: 44px
              font-size: 24px
              color: #80858a
              &.highlight
                color: #fff
          .num
            position: absolute
            top: 0
            right: 0
            width: 24px
            height: 16px
            line-height: 16px
            text-align: center
            border-radius: 16px
            font-size: 9px
            font-weight: 700
            color: #fff
            background: rgb(240, 20, 20)
            box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4)
        .price
          display: inline-block
          vertical-align: top
          margin-top: 12px
          line-height: 24px
          padding-right: 12px
          box-sizing: border-box
          border-right: 1px solid rgba(255, 255, 255, 0.1)
          font-size: 16px
          font-weight: 700
          color: rgba(255, 255, 255, 0.4)
          &.highlight
            color: #fff
        .desc
          display: inline-block
          vertical-align: top
          margin: 12px 0 0 12px
          line-height: 24px
          color: rgba(255, 255, 255, 0.4)
          font-size: 10px
      .content-right
        flex: 0 0 105px
        width: 105px
        .pay
          height: 48px
          line-height: 48px
          text-align: center
          font-size: 12px
          color: rgba(255, 255, 255, 0.4)
          font-weight: 700
          &.not-enough
            background: #2b333b
          &.enough
            background: #00b43c
            color: #fff
    .ball-container
      position: fixed
      .ball
        z-index: 200
        .inner
          width: 16px
          height: 16px
          border-radius: 50%
          background: rgb(0, 160, 200)
    .shopcart-list
      position: absolute
      top: 0
      left: 0
      z-index: -1
      width: 100%
      transform: translate3D(0,-100%,0)
      .list-header
        height: 40px
        line-height: 40px
        padding: 0 18px
        background-color: #f3f5f7
        bordoe-bottom: 1px solid rgba(7,17,27,0.1)
        .title
          float: left
          font-size: 14px
          color: rgb(7,17,27)
        .empty
          float: right
          font-size: 12px
          color: rgb(0,160,220)
      .list-content
        padding: 0 18px
        max-height: 217px
        background-color: #fff
        overflow: hidden
        .food
          position: relative
          padding: 12px 0
          box-sizing: border-box
          border-1px(rgba(7,17,27,0.1))
          .name
            line-height: 24px
            font-size: 14px
            color: rgb(7,17,27)
          .price
            position: absolute
            right: 90px
            bottom: 12px
            line-height: 24px
            font-weight: 700
            font-size: 14px
            color: rgb(240,20,20)
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom: 16px
    .list-mask
      position: fixed
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: -10
      backdrop-filter: blur(10px)
      background-color: rgba(7,17,27,0.6);
</style>

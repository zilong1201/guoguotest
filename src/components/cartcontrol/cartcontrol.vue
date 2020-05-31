<template>
  <div class="cartcontrol">
    <transition name='move'>
      <div class="cart-decrease" v-show='food.count>0' @click.stop='decreaseCart'>
        <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="cart-count" v-show='food.count>0'>{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop='addCart' ref='addCart'></div>
  </div>
</template>

<script>
import Vue from 'vue';

export default {
  components: {},
  props: {
    food: {
      type: Object
    }
  },
  data() {
    return {
    };
  },
  watch: {},
  computed: {},
  methods: {
    addCart(event) {
      // 在PC上或某些手机端，由于未成功将touchend事件move掉，点击事件会执行两次,这个方法可以解除
      // if (!event._constructed) {
      //   return;
      // }
      if (!this.food.count) {
        Vue.set(this.food, 'count', 1);
      } else {
        this.food.count++;
      }
      // 触发父组件的方法
      // console.log('1');
      this.$emit('clickBrother', this.$refs.addCart);
      // this.$emit('clickBrother3', this.$refs.addCart);
      this.$emit('drop', this.$refs.addCart);
    },
    decreaseCart(event) {
      // 在PC上或某些手机端，由于未成功将touchend事件move掉，点击事件会执行两次,这个方法可以解除
      // if (!event._constructed) {
      //   return;
      // }
      if (this.food.count) {
        this.food.count--;
      }
    }
  },
  created() {

  },
  mounted() {}
};
</script>
<style lang="stylus" scoped>
  .cartcontrol
    font-size: 0
    .cart-decrease
      display: inline-block
      padding: 6px
      .inner
        display: inline-block
        line-height: 24px
        font-size: 24px
        color: rgb(0, 160, 240)
    .move-enter, .move-leave-to
      opacity: 0
      transform: translate3D(24px,0,0) rotate(180deg)
    .move-enter-to, .move-leave
      opacity: 1
      transform: translate3D(0,0,0) rotate(0deg)
    .move-enter-active,.move-leave-active
      transition: all 0.4s linear
    .cart-count
      display: inline-block
      vertical-align: top
      width: 12px
      padding-top: 6px
      line-height: 24px
      text-align: center
      font-size: 10px
      color: rgb(147, 153, 159)
    .cart-add
      display: inline-block
      padding: 6px
      line-height: 24px
      font-size: 24px
      color: rgb(0, 160, 240)
</style>

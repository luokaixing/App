<!--  -->
<template>
  <div class="cartcontrol">
    <transition name='move'>
      <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="decCart"><img src="@/assets/dec.png" alt="" class="cart-img"></div>

    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add" @click.stop.prevent="addCart($event)"><img src="@/assets/add.png" alt="" class="cart-img"></div>
  </div>
</template>

<script>
import Vue from 'vue';
export default {
  props:{
    food:{
      type:Object
    }
  },
  data () {
    return {
    }
  },
  created(){
    // console.log(this.food)
  },
  methods:{
    addCart(event){
      if(!event._constructed){
        return
      }
      if(!this.food.count){
        Vue.set(this.food,'count',1)
      }else{
        this.food.count++
      }
      this.$emit('add',event.target)
    },
    decCart(event){
      if(!event._constructed){
        return
      }
      if(this.food.count){
        this.food.count--
      }
    }
  }
}
</script>

<style lang='scss' scoped>
  .cartcontrol{
    font-size:0;
    .cart-decrease{
      display:inline-block;
      padding:6px;
      line-height: 24px;
      font-size:24px;
      transition:all 0.4s linear;
      .cart-img{
        width:20px;
        height:20px;
      }
    }
    .move-enter-active,.move-leave-active{
      opacity:1;
      transform:translate3d(0,0,0) rotate(0);
    }
    .move-enter,.move-leave-to{
      opacity:0;
      transform:translate3d(48px,0,0) rotate(180deg);
    }
    .cart-count{
      display:inline-block;
      vertical-align: top;
      width:12px;
      padding-top:4px;
      line-height:24px;
      text-align:center;
      font-size:10px;
      color:rgb(147,153,159);
    }
    .cart-add{
      display:inline-block;
      line-height: 24px;
      font-size:24px;
      padding:6px;
      .cart-img{
        width:20px;
        height:20px;
      }
    }
  }
</style>

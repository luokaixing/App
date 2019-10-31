<!--  -->
<template>
  <div>
    <div class="shopcart">
      <div class="container" @click="toggleList">
          <div class="container-left">
              <div class="logo-wrapper">
                <div class="logo" :class="{'highLight':totalCount>0}">
                    <img src="./shop-cart-2.png" alt="" class="icon-shopping_cart">
                </div>
                <div class="number" v-show="totalCount>0">{{totalCount}}</div>
              </div>
              <div class="price" :class="{'highLight':totalCount>0}">￥{{totalPrice}}元</div>
              <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
          </div>
          <div class="container-right" @click.stop.prevent="pay">
            <div class="pay" :class="payClass">{{payDesc}}</div>
          </div>
      </div>
      <div class="ball-container">
        <div  v-for="(ball,index) in balls" :key="'ball-'+index">
          <transition 
            @before-enter="handleBeforeEnter"
            @enter="handleEnter"
            @after-enter="handleAfterEnter"
          >
            <div v-show="ball.show" class="ball">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
      </div>
      <transition name="fold" mode="out-in">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li class="food" v-for="(food,index) in selectFoods" :key="index">
                <span class="name">{{food.name}}</span>
                <div class="price"><span>￥{{food.price*food.count}}</span></div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food" @add="add"></cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
    <div class="list-mask" v-show="listShow" @click="hideList"></div>
  </div>
</template>

<script>
import shopcart from '@/components/shopcart/shopcart'
import BScroll from 'better-scroll'
import cartcontrol from '@/components/cartcontrol/cartcontrol'
export default {
  props:{
    selectFoods:{
      type:Array,
      default(){
        return []
      }
    },
    deliveryPrice:{
      type:Number,
      default:0
    },
    minPrice:{
      type:Number,
      default:20
    }
  },
  data () {
    return {
      balls:[
        {
        show:false
      },{
        show:false
      },{
        show:false
      },{
        show:false
      },{
        show:false
      }
      ],
      dropBalls:[],
      fold:true
    }
  },
  computed:{
    totalPrice(){
      let total=0;
      this.selectFoods.forEach((food)=>{
        total+=food.price*food.count
      })
      return total
    },
    totalCount(){
      let count=0
      this.selectFoods.forEach((food)=>{
        count+=food.count
      })
      return count
    },
    payDesc(){
      if(this.totalPrice===0){
        return `￥${this.minPrice}元起送`
      }else if(this.totalPrice<this.minPrice){
        return `还差￥${this.minPrice-this.totalPrice}元起送`
      }else{
        return '去结算'
      }
    },
    payClass(){
      if(this.totalPrice<this.minPrice){
        return 
      }else{
        return 'active'
      }
    },
    listShow(){
      if(!this.totalCount){
        this.fold=true
        return false
      }
      let show=!this.fold
      if(show){
        this.$nextTick(()=>{
          if(!this.scroll){
            this.scroll=new BScroll(this.$refs.listContent,{
              click:true
            })
          }else{
            this.scroll.refresh()
          }
        })
      }
      return show
    }
  },
  methods:{
    add(target){
      this.drop(target)
    },
    drop(el){
      // console.log(el)
      for(let i=0;i<this.balls.length;i++){
        let ball=this.balls[i]
        if(!ball.show){
          ball.show=true
          ball.el=el
          this.dropBalls.push(ball)
          // console.log(this.dropBalls,111)
          return
        }
      }
    },
    toggleList(){
      if(!this.totalCount){
        return
      }
      this.fold=!this.fold
    },
    empty(){
      this.selectFoods.forEach((food)=>{
        food.count=0
      })
    },
    hideList(){
      this.fold=true
    },
    pay(){
      if(this.totalPrice<this.minPrice){
        return
      }
      window.alert(`支付${this.totalPrice}元`)
    },
    handleBeforeEnter(el){
      // console.log('success')
      let count = this.balls.length 
      while (count--) {
      let ball = this.balls[count]
      if (ball.show) {
      let rect = ball.el.getBoundingClientRect() // getBoundingClientRect()获取小球相对于视窗的位置，屏幕左上角坐标为0，0
      let x = rect.left - 32 // 小球x方向位移= 小球距离屏幕左侧的距离-外层盒子距离水平的距离
      let y = -(window.innerHeight - rect.top - 22) // 负数，因为是从左上角向下
      el.style.display = ''
      el.style.webkitTransform = `translate3d(0,${y}px,0)` // 设置外层盒子，即小球垂直方向的位移
      el.style.transform = `translate3d(0,${y}px,0)`
      let inner = el.getElementsByClassName('inner-hook')[0]
      inner.style.webkitTransform = `translate3d(${x}px,0,0)` // 设置内层盒子，即小球水平方向的距离
      inner.style.transform = `translate3d(${x}px,0,0)`
      }
      }
    },
    handleEnter(el,done){
      // console.log('Two-success')
      let rf = el.offsetHeight
      this.$nextTick(() => { // 让动画效果异步执行,提高性能
      el.style.webkitTransform = 'translate3d(0, 0, 0)'// 设置小球掉落后最终的位置
      el.style.transform = 'translate3d(0, 0, 0)'
      let inner = el.getElementsByClassName('inner-hook')[0]
      inner.style.webkitTransform = 'translate3d(0, 0, 0)'
      inner.style.transform = 'translate3d(0, 0, 0)'
      el.addEventListener('transitionend', done) // Vue为了知道过渡的完成，必须设置相应的事件监听器。它可以是transitionend或 animationend
      })
    },
    handleAfterEnter(el){
      // console.log('Three-success')
      let ball = this.dropBalls.shift() // 完成一次动画就删除一个dropBalls的小球
      if (ball) {
      ball.show = false
      el.style.display = 'none'
      }
    }
  },
  components:{
    shopcart,
    cartcontrol,
  }  
}
</script>

<style lang='scss' scoped>
@import '@/common/scss/mixin.scss';
.shopcart{
  position:fixed;
  left:0;
  bottom:0;
  width:100%;
  height:48px;
  z-index:50;
  .container{
    display:flex;
    background: #141d27;
    font-size:0;
    .container-left{
      flex:1;
      .logo-wrapper{
        display:inline-block;
        position:relative;
        top:-10px;
        margin:0 12px;
        padding:6px;
        width:56px;
        height:56px;
        box-sizing:border-box;
        vertical-align:top;
        border-radius:50%;
        background-color: #141d27;
        .logo{
          width:100%;
          height:100%;
          border-radius:50%;
          background: #2b343c;
          &.highLight{
            background:rgb(0,160,220);
          }
          .icon-shopping_cart{
            color:#80858a;
            text-align:center;
            margin:6px;
            &.highLight{
              color:white;
            }
          }
        }
        .number{
          position:absolute;
          top:0;
          right:0;
          width:24px;
          height:16px;
          line-height: 16px;
          text-align:center;
          border-radius:16px;
          color:#fff;
          font-size:9px;
          font-weight:700;
          background: rgb(240,20,20);
          box-shadow: 0 4px 8px 0 rgba(0,0,0,0.4);
        }
      }
      .price{
        display:inline-block;
        vertical-align:top;
        line-height: 24px;
        margin-top:12px;
        padding-right:12px;
        box-sizing:border-box;
        border-right:1px solid rgba(255,255,255,0.1);
        font-size:16px;
        font-weight:700;
        color:rgba(255,255,255,0.1);
        &.highLight{
          color:#fff;
        }
      }
      .desc{
        display:inline-block;
        vertical-align: top;
        margin:12px 0 0 12px;
        line-height:24px;
        color:rgba(255,255,255,0.1);
        font-size:10px;
      }
    }
    .container-right{
      flex:0 0 105px;
      width:105px;
      .pay{
        line-height: 48px;
        height:48px;
        text-align:center;
        font-size:12px;
        color:rgba(255,255,255,0.1);
        font-weight:700;
        background: #2b333b;
        &.active{
          background: #00b43c;
          color:#fff;
        }
      }
    }
  }
  .ball-container{
    /* .drop-transition{
      transition:all 0.4s;
      .inner{
        width:16px;
        height:16px;
        border-radius:50%;
        background:rgb(0,160,220);
        transition:all 0.4s;
      }
    } */
    .ball{
      position:fixed;
      left:32px;
      bottom:22px;
      z-index:200;
      transform:translate3d(0,0,0);
      transition:all 0.6s cubic-bezier(0.49,-0.29,0.75,0.41);
      .inner{
        width:16px;
        height:16px;
        border-radius:100%;
        background: rgb(0,160,220);
        transition:all 0.6s linear;
        transform:translate3d(0,0,0)
      }
    }
  }
  .fold-enter-active,.fold-leave-active{
    transition:all 0.5s;
    transform: translate3d(0,0,0);
  }
  .fold-enter,.fold-leave-to{
    transform: translate3d(0,100%,0);
  }
  .shopcart-list{
    position:absolute;
    left:0;
    bottom:48px;
    z-index:-1;
    width:100%;
    .list-header{
      height:40px;
      line-height:40px;
      padding:18px;
      background:#f3f5f7;
      border-bottom:2px solid rgba(7,17,27,0.1);
      .title{
        float:left;
        font-size:14px;
        color:rgb(7,17,27);
      }
      .empty{
        float:right;
        font-size:12px;
        color:rgb(0,160,220);
      }
    }
    .list-content{
      padding:0 18px;
      max-height:217px;
      background:#fff;
      overflow: hidden;
      .food{
        position:relative;
        padding:12px 0;
        box-sizing:border-box;
        @include border-1px(rgba(7,17,27,0.1));
        .name{
          line-height: 24px;
          font-size:14px;
          color:rgb(7,17,27);
        }
        .price{
          line-height: 24px;
          position:absolute;
          right:90px;
          bottom:12px;
          font-size:14px;
          font-weight:700;
          color:rgb(140,20,20);
        }
        .cartcontrol-wrapper{
          position:absolute;
          right:0;
          bottom:6px;
        }
      }
    }
  }
}
.list-mask{
  position:fixed;
  top:0;
  left:0;
  width:100%;
  height:100%;
  z-index:40;
  background:rgba(7,17,27,0.6);
  backdrop-filter:blur(10px);
}
</style>

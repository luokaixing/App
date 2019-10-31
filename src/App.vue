<template>
  <div id="app">
    <div class="head">
      <v-head :seller="seller"></v-head>
    </div>
    <div class="tab">
      <router-link to="/goods" class="tab-item">商品</router-link>
      <router-link to="/ratings" class="tab-item">评论</router-link>
      <router-link to="/seller" class="tab-item">商家</router-link>
    </div>
    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>
  </div>
</template>

<script>
import head from './components/head/head'
import {urlParse} from './common/js/util'
const ERR_OK=0
export default {
  name: 'App',
  data() {
    return {
      seller:{
        id:(()=>{
          let queryParam=urlParse();
          // console.log(queryParam)
          return queryParam.id
        })()
      },
    }
  },
  components:{
    'v-head':head
  },
  created:function(){
    this.$http.get('/api/seller?id=').then((response) => {
      response=response.body
      if(response.errno===ERR_OK){
        // this.seller=response.data
        this.seller=Object.assign({},this.seller,response.data)
        // console.log(this.seller.id)
      }
    }).catch((error)=>{
      console.log(error)
    })
  }
}
</script>

<style lang='scss'>
  @import '@/common/scss/mixin.scss';
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  .tab{
    display: flex;
    height: 40px;
    width: 100%;
    line-height: 40px;
    /* border-bottom:1px solid rgba(7,71,27,0.1);   */
    @include border-1px(rgba(7,71,27,0.1));
    .tab-item{
      flex: 1;
      text-align: center;
      color:rgb(77,85,93);
      font-size:14px;
      &.router-link-active{
        color:rgb(240,20,20)
      }
    }
  }
}
</style>

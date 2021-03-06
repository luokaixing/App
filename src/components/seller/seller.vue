<!-- seller page -->
<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc border-1px">
          <star :size="36" :score="seller.score"></star>
          <span class="text">({{seller.ratingCount}})</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block">
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
        <div class="favorite" @click="textToggle">
          <span class="icon-f" :class="{'active':favorite}"></span>
          <div class="text">{{favoriteText}}</div>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content-wrapper">
          <p class="content">{{seller.bulletin}}</p>
        </div>
        <ul v-if="seller.supports" class="supports">
          <li class="support-item border-1px" v-for="(item,index) in seller.supports" :key="index">
            <span class="icon" :class="classMap[seller.supports[index].type]"></span>
            <span class="text">{{seller.supports[index].description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <li v-for="(pic,index) in seller.pics" :key="'pic'+index" class="pic-item">
              <img :src="pic" alt width="120" height="90" />
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="info">
        <h1 class="title border-1px">商家信息</h1>
        <ul>
          <li v-for="(info,index) in seller.infos" :key="'info'+index" class="info-item">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import star from "@/components/star/star";
import { saveToLocal, loadFromLocal } from "@/common/js/store";
import split from "@/components/split/split";
import BScroll from "better-scroll";

export default {
  data() {
    return {
      // favorite:(()=>{
      //   return loadFromLocal(this.seller.id,'favorite',false)
      // })()
      favorite: false
    };
  },
  props: {
    seller: {
      type: Object
    }
  },
  created() {
    this.classMap = ["decrease", "discount", "special", "invoice", "guarantee"];
  },
  watch: {
    seller() {
      this._initScoll();
      this._picScroll();
      this.favorite = loadFromLocal(this.seller.id, "favorite", false);
    }
  },
  methods: {
    _initScoll() {
      if (!this.scroll) {
        this.scroll = new BScroll(this.$refs.seller, {
          click: true
        });
      } else {
        this.scroll.refresh();
      }
    },
    _picScroll() {
      if (this.seller.pics) {
        let picWidth = 120;
        let margin = 6;
        let width = (picWidth + margin) * this.seller.pics.length;
        this.$refs.picList.style.width = width + "px";
        if (!this.picScroll) {
          this.$nextTick(() => {
            this.picScroll = new BScroll(this.$refs.picWrapper, {
              scrollX: true, //横向滚动
              eventPassthrough: "vertical"
            });
          });
        } else {
          this.picScroll.refresh();
        }
      }
    },
    textToggle() {
      if (!event._constructed) {
        return;
      }
      this.favorite = !this.favorite;
      saveToLocal(this.seller.id, "favorite", this.favorite);
      // localStorage.favorite=this.favorite
    }
  },
  mounted() {
    this._initScoll();
    this._picScroll();
  },
  components: {
    star,
    split
  },
  computed: {
    favoriteText() {
      return this.favorite ? "已收藏" : "收藏";
    }
  }
};
</script>

<style lang='scss' scoped>
@import "@/common/scss/mixin.scss";
.seller {
  position: absolute;
  top: 174px;
  left: 0;
  bottom: 0;
  width: 100%;
  overflow: hidden;
  .overview {
    padding: 18px;
    position: relative;
    .title {
      margin-bottom: 8px;
      line-height: 14px;
      color: rgb(7, 17, 27);
      font-size: 14px;
    }
    .desc {
      padding-bottom: 18px;
      line-height: 18px;
      font-size: 0;
      @include border-1px(rgba(7, 17, 27, 0.1));
      .star {
        display: inline-block;
        margin-right: 8px;
        vertical-align: top;
      }
      .text {
        display: inline-block;
        vertical-align: top;
        margin-right: 12px;
        color: rgb(77, 85, 93);
        font-size: 10px;
      }
    }
    .remark {
      display: flex;
      padding-top: 18px;
      .block {
        flex: 1;
        text-align: center;
        border-right: 1px solid rgba(7, 17, 27, 0.1);
        &:last-child {
          border: none;
        }
        h2 {
          font-size: 10px;
          margin-bottom: 4px;
          line-height: 10px;
          color: rgb(147, 153, 159);
        }
        .content {
          line-height: 24px;
          font-size: 10px;
          color: rgb(7, 17, 27);
          .stress {
            font-size: 24px;
          }
        }
      }
    }
    .favorite {
      position: absolute;
      width: 50px;
      right: 11px;
      top: 18px;
      text-align: center;
      .icon-f {
        display: inline-block;
        width: 24px;
        height: 24px;
        background-image: url(../../assets/like.png);
        background-repeat: no-repeat;
        background-size: 100% 100%;
        &.active {
          background-image: url(../../assets/liked.png);
          background-repeat: no-repeat;
          background-size: 100% 100%;
        }
      }
      .text {
        padding-top: 5px;
        line-height: 10px;
        font-size: 10px;
        color: rgb(77, 85, 93);
      }
    }
  }
  .bulletin {
    padding: 18px 18px 0 18px;
    .title {
      margin-bottom: 8px;
      line-height: 14px;
      color: rgb(7, 17, 27);
      font-size: 14px;
    }
    .content-wrapper {
      padding: 0 12px 16px 12px;
      @include border-1px(rgba(7, 17, 27, 0.1));
      .content {
        line-height: 24px;
        font-size: 12px;
        color: rgb(240, 20, 20);
      }
    }
    .supports {
      .support-item {
        padding: 16px 12px;
        @include border-1px(rgba(7, 17, 27, 0.1));
        font-size: 0;
      }
      .icon {
        display: inline-block;
        width: 12px;
        height: 12px;
        margin-right: 4px;
        background-size: 12px 12px;
        background-repeat: no-repeat;
        &.decrease {
          @include bg-image("decrease_4");
        }
        &.discount {
          @include bg-image("discount_4");
        }
        &.guarantee {
          @include bg-image("guarantee_4");
        }
        &.invoice {
          @include bg-image("invoice_4");
        }
        &.special {
          @include bg-image("special_4");
        }
      }
      .text {
        line-height: 16px;
        font-size: 12px;
        display: inline-block;
        vertical-align: top;
        margin-top: -2px;
        color: rgb(7, 17, 27);
      }
    }
  }
  .pics {
    padding: 18px;
    .title {
      margin-bottom: 12px;
      line-height: 14px;
      font-size: 14px;
      color: rgb(7, 17, 27);
    }
    .pic-wrapper {
      width: 100%;
      overflow: hidden;
      white-space: nowrap;
      .pic-list {
        font-size: 0;
        .pic-item {
          display: inline-block;
          margin-right: 6px;
          width: 120px;
          height: 90px;
          &:last-child {
            margin-right: 0;
          }
        }
      }
    }
  }
  .info {
    padding: 18px 18px 0 18px;
    .title {
      padding-bottom: 12px;
      line-height: 14px;
      @include border-1px(rgba(7, 17, 27, 0.1));
      color: rgb(7, 17, 27);
      font-size: 14px;
    }
    .info-item {
      padding: 16px 12px;
      font-size: 12px;
      color: rgb(7, 17, 27);
      line-height: 16px;
      @include border-1px(rgba(7, 17, 27, 0.1));
      &:last-child {
        border: none;
      }
    }
  }
}
</style>

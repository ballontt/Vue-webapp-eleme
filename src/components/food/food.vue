<template>
  <div v-show="showFlag" class="food" transition="move" v-el:food>
    <div class="food-content">
      <!--头图-->
      <div class="image-header">
        <img :src="food.image">
        <div class="back" @click="hide">
          <i class="icon-arrow_lift"></i>
        </div>
      </div>

      <!--介绍-->
      <div class="content">
        <h1 class="title">{{food.name}}</h1>
        <div class="detail">
          <span class="sell-count">月售{{food.sellCount}}</span>
          <span class="rating">好评{{food.rating}}%</span>
        </div>
        <div class="price">
          <span class="now">¥{{food.price}}</span><span class="old" v-show="food.oldPrice">¥{{food.oldPrice}}</span>
        </div>
        <!--增加删除按钮组件-->
        <div class="cartcontrol-wrapper">
          <cartcontrol :food="food"></cartcontrol>
        </div>
        <!--加入购物车按钮-->
        <div @click.stop.prevent="addFirst($event)"
             class="buy"
             v-show="!food.count || food.count===0"
             transition="fade">加入购物车
        </div>
      </div>

      <!--间隙-->
      <split v-show="food.info"></split>

      <!--商品详情-->
      <div class="info" v-show="food.info">
        <h1 class="title">商品信息</h1>
        <p class="text">{{food.info}}</p>
      </div>

      <!--间隙-->
      <split></split>

      <!--评价选择-->
      <div class="rating">
        <h1 class="title">商品评价</h1>
        <ratingselect
          v-bind:select-type="selectType"
          v-bind:only-content="onlyContent"
          v-bind:desc="desc"
          v-bind:ratings="food.ratings"
        ></ratingselect>
      </div>

      <!--评价-->
      <div class="rating-wrapper">
        <!--有评论-->
        <ul v-show="food.ratings && food.ratings.length">
          <li v-show="needShow(rating.rateType,rating.text)"
              v-for="rating in food.ratings"
              class="rating-item">
            <!--用户信息-->
            <div class="user">
              <!--用户名-->
              <span class="name">{{rating.username}}</span>
              <!--用户头像-->
              <img :src="rating.avatar" width="12" height="12" class="avatar">
            </div>
            <!--评论时间-->
            <div class="time">{{rating.rateTime | formatDate}}</div>
            <!--评论内容-->
            <p class="text">
              <!--图标-->
              <span :class="{'icon-thumb_up':rating.rateType===0,
                             'icon-thumb_down':rating.rateType===1}">
              </span>
              {{rating.text}}
            </p>
          </li>
        </ul>
        <!--无评论-->
        <div class="no-rating"
             v-show="!food.ratings || !food.ratings.length">暂无评价
        </div>
      </div>

    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import Cartcontrol from 'components/cartcontrol/cartcontrol';
  import Split from 'components/split/split';
  import Ratingselect from 'components/ratingselect/ratingselect';
  import Vue from 'vue';
  import {formatDate} from 'common/js/date';

  // 全部评价
  const ALL = 2;

  export default {
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      };
    },
    methods: {
      show() {
        this.showFlag = true;
        this.selectType = 2;
        this.onlyContent = true;
        // 保证Dom是渲染的，高度能正确计算
        this.$nextTick(() => {
          // 有可能多次显示，判断是否已经调用
          if (!this.scroll) {
            this.scroll = new BScroll(this.$els.food, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
      },
      hide() {
        this.showFlag = false;
      },
      addFirst(event) {
        // 判断PC多次点击
        if (!event._constructed) {
          return;
        }
        Vue.set(this.food, 'count', 1);
        // 向父组件派发事件
        this.$dispatch('cart.add', event.target);
      },
      needShow(type, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return type === this.selectType;
        }
      }
    },
    components: {
      'cartcontrol': Cartcontrol,
      'split': Split,
      'ratingselect': Ratingselect
    },
    events: {
      'ratingtype.select'(type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      'content.toggle'(onlyContent) {
        this.onlyContent = onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      }
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"

  .food
    position fixed
    left 0
    top 0
    bottom 48px
    z-index 30
    width 100%
    background #fff
    &.move-transition
      transition all 0.2s linear
      transform translate3d(0,0,0)
    &.move-enter,&.move-leave
      transform translate3d(100%,0,0)
    .image-header
      position relative
      width 100%
      height 0
      padding-top 100%
      img
        position absolute
        top 0
        left 0
        width 100%
        height 100%
      .back
        position absolute
        top 10px
        left 0
        .icon-arrow_lift
          display block
          padding 18px
          font-size 20px
          color #fff
    .content
      padding 18px
      position relative
      .title
        line-height 14px
        margin-bottom 8px
        font-size 14px
        font-weight 700
        color rgb(7,17,27)
      .detail
        margin-bottom 18px
        line-height 10px
        height 10px
        font-size 0
        .sell-count,.rating
          font-size 10px
          color rgb(147,153,159)
        .sell-count
          margin-right 12px
      .price
        font-weight border
        line-height 24px
        .now
          margin-right 8px
          font-size 14px
          color rgb(250,20,20)
        .old
          text-decoration line-through
          font-size 10px
          color rgb(147,153,159)
      .cartcontrol-wrapper
        position absolute
        bottom 12px
        right 12px
      .buy
        position absolute
        right 18px
        bottom 18px
        z-index 10
        height 24px
        line-height 24px
        padding 0 12px
        box-sizing border-box
        font-size 10px
        border-radius 12px
        color #fff
        background rgb(0,160,220)
        &.fade-transition
          transition all 0.2s
          opacity 1
        &.fade-enter,&.fade-leave
          opacity 0
    .info
      padding 18px
      .title
        line-height 14px
        margin-bottom 6px
        font-size 14px
        color rgb(7,17,27)
      .text
        line-height 24px
        padding 0 8px
        font-size 12px
        color rgb(77,85,93)
    .rating
      padding-top 18px
      .title
        line-height 14px
        margin-left 18px
        font-size 14px
        color rgb(7,17,27)
    .rating-wrapper
      padding 0 18px
      .rating-item
        position relative
        padding 16px 0
        boder-1px(rgba(7,17,27,0.1))
        .user
          position absolute
          right 0
          top 16px
          font-size 0
          line-height 12px
          .name
            display inline-block
            margin-right 6px
            vertical-align top
            font-size 10px
            color rgb(147,153,159)
          .avatar
            border-radius 50%
        .time
          margin-bottom 6px
          line-height 12px
          font-size 10px
          color rgb(147,153,159)
        .text
          line-height 16px
          font-size 12px
          color rgb(7,17,27)
          .icon-thumb_up,.icon-thumb_down
            line-height 16px
            margin-right 4px
            font-size 12px
          .icon-thumb_up
            color rgb(0,160,220)
          .icon-thumb_down
            color rgb(7,17,27)
    .no-rating
      padding 16px 0
      font-size 12px
      color rgb(147,154,159)
</style>

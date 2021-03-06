<template>
  <div class="seller" v-el:seller>
    <div class="seller-content">

      <!--基本信息-->
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc">
          <star v-bind:size="36" v-bind:score="seller.score"></star>
          <span class="text">({{seller.ratingCount}})</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block">
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span> 元
            </div>
          </li>
          <li class="block">
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span> 元
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span> 分钟
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleFavorite">
          <span class="icon-favorite"
                :class="{'active':favorite===true}"
                transition="favorite"></span>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <!--间隙-->
      <split></split>
      <!--公告-->
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content-wrapper">
          <p class="content">{{seller.bulletin}}</p>
        </div>
        <!--优惠信息列表-->
        <ul class="supports" v-if="seller.supports">
          <li class="supports-item" v-for="item in seller.supports">
            <span class="icon" v-bind:class="classMap[seller.supports[$index].type]"></span>
            <span class="text">{{seller.supports[$index].description}}</span>
          </li>
        </ul>
      </div>
      <!--间隙-->
      <split></split>
      <!--商家实景-->
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" v-el:pic-wrapper>
          <ul class="pic-list" v-el:pic-list>
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic" width="120" height="90">
            </li>
          </ul>
        </div>
      </div>
      <!--间隙-->
      <split></split>
      <!--其他信息-->
      <div class="info">
        <h1 class="title">商家信息</h1>
        <ul>
          <li class="info-item" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>

    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Star from 'components/star/star';
  import Split from 'components/split/split';
  import BScroll from 'better-scroll';
  import {saveToLocal, loadFromLocal} from 'common/js/store';

  export default {
    data() {
      return {
        favorite: (() => {
          return loadFromLocal(this.seller.id, 'favorite', false);
        })()
      };
    },
    computed: {
      favoriteText() {
        return this.favorite ? '已收藏' : '收藏';
      }
    },
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      'star': Star,
      'split': Split
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    watch: {
      'seller'() {
        this._initScroll(this.$els.seller);
        this._initPics();
      }
    },
    ready() {
      this._initScroll(this.$els.seller);
      this._initPics();
    },
    methods: {
      _initScroll(els) {
        if (!this.scroll) {
          this.scroll = new BScroll(els, {
            click: true
          });
        } else {
          this.scroll.refresh();
        }
      },
      _initPics() {
        if (this.seller.pics) {
          let picWidth = 120;
          let margin = 6;
          let width = (picWidth + margin) * this.seller.pics.length - margin;
          this.$els.picList.style.width = width + 'px';
          this.$nextTick(() => {
            if (!this.picScroll) {
              this.picScroll = new BScroll(this.$els.picWrapper, {
                scrollX: true,
                eventPassthrough: 'vertical'
              });
            } else {
              this.picScroll.refresh();
            }
          });
        }
      },
      toggleFavorite(event) {
        if (!event._constructed) {
          return;
        }
        this.favorite = !this.favorite;
        saveToLocal(this.seller.id, 'favorite', this.favorite);
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"

  .seller
    position absolute
    top 174px
    bottom 0
    left 0
    width 100%
    overflow hidden
    .overview
      padding 18px
      .title
        font-size 14px
        margin-bottom 8px
        line-height 14px
        color rgb(7,17,27)
        font-size 14px
      .desc
        padding-bottom 18px
        font-size 0
        boder-1px(rgba(7,17,27,0.1))
        .star
          display inline-block
          vertical-align top
          margin-right 8px
        .text
          display inline-block
          vertical-align top
          margin-right 12px
          font-size 10px
          line-height 18px
          color rgb(77,85,93)
      .remark
        display flex
        padding-top 18px
        .block
          flex 1
          text-align center
          border-right 1px solid rgba(7,17,27,0.1)
          &:last-child
            border none
          h2
            margin-bottom 8px
            line-height 10px
            font-size 10px
            color rgb(147,153,159)
          .content
            line-height 24px
            font-size 10px
            color rgb(7,17,27)
            .stress
              font-size 24px
      .favorite
        position absolute
        top 14px
        right 11px
        text-align center
        width 50px
        .icon-favorite
          font-size 24px
          margin-bottom 4px
          line-height 24px
          display block
          color #d4d6d9
          opacity 1
          &.active
            color rgb(240,20,20)
          &.favorite-transition
            transition all 0.2s linear
          &.favorite-enter
            opacity 0
        .text
          line-height 10px
          font-size 10px
          color rgb(77,85,93)
    .bulletin
      padding 18px 18px 0 18px
      .title
        font-size 14px
        margin-bottom 8px
        line-height 14px
        color rgb(7,17,27)
        font-size 14px
      .content-wrapper
        padding 0 12px 16px 12px
        boder-1px(rgba(7,17,27,0.1))
        .content
          line-height 24px
          font-size 12px
          color rgb(240,20,20)
      .supports
        .supports-item
          padding  16px 12px
          boder-1px(rgba(7,17,27,0.1))
          font-size 0
          &:last-child
            boder-none()
        .icon
          display inline-block
          width 16px
          height 16px
          vertical-align top
          margin-right 6px
          background-size 16px 16px
          background-repeat no-repeat
          &.decrease
            bg-image('decrease_4')
          &.discount
            bg-image('discount_4')
          &.guarantee
            bg-image('guarantee_4')
          &.invoice
            bg-image('invoice_4')
          &.special
            bg-image('special_4')
        .text
          line-height 16px
          font-size 12px
          color rgb(7,17,27)
    .pics
      padding 18px
      .title
        font-size 14px
        margin-bottom 12px
        line-height 14px
        color rgb(7,17,27)
        font-size 14px
      .pic-wrapper
        width 100%
        overflow hidden
        white-space nowrap
        .pic-list
          font-size 0
          .pic-item
            display inline-block
            margin-right 6px
            width 120px
            height 90px
            &:last-child
              margin 0
    .info
      padding 18px 18px 0 18px
      color rgb(7,17,27)
      .title
        font-size 14px
        padding-bottom 12px
        line-height 14px
        font-size 14px
        boder-1px(rgba(7,17,27,0.1))
      .info-item
        padding 16px 12px
        line-height 16px
        font-size 12px
        boder-1px(rgba(7,17,27,0.1))
        &:last-child
          boder-none()
</style>

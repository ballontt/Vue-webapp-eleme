<template>
  <div class="ratingselect">
    <div class="rating-type">
      <span class="block positive" @click="select(2,$event)"
            v-bind:class="{'avtive':selectType===2}">
        {{desc.all}}
        <span class="count">{{ratings.length}}</span>
      </span>
      <span class="block positive"  @click="select(0,$event)"
            v-bind:class="{'avtive':selectType===0}">
        {{desc.positive}}
        <span class="count">{{positives.length}}</span>
      </span>
      <span class="block negative"  @click="select(1,$event)"
            v-bind:class="{'avtive':selectType===1}">
        {{desc.negative}}
        <span class="count">{{negative.length}}</span>
      </span>
    </div>
    <div @click="toggleContent($event)" class="switch" v-bind:class="{'on':onlyContent}">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  // 正面评价
  const POSITIVE = 0;
  // 负面评价
  const NEGATIVE = 1;
  // 全部评价
  const ALL = 2;

  export default {
    props: {
      // 评价，默认空数组
      ratings: {
        type: Array,
        default() {
          return [];
        }
      },
      // 选择评价类型
      selectType: {
        type: Number,
        default: ALL
      },
      // 只看有内容的评价
      onlyContent: {
        type: Boolean,
        default: false
      },
      // 描述
      desc: {
        type: Object,
        default() {
          return {
            all: '全部',
            positive: '满意',
            negative: '不满意'
          };
        }
      }
    },
    methods: {
      select(type, event) {
        if (!event._constructed) {
          return;
        }
        this.selectType = type;
        this.$dispatch('ratingtype.select', type);
      },
      toggleContent(event) {
        if (!event._constructed) {
          return;
        }
        this.onlyContent = !this.onlyContent;
        this.$dispatch('content.toggle', this.onlyContent);
      }
    },
    computed: {
      positives() {
        return this.ratings.filter((rating) => {
          return rating.rateType === POSITIVE;
        });
      },
      negative() {
        return this.ratings.filter((rating) => {
          return rating.rateType === NEGATIVE;
        });
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"

  .ratingselect
    .rating-type
      padding 18px 0
      margin 0 18px
      boder-1px(rgba(7,17,27,0.1))
      font-size 0
      .block
        display inline-block
        padding 8px 12px
        margin-right 8px
        border-radius 1px
        color rgb(77,85,93)
        font-size 12px
        line-height 16px
        &.avtive
          color #fff
        .count
          font-size 8px
          margin-left 2px
        &.positive
          background rgba(0,160,220,0.2)
          &.avtive
            background rgb(0,160,220)
        &.negative
          background rgba(77,85,93,0.2)
          &.avtive
            background rgb(77,85,93)
    .switch
      padding 12px 18px
      line-height 24px
      border-bottom 1px solid rgba(7,17,27,0.1)
      color rgb(147,153,159)
      font-size 0
      &.on
        .icon-check_circle
          color #00c850
      .icon-check_circle
        display inline-block
        vertical-align top
        margin-right 4px
        font-size 24px
      .text
        display inline-block
        vertical-align top
        font-size 12px
</style>

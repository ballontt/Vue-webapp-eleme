<template>
    <div class="star" v-bind:class="starType">
      <span class="star-item" v-for="itemClass in itemClasses" v-bind:class="itemClass" track-by="$index"></span>
    </div>
</template>

<script type="text/ecmascript-6">
  // 定义星级个数
  const LENGTH = 5;
  // 定义状态
  const CLS_ON = 'on';
  const CLS_HALF = 'half';
  const CLS_OFF = 'off';

  export default {
    // 传递数据
    props: {
      size: {    // 尺寸
        type: Number
      },
      score: {   // 评分
        type: Number
      }
    },
    // 计算属性
    computed: {
      starType() {
        // 返回Class名
        return 'star-' + this.size;
      },
      itemClasses () {
        // 定义数组
        let result = [];
        // 取整
        let score = Math.floor(this.score * 2) / 2;
        // 判断是否存在小数
        let hasDecimal = score % 1 !== 0;
        // 计算全星的个数
        let integer = Math.floor(score);
        // 全星
        for (let i = 0; i < integer; i++) {
          result.push(CLS_ON);
        }
        // 半星
        if (hasDecimal) {
          result.push(CLS_HALF);
        }
        // 补全
        while (result.length < LENGTH) {
          result.push(CLS_OFF);
        }
        return result;
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin"

  .star
    font-size 0
    .star-item
      display inline-block
      background-repeat no-repeat
    &.star-48
      .star-item
        width 20px
        height 20px
        margin-right 22px
        background-size 20px 20px
        &:last-child
          margin-right 0
        &.on
          bg-image('star48_on')
        &.half
          bg-image('star48_half')
        &.off
          bg-image('star48_off')
    &.star-36
      .star-item
        width 15px
        height 15px
        margin-right 8px
        background-size 15px 15px
        &:last-child
          margin-right 0
        &.on
          bg-image('star36_on')
        &.half
          bg-image('star36_half')
        &.off
          bg-image('star36_off')
    &.star-24
      .star-item
        width 10px
        height 10px
        margin-right 13px
        background-size 10px 10px
        &:last-child
          margin-right 0
        &.on
          bg-image('star24_on')
        &.half
          bg-image('star24_half')
        &.off
          bg-image('star24_off')

</style>

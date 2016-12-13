<template>

  <div class="goods">
    <!--左侧菜单-->
    <div class="menu-wrapper" v-el:menu-wrapper >
      <ul>
        <li v-for="item in goods" class="menu-item"
            :class="{'current':currentIndex===$index}"
            @click="selectMenu($index,$event)"
        >
          <span class="text boder-1px">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>
            {{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <!--右侧商品-->
    <div class="foods-wrapper" v-el:food-wrapper>
      <ul>
        <!--遍历存放每个分类-->
        <li v-for="item in goods" class="food-list food-list-hook">
          <!--分类的标题-->
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <!--遍历存放每个商品-->
            <li @click="selectFood(food,$event)" v-for="food in item.foods" class="food-item boder-1px">
              <!--商品图标-->
              <div class="icon">
                <img height="57px" width="57px" v-bind:src="food.icon">
              </div>
              <!--内容区-->
              <div class="content">
                <!--商品名称-->
                <h2 class="name">{{food.name}}</h2>
                <!--商品描述-->
                <p class="desc">{{food.description}}</p>
                <!--商品销量-->
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span><span>好评{{food.rating}}%</span>
                </div>
                <!--商品价格-->
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <!--添加删除控件,并把food传进子组件-->
                <div class="cartcontrol-wrapper">
                  <cartcontrol v-bind:food="food"></cartcontrol>
                </div>

              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <!--购物车-->
    <shopcart v-ref:shopcart
      v-bind:select-foods="selectFoods"
      v-bind:delivery-price="seller.deliveryPrice"
      v-bind:min-price="seller.minPrice"
    >
    </shopcart>
    <!--食物详情页-->
    <food :food="selectedFood" v-ref:food></food>
  </div>

</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import Shopcart from 'components/shopcart/shopcart';
  import Cartcontrol from 'components/cartcontrol/cartcontrol';
  import Food from 'components/food/food';

  const ERR_OK = 0;

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectedFood: {}
      };
    },
    created() {   // 生命周期钩子函数，实例化Vue调用
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
      // get请求
      this.$http.get('/api/goods').then((response) => {
        // 获得json
        response = response.body;
        // 判定状态码
        if (response.errno === ERR_OK) {
          this.goods = response.data;
          // Dom渲染结束后的回调
          this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight();
          });
        }
      });
    },
    methods: {
      // 滚动
      _initScroll() {
        // 使用better-scroll
        this.meunScroll = new BScroll(this.$els.menuWrapper, {
          click: true
        });
        this.foodsScroll = new BScroll(this.$els.foodWrapper, {
          click: true,
          probeType: 3
        });
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      // 计算高度
      _calculateHeight() {
        // 获得每个商品分类的Dom
        let foodList = this.$els.foodWrapper.getElementsByClassName('food-list-hook');
        // 初始化高度并推入数组
        let height = 0;
        this.listHeight.push(height);
        // 遍历每个商品分类，计算高度并推入数组
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
        }
      },
      // 点击左侧分类滚动
      selectMenu(index, event) {
        // 消除浏览器默认点击事件
        if (!event._constructed) {
          console.log(event);
          return;
        }
        let foodList = this.$els.foodWrapper.getElementsByClassName('food-list-hook');
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el, 300);
      },
      // 小球抛落
      _drop(target) {
        this.$refs.shopcart.drop(target);
      },
      // 商品详情页
      selectFood(food, event) {
        // 消除浏览器默认点击事件
        if (!event._constructed) {
          return;
        }
        this.selectedFood = food;
        this.$refs.food.show();
      }
    },
    computed: {
      // 区间高度检查
      currentIndex() {
        // 检查每个li
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          // 判断是否在区间内
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i;
          }
        }
        return 0;
      },
      // 已选中的食物
      selectFoods() {
        let Foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              Foods.push(food);
            }
          });
        });
        return Foods;
      }
    },
    components: {
      'shopcart': Shopcart,
      'cartcontrol': Cartcontrol,
      'food': Food
    },
    events: {
      'cart.add'(target) {
        this._drop(target);
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .goods
    display flex
    position absolute
    top 174px
    bottom 46px
    width 100%
    overflow hidden
    .menu-wrapper
      flex 0 0 80px
      width 80px
      background #f3f5f7
      .menu-item
        display table
        height 54px
        width 56px
        padding 0 12px
        line-height 14px
        &.current
          position relative
          margin-top -1px
          z-index 10
          background #fff
          font-weight 700
          .text
            boder-none()
        .icon
          display inline-block
          width 12px
          height 12px
          vertical-align top
          margin-right 2px
          background-size 12px 12px
          background-repeat no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')

        .text
          display table-cell
          width 56px
          vertical-align middle
          boder-1px(rgba(7,17,27,0.1))
          font-size 12px
    .foods-wrapper
      flex 1
      .title
        padding-left 14px
        height 26px
        line-height 26px
        border-left 2px solid #d9dde1
        font-size 12px
        color rgb(147,153,159)
        background #f3f5f7
      .food-item
        display flex
        margin 18px
        padding-bottom 18px
        boder-1px(rgba(7,17,27,0.1))
        &:last-child
          boder-none()
          margin-bottom 0
        .icon
          flex 0 0 57px
          margin-right 10px
        .content
          flex 1
          .name
            margin 2px 0 8px 0
            height 14px
            line-height 14px
            font-size 14px
            color rgb(7,17,27)
          .desc,.extra
            line-height 10px
            font-size 10px
            color rgb(147,153,159)
          .extra
            .count
              margin-right 12px
          .desc
            margin-bottom 8px
            line-height 12px
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
            right 0
            bottom 4px

</style>

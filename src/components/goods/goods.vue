<template>
    <div class="goods">
        <div class="menu-wrapper" ref="menuWrapper">
            <ul>
                <li v-for="(item,index) in goods" class="menu-item" :class="{'current': currentIndex==index}" @click="selectMenu(index)">
                    <span class="text border-1px">
                        <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>
                        {{item.name}}
                    </span>
                </li>
            </ul>
        </div>
        <div class="foods-wrapper" ref="foodsWrapper">
            <ul>
                <li v-for="item in goods" class="food-list food-list-hook">
                    <h1 class="title">{{item.name}}</h1>
                    <ul>
                        <li v-for="food in item.foods" class="food-item border-1px" @click="selectFood(food)">
                            <div class="icon">
                                <img :src="food.icon" alt="" width="57" height="57">
                            </div>
                            <div class="content">
                                <h2 class="name">{{food.name}}</h2>
                                <p class="desc">{{food.description}}</p>
                                <div class="extra">
                                    <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                                </div>
                                <div class="price">
                                    <span class="now">￥{{food.price}}</span><span v-show="food.oldPrice" class="old">￥{{food.oldPrice}}</span>
                                </div>
                                <div class="cartcontrol-wrapper">
                                    <cartcontrol :food="food" :updateFoodCount="updateFoodCount"></cartcontrol>
                                </div>
                            </div>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
        <shop-cart ref="shopcart" :selectFoods="selectFoods" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice" :updateFoodCount="updateFoodCount"></shop-cart>
        <food :food="selectedFood" ref="food"></food>
    </div>

</template>
<script>
import BScroll from 'better-scroll'
import shopCart from '@/components/shopcart/shopcart'
import cartcontrol from '@/components/cartcontrol/cartcontrol'
import food from '@/components/food/food'
export default {
    props:{
        seller: {
            type: Object
        }
    },
    components:{
        shopCart,
        cartcontrol,
        food
    },
    data(){
        return {
            goods: [],
            scrollY: 0,
            listHeight: [],
            tops: [],
            selectedFood: {}
        }
    },
    computed: {
        currentIndex(){
            for(let i=0;i<this.listHeight.length;i++){
                let height1 = this.listHeight[i];
                let height2 = this.listHeight[i+1];
                if(!height2 || (this.scrollY >= height1 && this.scrollY < height2)){
                    return i
                }
            }
            return 0;
        },
        selectFoods(){
            let foods = []
            this.goods.forEach((good) => {
                good.foods.forEach((food) => {
                    if(food.count){
                        foods.push(food)
                    }
                })
            })
            return foods
        }
    },
    mounted(){
        this.$http.get('./api2/goods').then((response) => {
            const result = response.data
            if(result.code == 0){
                this.goods = result.data
                this.$nextTick(() => {
                    this.initScroll()
                    this.initTops()
                })
            }
        })
        this.classMap = ['decrease','discount','guarantee','invoice','special']

    },
    methods:{
        initScroll(){
            this.menuScroll = new BScroll(this.$refs.menuWrapper,{
                click : true
            })
            this.foodsScroll = new BScroll(this.$refs.foodsWrapper,{
                click: true,
                probeType: 3
            })
            this.foodsScroll.on('scroll', (pos) => {
                this.scrollY = Math.abs(Math.round(pos.y))
            })
        },
        initTops(){
            var foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
            let height = 0;
            this.listHeight.push(height);
            for (let i = 0; i < foodList.length; i++) {
                let item = foodList[i];
                height += item.clientHeight;
                this.listHeight.push(height);
            }
            console.log(this.listHeight)
        },
        selectMenu(index){
            let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
            let el = foodList[index];
            this.foodsScroll.scrollToElement(el,300)
        },
        updateFoodCount(food,isFlag){
            if(isFlag){
                if(!food.count){
                    this.$set(food,'count',1)
                }else {
                    food.count++
                }
                this.$refs.shopcart.drop(event.target)
            }else{
                if(food.count){
                    food.count--
                }
            }
        },
        selectFood(food){
            this.selectedFood = food
            console.log(food)
            this.$refs.food.show()
        }
    }
}
</script>
<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixins"
.goods
    display: flex
    position: absolute
    width: 100%
    top: 174px
    bottom: 46px
    overflow: hidden
    .menu-wrapper
        flex: 0 0 80px
        width: 80px
        background: #f3f5f7
        /*overflow: auto*/
        .menu-item
            display: table
            height: 54px
            width: 56px
            line-height: 14px
            padding: 0 12px
            &.current
                position: relative
                z-index: 10
                margin-top: -1px
                background: #fff
                font-weight: 700
                .text
                    border-none()
            .icon
                display: inline-block
                width: 12px
                height: 12px
                background-size: 12px 12px
                background-repeat: no-repeat
                vertical-align: top
                &.decrease
                    bg-image: ('decrease_3')
                &.discount
                    bg-image: ('discount_3')
                &.guarantee
                    bg-image: ('guarantee_3')
                &.invoice
                    bg-image: ('invoice_3')
                &.special
                    bg-image: ('special_3')
            .text
                display: table-cell
                width: 56px
                vertical-align: middle
                font-size: 12px
                border-1px(rgba(7,17,27,.1))
    .foods-wrapper
        flex: 1
        .title
            padding-left: 14px
            height: 26px
            line-height: 26px
            border-right: 2px solid #d9dde1
            font-size: 12px
            color: rgb(147,153,159)
            background: #f3f5f7
        .food-item
            display: flex
            margin: 18px
            padding-bottom: 18px
            border-1px(rgba(7,17,27,.1))
            &:last-child
                border-none()
                margin-bottom: 0px
            .icon
                flex: 0 0 57px
                margin-right: 10px
            .content
                flex: 1
                .name
                    margin: 2px 0 8px 0
                    height: 14px
                    line-height: 14px
                    font-size: 14px
                    color: rgb(7,17,27)
                .desc, .extra
                    line-height: 10px
                    font-size: 10px
                    color: rgb(147,153,159)
                .desc
                    margin-bottom: 8px
                    line-height: 12px
                .extra
                    .count
                        margin-right: 12px
                .price
                    font-weight: 700
                    line-height: 24px
                    .now
                        margin-right: 8px
                        font-size: 14px
                        color: rgb(240,20,20)
                    .old
                        text-decoration: line-through
                        font-size: 10px
                        color: rgb(147,153,159)
                .cartcontrol-wrapper
                    position: absolute
                    right: 0px
                    bottom: 12px

</style>


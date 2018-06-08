<template>
    <div class="cartcontrol" ref="shopcart">
        <transition name="move">
            <div class="cart-decrease " v-show="food.count>0" @click="updateFoodCount(food,false)">
                <span class="icon-remove_circle_outline inner"></span>
            </div>
        </transition>
        <div class="cart-count"  v-show="food.count>0">{{food.count}}</div>
        <div class="cart-add icon-add_circle" @click="updateFoodCount(food,true)"></div>
    </div>
</template>

<script>
export default{
    props: {
        food: {
            type: Object
        },
        updateFoodCount: {
            type: Function
        }
    },
    created(){
    },
    methods:{
    }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
    .cartcontrol
        font-size: 0
        .cart-decrease
            display: inline-block
            padding: 6px
            &.move-enter-active, &.move-leave-active
                transition: all .4s linear
            &.move-enter, &.move-leave-active
                opacity: 0
                transform: translate3d(24px, 0, 0) rotate(180deg)
            .inner
                display: inline-block
                font-size: 24px
                line-height: 24px
                color: rgb(0,160,220)
        .cart-count
            display: inline-block
            width: 12px
            padding-top: 6px
            text-align: center
            vertical-align: top
            font-size: 10px
            color: rgb(147,153,159)
            line-height: 24px
        .cart-add
            display: inline-block
            padding: 6px
            font-size: 24px
            line-height: 24px
            color: rgb(0,160,220)
</style>
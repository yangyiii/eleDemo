<template>
<div>
    <div class="shopcart">
        <div class="content" @click="toggleList">
            <div class="content-left">
                <div class="logo-wrapper">
                    <div class="logo" :class="{'highlight':totalCount>0}">
                        <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
                    </div>
                    <div class="num" v-show="totalCount>0">{{totalCount}}</div>
                </div>
                <div class="price" :class="{'highlight':totalPrice>0}">￥{{totalPrice}}</div>
                <div class="desc">另需配送费￥{{deliveryPrice}}</div>
            </div>
            <div class="content-right" @click.stop="pay">
                <div class="pay" :class="payClass">
                    {{payDesc}}
                </div>
            </div>
        </div>
        <div class="ball-container">
            <transition name="drop" v-for="(ball, index) in balls" :key="index"
                        @before-enter="beforeDrop"
                        @enter="dropping"
                        @after-enter="afterDrop"
                        v-bind:css="false">
                <div class="ball" v-show="ball.show">
                    <div class="inner inner-hook"></div>
                </div>
            </transition>
        </div>
        <transition name="fold">
            <div class="shopcart-list" v-show="listShow">
                <div class="list-header">
                    <h1 class="title">购物车</h1>
                    <span class="empty" @click="empty">清空</span>
                </div>
                <div class="list-content" ref="listContent">
                    <ul>
                        <li class="food" v-for="food in selectFoods">
                            <span class="name">{{food.name}}</span>
                            <div class="price">
                                <span>￥{{food.price*food.count}}</span>
                            </div>
                            <div class="cartcontrol-wrapper">
                                <cartcontrol :food="food" :updateFoodCount="updateFoodCount"></cartcontrol>
                            </div>
                        </li>
                    </ul>
                </div>
            </div>
        </transition>
    </div>
    <transition name="fade">
        <div class="list-mask" v-show="listShow" @click="toggleList"></div>
    </transition>
</div>
</template>
<script>
import cartcontrol from '@/components/cartcontrol/cartcontrol'
import BScroll from 'better-scroll'
export default{
    props:{
        selectFoods: {
          type: Array,
            default(){
              return [

              ]
            }
        },
        deliveryPrice: {
            type: Number
        },
        minPrice:{
            type: Number
        },
        updateFoodCount: {
            type: Function
        }
    },
    data() {
        return {
            balls: [
                {show: false},
                {show: false},
                {show: false},
                {show: false},
                {show: false}
            ],
            dropBalls: [],
            fold: true
        }
    },
    components: {
        cartcontrol
    },
    computed:{
        totalPrice(){
            let total = 0;
            this.selectFoods.forEach((food) => {
                total += food.price*food.count
            })
            return total
        },
        totalCount(){
            let count = 0
            this.selectFoods.forEach((food) => {
                count += food.count
            })
            return count
        },
        payDesc(){
            if(this.totalPrice === 0){
                return '￥'+this.minPrice+"元起送"
            }else if(this.totalPrice < this.minPrice){
                let diff = this.minPrice - this.totalPrice;
                return '还差￥'+diff+"元起送"
            }else {
                return "去结算";
            }
        },
        payClass(){
            if(this.totalPrice < this.minPrice){
                return 'not-enough'
            }else{
                return 'enough'
            }
        },
        listShow(){
            if(!this.totalCount){
                this.fold = true
                return false
            }
            let show = !this.fold
            if(show){
                this.$nextTick(()=>{
                    if(!this.scroll){
                        console.log(1)
                        this.scroll = new BScroll(this.$refs.listContent,{
                            click: true
                        })
                    }else{
                        this.scroll.refresh()
                    }

                })
            }
            return show
        }
    },
    methods: {
        drop(startEl){
            // 找到隐藏的小球
            const ball = this.balls.find(ball => !ball.show)
            // 显示它
            if(ball) {
                ball.show = true
                ball.startEl = startEl
                this.dropBalls.push(ball)
            }
        },
        beforeDrop(el){
            // 找到对应的ball
            const ball = this.dropBalls.shift() // 移除第一个

            var offsetY = 0
            var offsetX = 0
            // 计算
            const {left, top} = ball.startEl.getBoundingClientRect()
            const elLeft = 32
            const elBottom = 22
            offsetX = left - elLeft
            offsetY = -(window.innerHeight - top - elBottom)

            // 指定transform样式
            el.style.transform = `translate3d(0, ${offsetY}px, 0)`
            el.children[0].style.transform = `translate3d(${offsetX}px, 0, 0)`

            // 保存ball
            el.ball = ball
        },
        dropping(el){
            // 强制重排重绘
            var temp = el.clientHeight
            console.log('dropping()', Date.now())
            // 异步指定
            this.$nextTick(() => {
                // 指定transform样式
                el.style.transform = 'translate3d(0, 0, 0)'
                el.children[0].style.transform = 'translate3d(0, 0, 0)'
            })
        },
        afterDrop(el){
            // 必须延迟更新状态
            setTimeout(() => {
                el.ball.show = false
            }, 400)
        },
        toggleList(){
            if(!this.totalCount){
                return
            }
            this.fold = !this.fold
        },
        empty(){
            this.selectFoods.forEach((food) => {
                food.count = 0
            })
        },
        pay(){
            if(this.totalPrice < this.minPrice){
                return
            }
            window.alert(`支付${this.totalPrice}元`)
        }
    }
}
</script>
<style lang="stylus" rel="stylesheet/stylus">
 @import "../../common/stylus/mixins"
.shopcart
    position: fixed
    left: 0px
    bottom: 0px
    z-index: 50
    width: 100%
    height: 48px
    .content
        display: flex
        background: #141d27
        font-size: 0
        color: rgba(255,255,255,.4)
        .content-left
            flex: 1
            .logo-wrapper
                display: inline-block
                position: relative
                top: -10px
                margin: 0 12px
                padding: 6px
                width: 56px
                height: 56px
                box-sizing: border-box
                vertical-align: top
                border-radius: 50%
                background: #141d27
                .logo
                    width: 100%
                    height: 100%
                    border-radius: 50%
                    background: #2b343c
                    text-align: center
                    &.highlight
                        background: rgb(0,160,220)
                    .icon-shopping_cart
                        font-size: 24px
                        color: #80858a
                        line-height: 44px
                        &.highlight
                            color: #fff
                .num
                    position: absolute
                    top: 0
                    right: 0
                    width: 24px
                    height: 16px
                    line-height: 16px
                    text-align: center
                    border-radius: 16px
                    font-size: 9px
                    font-weight: 700
                    color: #fff
                    background: rgb(240,20,20)
                    box-shadow: 0 4px 8px 0 rgba(0,0,0,.4)
            .price
                display: inline-block
                vertical-align: top
                margin-top: 12px
                padding-right: 12px
                line-height: 24px
                box-sizing: border-box
                border-right: 1px solid rgba(255,255,255,0.1)
                font-size: 16px
                font-weight: 700
                &.highlight
                    color: #fff
            .desc
                display: inline-block
                vertical-align: top
                line-height: 24px
                margin: 12px 0 0 12px
                font-size: 10px

        .content-right
            flex: 0 0 105px
            width: 105px
            .pay
                height: 48px
                line-height: 48px
                text-align: center
                font-size: 12px
                font-weight: 700
                &.not-enough
                    background: #2b333b
                &.enough
                    background: #00b43c
                    color: #fff
    .ball-container
        .ball
            position: fixed
            left: 32px
            bottom: 22px
            z-index: 200
            transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
            .inner
                width: 16px
                height: 16px
                border-radius: 50%
                background: rgb(0, 160, 220)
                transition: all 0.4s linear
    .shopcart-list
        position: absolute
        top: 0px
        left: 0px
        z-index: -1
        width: 100%
        transform: translate3d(0, -100%, 0) /*显示时的状态*/
        &.fold-enter-active, &.fold-leave-active
            transition: all 0.5s
        &.fold-enter, &.fold-leave-active
            transform: translate3d(0, 0, 0)
        .list-header
            height: 40px
            line-height: 40px
            padding: 0 18px
            background: #f3f5f7
            border-bottom: 1px solid rgba(7,17,27,.1)
            .title
                float: left
                font-size: 14px
                color: rgb(7,17,27)
            .empty
                float: right
                font-size: 12px
                color: RGB(0,160,220)
        .list-content
            padding: 0 18px
            max-height: 217px
            background: #fff
            overflow: hidden
            .food
                position: relative
                padding: 12px 0
                box-sizing: border-box
                border-1px(rgba(7,17,27,.1))
                .name
                    line-height: 24px
                    font-size: 14px
                    color: rgb(7,17,27)
                .price
                    position: absolute
                    right: 90px
                    bottom: 12px
                    line-height: 24px
                    font-size: 14px
                    font-weight: 700
                    color: rgb(240,20,20)
                .cartcontrol-wrapper
                    position: absolute
                    right: 0px
                    bottom: 6px
.list-mask
    position: fixed
    top: 0px
    left: 0px
    width: 100%
    height: 100%
    z-index: 40
    backdrop-filter: blur(10px)
    opacity: 1
    background: rgba(7, 17, 27, 0.6)
    &.fade-enter-active, &.fade-leave-active
        transition: all 0.5s
    &.fade-enter, &.fade-leave-active
        opacity: 0
        background: rgba(7, 17, 27, 0)
</style>
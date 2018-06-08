<template>
    <div class="header">
        <div class="content-wrapper">
            <div class="avatar">
                <img :src="seller.avatar" alt="" width="64" height="64">
            </div>
            <div class="content">
                <div class="title">
                    <span class="brand"></span>
                    <span class="name">{{seller.name}}</span>
                </div>
                <div class="description">
                    {{seller.description}}/{{seller.deliveryTime}}分钟送达
                </div>
                <div class="support" v-if="seller.supports">
                    <span class="icon" :class="classMap[seller.supports[0].type]"></span>
                    <span class="text">{{seller.supports[0].description}}</span>
                </div>
            </div>
            <div class="support-count" v-if="seller.supports" @click="showDetail(true)">
                <span class="count">{{seller.supports.length}}个</span>
                <i class="icon-keyboard_arrow_right"></i>
            </div>
        </div>
        <div class="bullestin-wrapper">
            <span class="bullestin-title"></span>
            <span class="bullestin-text">{{seller.bulletin}}</span>
            <i class="icon-keyboard_arrow_right"></i>
        </div>
        <div class="background">
            <img :src="seller.avatar" alt="" width="100%" height="100%">
        </div>
        <transition name="fade">
            <div class="detail" v-show="detailShow">
                <div class="detail-wrapper clearfix">
                    <div class="detail-main">
                        <h1 class="name">{{seller.name}}</h1>
                        <div class="star-wrapper">
                            <star :size="48" :score="seller.score"></star>
                        </div>
                        <div class="title">
                            <div class="line"></div>
                            <div class="text">优惠信息</div>
                            <div class="line"></div>
                        </div>
                        <ul v-if="seller.supports" class="supports">
                            <li class="support-item" v-for="(item,index) in seller.supports">
                                <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                                <span class="text">{{seller.supports[index].description}}</span>
                            </li>
                        </ul>
                        <div class="title">
                            <div class="line"></div>
                            <div class="text">商家公告</div>
                            <div class="line"></div>
                        </div>
                        <div class="bulletin">
                            <p class="content">{{seller.bulletin}}</p>
                        </div>
                    </div>
                </div>
                <div class="detail-close" @click="showDetail(false)">
                    <i class="icon-close"></i>
                </div>
            </div>
        </transition>
    </div>
</template>
<script>
import star from '@/components/star/star'
export default {
    props:{
        seller: {
            type: Object
        }
    },
    data(){
        return{
            detailShow: false
        }
    },
  components: {
        star
  },
    methods:{
        showDetail(flag){
            this.detailShow = flag
        }
    },
    created(){
        this.classMap = ['decrease','discount','guarantee','invoice','special']
    },
    mounted(){

    }
}
</script>
<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixins"

.header
    position: relative
    color: #fff
    background: rgba(7,17,27,.5)
    overflow: hidden
    .content-wrapper
        position: relative
        padding: 24px 12px 19px 24px
        font-size: 0
        .avatar
            display: inline-block
            vertical-align: top
            img
                border-radius: 2px
        .content
            display: inline-block
            margin-left: 16px
            .title
                margin: 2px 0 0 0
                .brand
                    display: inline-block
                    width: 30px
                    height: 18px
                    vertical-align: top
                    bg-image: ('brand')
                    background-size: 30px 18px
                    background-repeat: no-repeat
                .name
                    margin-left: 6px
                    font-size: 16px
                    font-weight: bold
                    line-height: 18px
            .description
                margin-bottom: 10px
                margin-top: 5px
                line-height: 12px
                font-size: 12px
            .support
                .icon
                    display: inline-block
                    width: 12px
                    height: 12px
                    margin-right: 4px
                    background-size: 12px 12px
                    background-repeat: no-repeat
                    vertical-align: top
                    &.decrease
                        bg-image: ('decrease_4')
                    &.discount
                        bg-image: ('discount_4')
                    &.guarantee
                        bg-image: ('guarantee_4')
                    &.invoice
                        bg-image: ('invoice_4')
                    &.special
                        bg-image: ('special_4')
                .text
                    font-size: 10px
                    line-height: 12px
        .support-count
            position: absolute
            right: 12px
            bottom: 18px
            padding: 0 8px
            height: 24px
            line-height: 24px
            border-radius: 14px
            background-color: rgba(0,0,0,.2)
            text-align: center
            font-size: 10px
            .count
                vertical-align: top
            .icon-keyboard_arrow_right
                margin-left: 2px
                line-height: 24px
    .bullestin-wrapper
        position: relative
        height: 28px
        line-height: 28px
        padding: 0 24px 0 12px
        white-space: nowrap
        font-size: 10px
        overflow: hidden
        text-overflow: ellipsis
        background: rgba(7,17,27,.2)
        .bullestin-title
            display: inline-block
            width: 22px
            height: 12px
            vertical-align: top
            margin-top: 8px
            bg-image: ('bulletin')
            background-size: 22px 12px
            background-repeat: no-repeat
        .bullestin-text
            vertical-align: top
            margin: 0 4px
        .icon-keyboard_arrow_right
            position: absolute
            font-size: 10px
            right: 12px
            top: 9px
    .background
        position: absolute
        width: 100%
        height: 100%
        top: 0
        left: 0
        z-index: -1
        filter: blur(10px)
    .detail
        position: fixed
        top: 0
        left: 0
        width: 100%
        height: 100%
        z-index: 100
        overflow: auto
        transition: all 0.5s
        background rgba(7,17,27,0.8)
        &.fade-enter-active, &.fade-leave-active
            transition: opacity .3s linear
        &.fade-enter,&.fade-leave-to
            opacity: 0
        .detail-wrapper
            min-height: 100%
            width: 100%
            .detail-main
                margin-top: 64px
                padding-bottom: 64px
                .name
                    line-height: 16px
                    text-align: center
                    font-size: 16px
                    font-weight: 700
                .star-wrapper
                    margin-top: 18px
                    padding: 2px 0
                    text-align: center
                .title
                    display: flex
                    width: 80%
                    margin: 30px auto 24px
                    .line
                        flex: 1
                        position: relative
                        top: -6px
                        border-bottom: 1px solid rgba(255,255,255,.2)
                    .text
                        padding: 0 12px
                        font-size: 14px
                        font-weight: 700
                .supports
                    width: 80%
                    margin: 0 auto
                    .support-item
                        padding: 0 12px
                        margin-bottom: 12px
                        font-size: 0
                        &:last-child
                            margin-bottom: 0px
                        .icon
                            display: inline-block
                            width: 16px
                            height: 16px
                            vertical-align: top
                            margin-right: 6px
                            background-size: 16px 16px
                            background-repeat: no-repeat
                            &.decrease
                                bg-image: ('decrease_2')
                            &.discount
                                bg-image: ('discount_2')
                            &.guarantee
                                bg-image: ('guarantee_2')
                            &.invoice
                                bg-image: ('invoice_2')
                            &.special
                                bg-image: ('special_2')
                        .text
                            line-height: 16px
                            font-size: 12px
                .bulletin
                    width: 80%
                    margin: 0 auto
                    .content
                        padding: 0 12px
                        line-height: 24px
                        font-size: 12px

        .detail-close
            position: relative
            width: 32px
            height: 32px
            margin: -64px auto 0
            clear:both
            font-size: 32px

</style>


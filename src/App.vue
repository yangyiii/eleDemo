<template>
  <div id="app">
    <ele-header :seller="seller"></ele-header>
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">评价</router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">商家</router-link>
      </div>
    </div>
    <router-view :seller="seller"></router-view>
  </div>
</template>

<script>
import header from './components/header/header'
export default {
  name: 'App',
  data(){
    return {
      seller:{}
    }
  },
  components: {
    'ele-header': header
  },
  created(){
    this.$http.get('./api2/seller').then((response)=>{
      const result = response.data;
      console.log(result)
      if(result.code == 0){
        this.seller = result.data
      }
    })
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import './common/stylus/mixins.styl'

  .tab
    display: flex
    width: 100%
    height: 40px
    line-height: 40px
    border-1px(rgba(7, 17, 27, 0.1))
    .tab-item
      flex: 1
      text-align: center
      & > a
        display: block
        font-size: 14px
        color: rgb(77, 85, 93)
        &.active
          color: rgb(240, 20, 20)

</style>


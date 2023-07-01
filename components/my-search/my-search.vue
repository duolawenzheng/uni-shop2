<template> 
    <!-- 自定义的搜索组件 -->
    <view class="my-search-container" :style="{'background-color':bgcolor}" @click="searchBoxHandler">
      <view class="my-search-box" :style="{'border-radius':radious+'px'}"> 
        <uni-icons type="search" size="17"></uni-icons>
        <!-- </uni> -->
        <text class="placeholder">搜索xxx</text>
      </view>
    </view>
</template>    

<script> 
  export default {
    name:"my-search",
    props:{
      // 背景颜色
      bgcolor:{
        type:String,
        default:'#C00000'
      },
      // 圆角尺寸
      radious:{
        type:Number,
        default:18
      }
    },
    data() { 
      return {
        
      };
    },
    methods:{
      searchBoxHandler(){
        // console.log("okkkkkkk");
        this.$emit('click')
      }
    }
  }
</script>

<style lang="scss">
  .my-search-container{
    height: 50px;
    // background-color: #C00000;
    display: flex;
    align-items: center;
    padding: 0 10px;
    .my-search-box{
      height: 36px;
      background-color: #FFFFFF;
      // border-radius: 18px;
      width: 100%;
      
      display: flex;
      align-items: center;
      justify-content: center;
      
      .placeholder{
        font-size: 15px;
        margin-left: 5px;
      }
    }
  }

</style>
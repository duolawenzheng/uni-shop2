<template>
  <view>
    <!-- 搜索组件 -->
    <view class="search-box">
      <my-search @click="gotoSearch"></my-search>
    </view>
    
    <!-- 使用usw生成轮播图区域的代码片段 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <!-- item是每次循环的项 i是循环的索引 -->
      <swiper-item v-for="(item,i) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id='+item.goods_id">
          <image :src="item.image_src" ></image>   
        </navigator>
      </swiper-item>
    </swiper>
    <!-- 导航栏区域 -->
    <view class="nav-list">
      <view class="nav-item" v-for="(item,i) in navList" :key="i"
       @click="navClickHandler(item)">
        <image :src="item.image_src" class="nav_img"></image>
      </view>
    </view>
    <!-- 楼层区域 -->
   <view class="floor-list">
     <view class="floor-item" v-for="(item,i) in floorList" :key="i">
       <!-- 楼层的标题 -->
       <image :src="item.floor_title.image_src" class="floor-title"></image>
       
       <!-- 楼层的图片 -->
      <view class="floor-img-box">
        <navigator class="img-left-box" :url="item.product_list[0].url">
          <image :src="item.product_list[0].image_src" :style="{width:item.product_list[0].image_width+'rpx'}" mode="widthFix"></image>
        </navigator>
        
        <view class="right-img-box">
          <navigator class="right-img-item"  v-for="(item2,i2) in item.product_list" :key="i2" v-if="i2!=0" :url="item2.url">
            <image :src="item2.image_src" :style="{width:item2.image_width+'rpx'}" mode="widthFix"></image>         
          </navigator>
        </view>
        
        
      </view>       
     </view>
   </view>
    
    </view>
 
</template> 

<script>
  export default {
    data() {
      return {
        //轮播图数据列表
        swiperList:[],
        // 分类导航数据
        navList:[],
        // 获取楼层数据
        floorList:[]
      };
    },
    onLoad() {
      // 调用方法获取轮播图数据
      this.getSwiperList()
      this.getNavList()
      this.getFloorList()
    },
    methods:{
      async getSwiperList(){
      const {data:res} = await uni.$http.get('api/public/v1/home/swiperdata')
      console.log(res);
      // 請求失敗
      if(res.meta.status!=200){
        return uni.$showMsg()
        // return uni.showToast({
        //   title:"数据请求失败",
        //   duration:1500,
        //   icon:'none'
        // })
      }
      this.swiperList=res.message;
      uni.$showMsg('数据请求成功！')
      } , 
       
      async getNavList(){
        const {data:res}=await uni.$http.get('api/public/v1/home/catitems')
        if(res.meta.status!=200) return uni.$showMsg()
        this.navList=res.message;
        // uni.$showMsg('数据请求成功！')
        // console.log(res);
      },
      navClickHandler(item){
        // console.log(item);
        if(item.name=="分类"){
          uni.switchTab({
            url:"/pages/cate/cate"
          })
        }  
      },
      
       async getFloorList(){
        const {data:res} =await uni.$http.get('api/public/v1/home/floordata')
        if(res.meta.status!=200) return uni.$showMsg()
        // 对数据进行处理
        res.message.forEach(floor=>{
          floor.product_list.forEach(prod=>{
            prod.url='/subpkg/goods_list/goods_list?'+prod.navigator_url.split('?')[1]
          })
        }) 
        
        this.floorList=res.message;
        },
        gotoSearch(){
          uni.navigateTo({
            url:'/subpkg/search/search'
          })
        }
    }
  }
</script>

<style lang="scss">
  // 标签选择器
  swiper{
    height: 330rpx;
    .swiper-item,
    image{
      width: 100%;
      height: 100%;
    }
  } 
  
  .nav-list{
    display: flex;
    justify-content: space-around;
    margin: 15px 0;

    .nav_img{
      width: 128rpx;
      height: 140rpx;
  }
}
  .floor-title{
    width: 100%;
    height: 60rpx;
  }
  .right-img-box{
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }
  .floor-img-box{
    display: flex;
    padding: 10rpx;
  }
  .search-box{
    position: sticky;
    top: 0;
    z-index: 999;
  }
</style>

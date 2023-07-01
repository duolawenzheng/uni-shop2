<template>
  <view>
     <view class="goods-list">
       <view v-for="(goods,i) in goodsList" :key="i" @click="gotoDetail(goods)">
          <my-goods :goods="goods"></my-goods>
       </view>
     </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 请求参数的对象
        queryObj:{
          query:'',
          cid:'',
          pagenum:1,
          pagesize:10
        },
        // 商品列表的数据
        goodsList:[],
        // 总数量，用来实现分页
        total:0,
        // 节流阀
        isloading:false
        
      };
    },
    // onLoad函数的参数是跳转页面时候所包含的参数
    onLoad(options) {
      this.queryObj.cid=options.cid||'' 
      this.queryObj.query=options.query||''
      // console.log(options);
      this.getGoodsList()
    },
    methods:{
      // 获取商品列表数据的方法
      async getGoodsList(cb){
        // 打开节流阀
        this.isloading=true
        // 逗号后是页面参数
        const{data:res} =await uni.$http.get('api/public/v1/goods/search',this.queryObj)
        // 关闭节流阀
        this.isloading=false
        
        // 只要数据请求完毕 立即需要调用cb回调函数
        cb&&cb()
        
        if(res.meta.status!=200) return uni.$showMsg()
        this.goodsList=[...this.goodsList,...res.message.goods]
        this.total=res.message.total
    },
    gotoDetail(item){
      uni.navigateTo({
        url:'/subpkg/goods_detail/goods_detail?goods_id='+item.goods_id
      })
    }
  },
  onReachBottom() {
    if(this.queryObj.pagenum*this.queryObj.pagesize>=this.total) return uni.$showMsg('数据加载完毕')
    if(this.isloading){
      return
    }
    // 让页码自增+1
    this.queryObj.pagenum=this.queryObj.pagenum+1;
    this.getGoodsList()
  },
  // 下拉刷新的事件
  onPullDownRefresh() {
    // 重置关键数据
    this.queryObj.pagenum=1
    this.total=0
    this.isloading=false
    this.goodsList=[]
    
    // 重现发起请求
    this.getGoodsList(()=>uni.stopPullDownRefresh())
  }
}
</script> 

<style lang="scss">
  

</style>

<template>
  <view>
    <view class="search-box">
      <uni-search-bar placeholder="请输入搜索内容"  @input="input" :radius="20" cancelButton="none"></uni-search-bar>
    </view> 
    <view class="sugg-list" v-if="searchResult.length!==0">
      <view class="sugg-item" v-for="(item,i) in searchResult" :key="i" @click="gotoDetail(item)">
        <view class="goods-name">{{item.goods_name}}</view>
        <uni-icons type="arrowright" size="16"></uni-icons>
      </view>
    </view>
    
    <!-- 搜索历史区域 -->
    <view class="history-box" v-else>
      <!-- 标题区域 -->
      <view class="history-title">
        <text>搜索历史</text>
        <uni-icons type="trash" size="17" @click="clean"></uni-icons>
      </view>
      <!-- 列表区域 -->
      <view class="history-list">
        <uni-tag :text="item" v-for="(item,i) in historys" :key="i" @click="gotoGoodList(item)"></uni-tag>
      </view>
    </view>
  </view>
</template>
 
<script>
  export default {
    data() {
      return {
        timer:null,
        kw:'',
        // 搜索的结果列表
        searchResult:[],
        // 搜索关键字的历史记录
        historyList:[]
      }; 
    },
    onLoad() {
      this.historyList = JSON.parse(uni.getStorageSync('kw')||'[]')
      
    },
    methods:{
      input(e){
        // console.log(e);
        clearTimeout(this.timer)
        
        this.timer=setTimeout(()=>{
          this.kw=e;
          this.getSearchList()
        },500)
      },
      async getSearchList(){
        if(this.kw.length==0){
          this.searchResult=[]
          return
        }
        const {data:res} = await uni.$http.get('api/public/v1/goods/qsearch' ,{query:this.kw})
        if(res.meta.status!=200) return uni.$showMsg()
        this.searchResult=res.message
        // 调用方法将搜索历史封进历史列表
        this.saveSearchHistory()
        
      },
      gotoDetail(item){
        // console.log(item.goods_name);
        uni.navigateTo({
          url:'/subpkg/goods_detail/goods_detail?goods_id='+item.goods_id
        })
      },
      saveSearchHistory(){
        // this.historyList.push(this.kw)
        // 将array数组转为set数组
        const set=new Set(this.historyList)
        set.delete(this.kw)
        set.add(this.kw)
        this.historyList=Array.from(set)
        // 对搜索历史数据进行持久化存储
        uni.setStorageSync('kw',JSON.stringify(this.historyList))
      },
      clean(){
        this.historyList=[]
        uni.setStorageSync('kw','[]')
      },
      gotoGoodList(kw){
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?query='+kw
        })
      }
    },
    // 新定义一个计算属性historys  将原先数组反转后
    computed:{
      historys(){
        return[...this.historyList].reverse()
      }
    }
  }
</script>

<style lang="scss">
.search-box{
  position: sticky;
  top: 0;
  z-index: 999;
  // display: flex;
  // width: 100%;
}
  .sugg-list{
    padding: 0px 5px;
    
    .sugg-item{
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 12px;
      padding: 13px 0;
      border-bottom: 1px solid #efefef;
      
      .goods-name{
        // 文本不允许换行
        white-space: nowrap;
        // 溢出部分隐藏
        overflow: hidden;
        // 文本溢出后使用...代替
        text-overflow: ellipsis;
        margin-right: 3px;
      }
    }
  }
  .history-box{
    padding: 0 5px;
    
    .history-title{
      display: flex;
      justify-content: space-between;
      height: 40px;
      align-items: center;
      font-size: 13px;
      border-bottom: 1px solid #efefef;
     } 
        .history-list{
          display: flex;
          flex-wrap: wrap;
        
          .uni-tag{
            margin-top: 5px;
            margin-right: 5px;
          }
    }
  }
    
</style>

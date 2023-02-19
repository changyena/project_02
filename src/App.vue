<template>
  <div class="app-container">
    <!-- Header头部区域 -->
    <Header title="购物车案例"></Header>
    
    <!-- 循环渲染每一个商品的信息 -->
    <Goods 
    v-for="item in list" 
    :key="item.id" 
    :id="item.id"
    :title="item.goods_name" 
    :pic="item.goods_img" 
    :price="item.goods_price"
    :state="item.goods_state"
    :count="item.goods_count"
    @state-change="getNewState"
    
    
    ></Goods>

    <!-- footer区域 -->
    <Footer :isfull="fullState" @full-change="getFullState" :amount="amt" :all="total"></Footer>
   
  </div>
</template>

<script>
import axios from 'axios'
import Header from '@/components/Header/Header.vue'
import Goods from '@/components/Goods/Goods.vue'
import Footer from '@/components/Footer/Footer.vue'
import bus from '@/components/eventBus.js'




export default {

  data(){
    return{
      // 用来存储购物车的列表数据 默认为空数组
      list:[]
    }
  },
  components:{
    Header,
    Goods,
    Footer
   
  },
  methods: {
    // 封装请求列表数据的方法
   async initCartList(){
    const{data:res} =await axios.get('https://www.escook.cn/api/cart')
    if(res.status ===200){
      this.list = res.list
    }
    },
  getNewState(e){
    // console.log(val);
    this.list.some(item=>{
      if(item.id===e.id){
        item.goods_state=e.value
        return true
      }
    })
  },
  getFullState(val){
    // console.log(val);
    this.list.forEach(item=>(item.goods_state=val))

  }


  },
  computed:{
    // 动态计算出全选的状态是true还是false
    fullState(){
     return this.list.every(item=>item.goods_state)

    },
    // 已勾选商品的总价格
    amt(){
     return this.list.filter(item=>item.goods_state).reduce((total,item)=>{
      return  total+=item.goods_price*item.goods_count
      },0)

    },
    // 已勾选商品的总数量
    total(){
     return this.list.filter(item=>item.goods_state).reduce((t,item)=>{
       return t+=item.goods_count
      },0)

    }

  },
  created(){
    this.initCartList()
    bus.$on('share',(val)=>{
      // console.log('接收到了值',val);
      this.list.some(item=>{
        if(item.id===val.id){
          item.goods_count=val.value
          return true
        }
      })
    })
   
  },
  
}
</script>

<style lang="less" scoped>
.app-container {
  padding-top: 45px;
  padding-bottom: 50px;
}
</style>

<template>
  <div class="post">
     <el-row>
        <el-col :span='7' class="post-order" id="order-list">

            <el-tabs>
               <el-tab-pane label="点餐">
                  <el-table :data="tabledata" border style="width:100%">
                      <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                      <el-table-column prop="count" label="数量" width="50"></el-table-column>
                      <el-table-column prop="price" label="金额" width="70"></el-table-column>
                      <el-table-column label="操作" width="100" fixed="right">
                        <template  slot-scope="scope">
                            <el-button type="text" size='small' @click="delSingGoods(scope.row)">删除</el-button>
                            <el-button type="text" size='small' @click="addOrderList(scope.row)">增加</el-button>
                        </template>
                      </el-table-column>
                  </el-table>
                  <div class="totaldiv">
                     <small> 数量：</small>{{totalCount}}    <small> 金额：</small>{{totalMoney}}元     

                  </div>
                  <div class="btn-3-div">
                      <el-button type="warning">挂单</el-button>
                      <el-button type="danger" @click="delAllGoods">删除</el-button>
                      <el-button type="success" @click="checkout">结账</el-button>
                  </div>
               </el-tab-pane>
               <el-tab-pane label="挂单">
                 挂单
               </el-tab-pane>
               <el-tab-pane label="外卖">
                 外卖
               </el-tab-pane>
            </el-tabs>



        </el-col>

        <el-col :span='17'>
          <div class="often-goods">
              <div class="title">常用商品</div>
              <div class="often-goods-list">
                <ul>
                  <li :key="goods.goodsId" v-for="goods in oftenGoods" @click="addOrderList(goods)">
                    <span>{{goods.goodsName}}</span>
                    <span class="o-price">￥{{goods.price}}</span>
                  </li>
                </ul>
              </div>
          </div>
          <div class="goods-type">
            <el-tabs>
                <el-tab-pane label="汉堡">
                  <div>
                      <ul class="cookList">
                        <li :key="goods.goodsId" v-for="goods in type0Goods" @click="addOrderList(goods)">
                            <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                            <span class="foodName">{{goods.goodsName}}</span>
                            <span class="foodPrice">￥{{goods.price}}</span>
                        </li>
                      </ul>
                  </div>
                </el-tab-pane>
                <el-tab-pane label="小食">
                   <div>
                      <ul class="cookList">
                        <li :key="goods.goodsId" v-for="goods in type1Goods" @click="addOrderList(goods)">
                            <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                            <span class="foodName">{{goods.goodsName}}</span>
                            <span class="foodPrice">￥{{goods.price}}</span>
                        </li>
                      </ul>
                  </div>
                </el-tab-pane>
                <el-tab-pane label="饮料">
                   <div>
                      <ul class="cookList">
                        <li :key="goods.goodsId" v-for="goods in type2Goods" @click="addOrderList(goods)">
                            <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                            <span class="foodName">{{goods.goodsName}}</span>
                            <span class="foodPrice">￥{{goods.price}}</span>
                        </li>
                      </ul>
                  </div>
                </el-tab-pane>
                <el-tab-pane label="套餐">
                   <div>
                      <ul class="cookList">
                        <li :key="goods.goodsId" v-for="goods in type3Goods" @click="addOrderList(goods)">
                            <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                            <span class="foodName">{{goods.goodsName}}</span>
                            <span class="foodPrice">￥{{goods.price}}</span>
                        </li>
                      </ul>
                  </div>
                </el-tab-pane>
            </el-tabs>
          </div>
        </el-col>
     </el-row>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'pos',
  data(){
    return {
        tabledata:[],
        oftenGoods:[],
        type0Goods:[],
        type1Goods:[],
        type2Goods:[],
        type3Goods:[],
        totalMoney:0,
        totalCount:0
    }
  },
  created:function(){
    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods')
    .then(reponse=>{
      // console.log(reponse)
      this.oftenGoods=reponse.data;
    })
    .catch(error=>{
      // console.log(error)
      alert('网络错误 不能访问')
    })

    axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods')
    .then(reponse=>{
      // console.log(reponse)
      this.type0Goods=reponse.data[0];
      this.type1Goods=reponse.data[1];
      this.type2Goods=reponse.data[2];
      this.type3Goods=reponse.data[3];
    })
    .catch(error=>{
      // console.log(error)
      alert('网络错误 不能访问')
    })
  },
  mounted:function(){
    var orderHeight=document.body.clientHeight;
    document.querySelector("#order-list").style.height=orderHeight+'px'
    // console.log(orderHeight)
    
    },
    methods:{
      addOrderList(goods){

        this.totalMoney = 0;
        this.totalCount = 0;


        //商品是否存在订单列表中
            let isHave= false;
            for(let i=0;i<this.tabledata.length;i++){
               if(this.tabledata[i].goodsId==goods.goodsId){
                 isHave=true
               }
            }
        //根据判断的结果编写业务逻辑
        if(isHave){
          //有了这个商品。改变数量
          let arr = this.tabledata.filter(o=>o.goodsId == goods.goodsId);
          arr[0].count++;
        }else{
          let newGoods = {
            goodsId:goods.goodsId,
            goodsName:goods.goodsName,
            price:goods.price,
            count:1
          }
          this.tabledata.push(newGoods)
        }
        //计算汇总金额和数量
       this.getAllMoney();
      },

      //删除单个商品
      delSingGoods(goods){
        this.tabledata=this.tabledata.filter(o=>o.goodsId !=goods.goodsId)
        this.getAllMoney();
      },
      //汇总数量和金额
      getAllMoney(){
        this.totalMoney=0;
        this.totalCount=0;
        if(this.tabledata){
           this.tabledata.forEach((element)=>{
          this.totalCount+=element.count;
          this.totalMoney=this.totalMoney+(element.price*element.count);
        })
        }
      },
      //删除所有商品
      delAllGoods(){
        this.tabledata=[];
        this.totalMoney=0;
        this.totalCount=0;
      },
      //模拟结账
      checkout(){
        if(this.totalCount!=0){
          this.tabledata=[];
          this.totalMoney=0;
          this.totalCount=0;
          this.$message({
            message:'结账成功',
            type:'success'
          })
        }else{
          this.$message.error('购物车为空')
        }
      }
    }
}
</script>

<style scoped>

.post-order{
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.btn-3-div{
  margin-top: 10px;
}
.title{
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #F9FAFC;
  padding: 10px;
  text-align: left;
}
.often-goods-list ul li{
  list-style: none;
  float: left;
  border: 1px solid #E5E9F2;
  padding: 10px;
  margin: 10px;
  background-color: #fff;
}
.o-price{
  color: #58b7ff
}
.goods-type{
  clear: both;
}
.cookList li{
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auot;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;
       cursor: pointer;
 
   }
   .cookList li span{
       
        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       font-size: 18px;
       padding-left: 10px;
       color:brown;
 
   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
   .totaldiv{
     text-align: left;
     background-color: #fff;
     padding: 10px;
     border-bottom: 1px solid #ccc
   }
</style>

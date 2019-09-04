<template>
  <div class="allOrder fl">
    <div class="title">
      <span>我的订单</span>
    </div>
    <div class="content">
        <div class="info clearfix">
           <span class="fl" style="text-align:center;">订单编号</span>
           <span class="fl">商品信息</span>
           <span class="fl">价格</span>
           <!-- <span class="fl">支付方式</span> -->
           <span class="fl">状态</span>
           <span class="fr">下单时间</span>
        </div>
        <div class="total">
            <ul v-for="(item,index) in data" :key="index">
                <!-- <li class="clearfix dingdan">
                    <p class="fl">
                       <span>订单编号 ：</span><span>{{item.order_id}}</span> 
                    </p>
                    <p class="fr">
                    <span>下单时间 :</span><span>{{item.time}}</span>
                    </p>
                </li> -->
                <li class="clearfix neirong">
                    <p class="fl"><span>{{item.order_id}}</span></p>
                    <p class="fl guanword"><span>{{item.buy_obj}}</span></p>
                    <p class="fl monery">￥{{item.money}}元</p>
                    <!-- <p class="fl type">{{item.pay_type=='1'?"支付宝":item.pay_type=="2"?"微信":item.pay_type=="3"?"学时卡":"赠送"}}</p> -->
                    <p :class="item.status=='0'?'zhifu':'yizhifu'">{{item.status=='0'?"未支付":item.status=="1"?"已支付":"支付失败"}}</p>
                    <p class="fr"><span>{{item.time}}</span></p>
                </li>
            </ul>
           
          <div class="block">
            <el-pagination
                background
                :hide-on-single-page="pagevalue"
                layout="total,prev, pager, next,jumper"
                :page-size="10" 
                :total="count"   
                @current-change="handleCurrentChange" 
                :current-page.sync="pageNo" 
                :page-count='5'
                >
            </el-pagination>
            </div>
           
           
        </div>
    </div>
  </div>
</template>

<script>
import qs from 'qs'
export default {
    data (){
        return {
            uid:'',
            token:'',
            page:1,
            pageNo:1,
            num:10,
            count:0,
            data:[],
            pagevalue:false,
            // currentPage:1
            apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
        }
    },
    created (){
        this.uid= localStorage.getItem('uid')
        this.token=localStorage.getItem('token')
        console.log(this.uid,this.token)
        
         if(this.token){
           this.getOrder()
            }else{
                this.$router.push('/login')
                 this.removeInfo()
            }
    },
    watch: {
		token: {
			handler: function(val) {
                 if (val) {
                    }else{
                    this.removeInfo()
                }
			}
		},deep:true
	},
    methods:{
        removeInfo(){
            var that=this
            localStorage.removeItem("uid");
            localStorage.removeItem("token");
            localStorage.removeItem("sex");
            localStorage.removeItem("name");
            localStorage.removeItem("mobile");
            localStorage.removeItem("id_card");
            setTimeout(() => {
            that.$router.push({ path: '/login' });
            }, 1600);
        },
        getOrder (){
            var that=this
            var data={
                uid:this.uid,
                token:this.token,
                page:this.page,
                num:this.num
            }
            this.$axios.post(this.apiurl+'/pay/get_pay_order',
            qs.stringify(data)
            ).then(res =>{
                console.log(res)
                if(res.data.status=="ok"){
                    that.count=Number(res.data.data.count);
                    if(that.count/6>1){
                        that.pagevalue=true;
                    }
                    that.data=[]
                    that.data=that.data.concat(res.data.data.data)
                    console.log( that.data)
                }else if((res.data.status=="error")){
                    this.$message.error({message:res.data.errormsg,duration:1600});
                }else if((res.data.status=="relogin")){
                    this.$message.error({message:"重新登录",duration:1600});
                    that.removeInfo();
                }
            })
        },
       //分页
          handleCurrentChange(val) {
              this.page=val
           console.log(`当前页: ${val}`);
           localStorage.setItem('pagenum',this.page)
           this.getOrder ()
          },
    }
};
</script>

<style scoped lang="less">
.allOrder {
  width: 948px;
  .title {
    width: 100%;
    height: 55px;
    background-color: #fafafa;
    margin-bottom: 20px;
    font-size: 18px;
    padding-left: 20px;
    line-height: 55px;
    color: #0c69f5;
    box-sizing: border-box;
    span {
      border-bottom: 2px solid #0c69f5;
    }
  }
  .content{
      position: relative;
      width: 100%;
      min-height: 600px;
    //   border:1px solid red;
      .info{
          width: 100%;
          height: 35px;
          line-height: 35px;
            background-color: #138bef;
            padding: 0 20px 0 20px;
            box-sizing: border-box;
            color:#fff;
            font-size:14px;
             overflow: hidden;
            span{
                display:inline-block;
                overflow: hidden;
                text-align: center;
                &:nth-child(1){
                    width: 20.5%;
                }
                 &:nth-child(2){
                    width: 33%;
                }
                 &:nth-child(3){
                    width: 13%;
                }
                 &:nth-child(4){
                    width: 12%;
                }
                &:nth-child(5){
                    width: 20%;
                }

            }
      }
      .total{
          position: relative;
          width: 100%;
          min-height: 600px;
          background-color: #fff;
            ul{
                li{
                    &.dingdan{
                        height: 35px;
                        line-height: 35px;
                        padding: 0 20px 0 20px;
                        background-color: #f1f1f1;
                        font-size: 12px;
                        color: #333333;
                    }
                    &.neirong{
                        padding: 15px 20px 15px 20px;
                        box-sizing: border-box;
                        background-color: #fff;
                        img{
                            width: 150px;
                            margin-right:10px;
                        }
                        p{
                            font-size: 14px;
                            &:nth-child(1){ width: 20.5%;}
                            &:nth-child(2){ width: 33%;}
                            &:nth-child(3){ width: 13%;}
                            &:nth-child(4){ width: 12%;}
                            &:nth-child(5){ width: 20%;}
                            text-align: center;
                            &.guanword{
                                span{
                                    display: block;
                                    &:nth-child(1){
                                       width: 100%;
                                        overflow: hidden;
                                        text-overflow: ellipsis;
                                        white-space: nowrap;
                                    }
                                }
                            }
                            &.zhifu{
                                float: left;
                               color: #fe0000; 
                            }
                            &.yizhifu{
                                float: left;
                               color: #006aca; 
                            }
                             &.type{
                                color: #333; 
                            }
                        }

                    }
                }
            }
      }
      .block{
          text-align: center;
          position: absolute;
          bottom: 0;
          width: 100%;
          height: 52px;
      }
  }
}
</style>

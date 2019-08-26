<template>
    <div class="examrecord fl">
        <div class="title">
            <span>考试记录</span>        
        </div>
        <div class="content">
            <div class="top">
                <ul class="clearfix">
                    <li class="fl l1">试卷名称</li>
                     <li class="fr l2">操作</li>
                    <li class="fr l2">状态</li>
                    <li class="fr l3">总分数</li>
                    <li class="fr l4" >分数</li>
                    <li class="fr l5">考试时间</li>
                </ul>
            </div>
             <div class="topcon">
                <ul class="clearfix" v-show="nodata" v-for="(item,index) in data" :key="index">
                    <li class="fl l1">{{item.shijuan_title}}</li>
                     <li class="fr l2" @click="todetail(item.id)">查看详情</li>
                    <li class="fr l2">{{item.is_pass=='0'?'未通过':'通过'}}</li>
                    <li class="fr l3">{{item.zong_score}}</li>
                    <li class="fr l4">{{item.score}}</li>
                     <li class="fr l5"> {{item.time}}</li>
                </ul>
            </div>
            <div class="nodata" v-show="!nodata">

            </div>
             <div class="block">
                <button @click="prevPage()">
                    上一页
                </button>
                <span>第{{currentPage}}页/共{{totalPage}}页</span>
                <button @click="nextPage()">
                    下一页
                </button>
           </div>
            
        </div>
       
    </div>
</template>

<script>
import qs from 'qs'
export default {
    data(){
        return{
            nodata:false,
            uid:'',
            token:'',
            page:'1',
            num:'10',
            data:[],
            apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
            pageNo:1,
             productList:[], //所有数据
            totalPage: 1, // 统共页数，默认为1
            currentPage: 1, //当前页数 ，默认为1
            pageSize: 3, // 每页显示数量
            currentPageData: [], //当前页显示内容
            pageSize:1
        }
    },
    created (){
            this.uid=localStorage.getItem('uid')
            this.token=localStorage.getItem('token')
              this.kaoshi()
    },
     mounted() {
        // 计算一共有几页
        this.totalPage = Math.ceil(this.data.length / this.pageSize);
        console.log("杜崇")
        console.log(this.data.length)
        // 计算得0时设置为1
        this.totalPage = this.totalPage == 0 ? 1 : this.totalPage;
        this.getCurrentPageData();
       
    },
    methods:{
          kaoshi (){
           //获取考试记录
            var that=this 
            var data={
                page:this.page,
                uid:this.uid,
                token:this.token,
                num:this.num
            }
                this.$axios({
                method: 'post',
                url: this.apiurl+'/kaoshi/get_kaoshi_log',
                data:qs.stringify(data)
                }).then(res => {
                    console.log(res)
                    if(res.data.status=="ok"){
                        that.data=[]
                        that.data =res.data.data.data
                        that.count=res.data.data.count
                        that.pageSize=res.data.data.pageSize
                        if(res.data.data.data){
                            that.nodata=true
                        }else{
                            that.nodata=false
                        }
                    }else
                    if(res.data.status=="relogin"){
                         that.login1=0;
                        that.$message.error({message:"请重新登录",duration:1600});
                        localStorage.removeItem("login1");
                        that.removeInfo();
                    }else {
                        that.login1=0;
                        that.clearlocalData();
                        that.$message.error({message:response.data.errormsg,duration:1600});
                    }

            });
      },  
       removeInfo(){
        localStorage.removeItem("uid");
        localStorage.removeItem("token");
        localStorage.removeItem("sex");
        localStorage.removeItem("name");
        localStorage.removeItem("mobile");
        localStorage.removeItem("id_card");
      },
       //状态为relogin时清除local数据
      clearlocalData:function(){
        let that = this;
        that.$message.error({message:"请重新登录",duration:1600});
        localStorage.removeItem("login1");
        localStorage.removeItem("uid");
        localStorage.removeItem("token");
        localStorage.removeItem("sex");
        localStorage.removeItem("name");
        localStorage.removeItem("mobile");
        localStorage.removeItem("id_card");
        setTimeout(() => {
          that.$router.push({ path: 'login' });
        }, 1600);
      },
      todetail (num){
          this.$router.push({
              path:"/submit",
              query:{
                  kaoshi_id:num
              }
          })
      } ,
      getCurrentPageData() {
            let begin = (this.currentPage - 1) * this.pageSize;
            let end = this.currentPage * this.pageSize;
            this.currentPageData = this.productList.slice(
                begin,
                end
            );
        },
        //上一页
        prevPage() {
            console.log(this.currentPage);
            if (this.currentPage == 1) {
                return false;
            } else {
                this.currentPage--;
                this.getCurrentPageData();
            }
        },
        // 下一页
        nextPage() {
            if (this.currentPage == this.totalPage) {
                return false;
            } else {
                this.currentPage++;
                this.getCurrentPageData();
            }
        }
    }

}
</script>

<style lang="less" scoped>
    .examrecord{
        width: 948px;
        // border:1px solid red;
        .title{
            width: 100%;
            height: 55px;
            background-color: #fafafa;
            margin-bottom: 20px;
            font-size: 18px;
            padding-left: 20px;
            line-height: 55px;
            color: #0c69f5;
            box-sizing: border-box;
            span{
                border-bottom:2px solid #0c69f5;
            }
        }
        .content{
            width: 100%;
            .top{
                 ul{
                  background-color: #138bef;
                  width: 100%;
                  padding: 0 20px;
                    box-sizing: border-box;
                     cursor: default;
                li{
                    // width: 910px;
                    height: 35px;
                    line-height: 35px;
                    font-size:14px;
                    color:#fff;
                    overflow: hidden;
                    text-overflow: ellipsis;
                    white-space: nowrap;
                     cursor: default;
                     &.l2{
                        width: 130px;
                        text-align: right;
                    }
                     &.l3{
                        width: 150px;
                        text-align: center;
                        margin-right: 10px;
                         }
                     &.l4{
                        width: 150px;
                        text-align: center;

                    }
                     &.l5{
                        width: 260px;
                        text-align: center;

                    }
                }
            }
            }
             .topcon{
                 ul{
                //   background-color: #138bef;
                  width: 100%;
                  padding: 0 20px;
                    box-sizing: border-box;
                     cursor: default;
                li{
                    // width: 910px;
                   height: 35px;
                    line-height: 35px;
                    font-size:14px;
                    color:#868686;
                    overflow: hidden;
                     text-overflow: ellipsis;
                      white-space: nowrap;
                       cursor: default;
                      &.l1{
                          box-sizing: border-box;
                      }
                     &.l2{
                        width: 130px;
                        text-align: right;
                    }
                     &.l3{
                        width: 150px;
                        text-align: center;
                        margin-right: 10px;
                         }
                     &.l4{
                        width: 150px;
                        text-align: center;

                    }
                     &.l5{
                        width: 260px;
                        text-align: center;

                    }
                }
            }
            }
            .nodata{
                width: 212px;
                height: 240px;
                margin: 80px auto;
                background-image: url('../assets/nodata.png');
            }
            .block{
                color:#868686;
                text-align: right;
                margin-top:15px;
                button{
                    color:#868686;
                   padding:0 10px;
                    height: 20px;
                    box-sizing: border-box;
                    background: none;
                }
            }
           
        }

    }
</style>

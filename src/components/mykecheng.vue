<template>
  <div class="mykecheng fl">
    <div class="title">
      <span :class="{'curspan':suoyin==index}" v-for="(item,index) in tabs" :key="index" @click="changeTab(index)">{{item}}</span>
    </div>
    <div class="content">
        <ul class="clearfix" v-show="datalist.length">
            <li class="fl" v-for="item in datalist" :key="item.id" @click="todetail(item.id)" style=" cursor: pointer;">
                <div class="kechengbox">
                  <img :src="item.img_url" alt="" class="bigimg">
                  <div class="biaozhi">
                    <img v-if="item.kemu==1" src="/static/images/personal/zyk.png" alt="">
                    <img v-else src="/static/images/personal/gxk.png" alt="">
                  </div>
                  <div class="txt"><span>讲师:&nbsp;</span>{{item.jiangshi.name}}<span style="float:right;font-size:12px;margin-right:5px;">购买后永久有效</span></div> 
                </div>
                <p class="tit">{{item.title}}</p>
                <div class="num">
                  <div>学时：<span>{{item.xueshi_num}}</span></div>
                  <div>价格：<span>￥{{item.price}}元</span></div>
                </div>  
                <!-- <p class="txt">{{item.title}}(<span>{{item.xueshi_num}}</span>课时)</p> -->
                
                <div style="padding:10px 0;box-sizing:border-box; "><el-progress :percentage='Number(parseFloat(item.progress).toFixed(1))' :id="item.id"></el-progress></div>
                <!-- <p style="color:red;"><span>主讲:&nbsp;</span>{{item.jiangshi.name}}</p>          -->
            </li>
        </ul>
        <div class="blocks">
          <el-pagination
                background
                @current-change="handleCurrentChange"
                :current-page.sync="pageNo"
                :page-size="6"
                layout="total,prev, pager, next, jumper"
                :total="count"
                :pager-count="7"
                v-show="count>6"
                >
            </el-pagination>
        </div>
        <div class="nodata" v-show="!datalist.length" >
      </div>
    
    </div>
  </div>
</template>

<script>
import qs from 'qs'
import Vue from 'vue'
export default {
  name:'mykecheng',
   data() {
      return {
        used: 0,
        uid:'',
        token:'',
        year:[],
        datalist:[],
        idd:'',
        apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
        fenye:true,
        count:0,
        page:1,
        num:6,
        pageNo:1,
        suoyin:0,
        kemuid:'',
        pagevalue:false,
        tabs:["我的课程","专业课","公需课"]
       
      }
    },
    created (){
        this.uid=localStorage.getItem('uid')
        this.token=localStorage.getItem('token')
        var date=new Date;
        this.year=date.getFullYear()
       
        if(this.token){
            if( sessionStorage.getItem("suoyin1")==0){
               this.suoyin= sessionStorage.getItem("suoyin1");
              this.page=1
              this.kemuid='';
            }else if( sessionStorage.getItem("suoyin1")==1){
            this.suoyin= sessionStorage.getItem("suoyin1");
               this.page=1
              this.kemuid=1;
            }else if( sessionStorage.getItem("suoyin1")==2){
               this.suoyin= sessionStorage.getItem("suoyin1");
               this.page=1
              this.kemuid=2;
            }
           this.checkkecheng()
        }else{
            this.removeInfo()
        }
        
    },
     watch: {
		token: {
			handler: function(val) {
				if (val) {
          //  this.checkkecheng()
				}else{
           this.removeInfo()
        }
			}
		},deep:true
	},
    methods: {
      //分页
      handleCurrentChange(val) {
          this.page=val
        console.log(`当前页: ${val}`);
        localStorage.setItem('pagenum',this.page)
        this.checkkecheng ()
      },
      removeInfo(){
        var that=this
        this.$message.error({message:"重新登录",duration:1600});
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
      //我的课程
      checkkecheng(){
         var that=this
         this.$axios.post(this.apiurl+'/kecheng/get_list_for_buy',
          qs.stringify({
             uid:this.uid,
             token:this.token,
             year:this.year,
             page:this.page,
             num:this.num,
             kemu:this.kemuid,
          })
         ).then(res =>{
            console.log(res)
            if(res.data.status=="ok"){
              that.datalist=[]
              that.datalist=that.datalist.concat(res.data.data.data)
              that.count=Number(res.data.data.count)
              console.log( that.datalist)
              for(var i=0;i<that.datalist.length;i++){
                Vue.set(that.datalist[i],"progress","0")
                //  获取课程id
                  this.getprogress(that.datalist[i].id)
              }
            }else if((res.data.status=="error")){
              this.$message.error({message:res.data.errormsg,duration:1600});
            }else if((res.data.status=="relogin")){
              that.removeInfo();
            }
           
         }) 
      },
      //到课程详情页
    todetail (val){
      console.log(val)
        this.$router.push({
          name:'personcourseDetails',
          params:{
            courseId:val
          }
        })
      
    },
    //获取课程进度
    getprogress (id){
        var that=this
        console.log(id)
        that.$axios.post(this.apiurl+'/kecheng/get_kecheng_jindu',
            qs.stringify({
              kecheng_id:id,
              uid:that.uid,
              token:that.token
            })
        ).then(res =>{
          if(res.data.status=="ok"){
            // console.log(obj)
            for(var i=0;i<that.datalist.length;i++){
                   if(res.data.data['kecheng_id']==that.datalist[i].id){
                      Vue.set(that.datalist[i],"progress",res.data.data.progress)
                      break
                   }
              
            }
          }else if((res.data.status=="error")){
            that.used=0;
          }else if((res.data.status=="relogin")){
             that.removeInfo();
          }
        })
    },
    changeTab:function(index){
      this.suoyin=index;
       sessionStorage.setItem("suoyin1",index)
      if(index==0){
        this.kemuid='';
      }else if(index==1){
        this.kemuid=1;
      }else if(index==2){
        this.kemuid=2;
      }
      this.checkkecheng();
    },
   
    }

};
</script>

<style scoped lang="less">
.mykecheng{
   width: 948px;
   background-color: #fff;
  .title {
    width: 100%;
    height: 55px;
    font-size: 18px;
    padding-left: 35px;
    line-height: 55px;
    color: #0c69f5;
    box-sizing: border-box;
    border-bottom: 2px solid #eee;
    span{margin-right: 20px;cursor: pointer;color: #b9b9b9;}
    .curspan {
      border-bottom: 2px solid #0c69f5;
      padding-bottom: 13px;
      color: #0169ca;
    }
  }
  .content{
      width: 100%;
      min-height: 654px;
      position: relative;
      overflow: hidden;
      ul{
          li{
              // border:1px solid red;
               width: 260px;
                // height: 255px;
                margin-left: 32px;
                margin-top: 15px;
                border: 1px solid #dfe4ed;
                border-radius: 5px;
                padding: 10px;
                background-color: #fff;
                .kechengbox{position: relative;
                  .biaozhi{position: absolute;left: 0;top: 10px;
                    img{width: 50px;}
                  }
                  .bigimg{
                    width: 100%;
                    height: 144px;
                    //border-radius: 5px;
                    overflow: hidden;
                  }
                  .txt{
                    width: 100%;
                    height: 34px;
                    padding-left: 8px;
                    background-color: rgba(0,0,0,.4);
                    font-size: 14px;
                    color: #fff;
                    line-height: 34px;
                    position: absolute;
                    bottom: 0;
                    //border-radius: 0 0 5px 5px;
                  }
                  
                }
                .num{
                  padding-top: 12px;
                  font-size: 13px;
                  display: flex;
                  justify-content: space-between;
                  div{color: #000;
                  span{
                    color: red;
                    font-size: 16px;
                    font-weight: bold;
                  }}
                
                }
                .tit{
                    height: 22px;
                    font-size: 16px;
                    color: #000;
                    overflow: hidden;
                    text-overflow: ellipsis;
                    white-space: nowrap;
                    margin-top: 12px;
                  }
                .jindu{
                  width: 100%;
                  height: 12px;
                  display: flex;
                  justify-content: space-between;
                  margin-top: 16px;
                  span{
                    &.fr{
                      margin-top:-12px;
                    }
                  }
                }
                
          }
          li:hover{
           box-shadow: 0 0 10px #c1c1c1; 
           border:1px solid #c1c1c1; 
          }
      }
      .nodata{
          width: 212px;
          height: 240px;
          margin: 80px auto 0;
          background-image: url('../assets/nodata.png');
      }
      .blocks{
        position: absolute;bottom: 0; text-align:center;width: 100%; height:50px; padding-top:8px;
      }
  }

}
</style>
<style lang="less">
    .mykecheng{
        .block{
          width: 70%;
          height: 10px;
        }
        .el-slider{
            height: 10px;
        }
        .el-slider__runway{
          margin:-5px 0 0 40px;
        }
    }
</style>

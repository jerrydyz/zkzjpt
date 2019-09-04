<template>
  <div class="mypackage">
    <div class="title">
      <h3>
        <span :class="{active:type==1}" @click="tab(1)">全部课程包</span>
        <span :class="{active:type==0}" @click="tab(0)">我的课程包</span>
      </h3>
    </div>
    <div class="content">
      <ul v-show="allpack">
        <li class="clearfix" v-for="(item,index) in list" :key="index">
          <div class="one fl">
            <img :src="item.img_url" alt />
          </div>
          <div class="two fl">
            <h3>{{item.title}}</h3>
            <p>
              <span>年度 :</span>
              <span>{{item.year}}</span>
            </p>
            <p>
              <span>公需课学时 :</span>
              <span style="margin-right:20px;">{{item.gongxuke_xueshi_num}}</span>
              <span>专业课学时 :</span>
              <span>{{item.zhuanyeke_xueshi_num}}</span>
            </p>
            <p v-show="item.jindu>0">学习进度 :<el-progress :percentage="Number(item.jindu)"></el-progress></p>
          </div>
          <div class="three fr">
            <p>
              <span>价格:</span>
              <span>{{item.price}}元</span>
            </p>
            <p :class="{active:item.isBuy==1}" @click.stop="xuexi(item.id)">{{item.isBuy=="0"?"购买":"继续学习"}}</p>
          </div>
        </li>
      </ul>
      <ul v-show="mypack">
        <li class="clearfix" v-for="(item,index) in mydata" :key="index">
          <div class="one fl">
            <img :src="item.img_url" alt />
          </div>
          <div class="two fl">
            <h3>{{item.title}}</h3>
            <p>
              <span>年度 :</span>
              <span>{{item.year}}</span>
            </p>
            <p>
              <span>公需课学时 :</span>
              <span style="margin-right:20px;">{{item.gongxuke_xueshi_num}}</span>
              <span>专业课学时 :</span>
              <span>{{item.zhuanyeke_xueshi_num}}</span>
            </p>
            <p  v-show="item.jindu>0"><span >学习进度 :</span><el-progress :percentage="Number(item.jindu)" ></el-progress></p>
          </div>
          <div class="three fr">
            <p>
              <span>价格:</span>
              <span>{{item.price}}元</span>
            </p>
            <p :class="{active:true}" @click.stop="xuexi(item.id)">继续学习</p>
          </div>
        </li>
      </ul>
     
       <div class="blocks" style="text-align:right;margin-right:20px;margin-top:20px;">
        <el-pagination
             background
            @current-change="handleCurrentChange"
            :current-page.sync="pageNo"
            :page-size="3"
            layout="total,prev, pager, next, jumper"
            :total="count"
            :pager-count="7"
            v-show="count>3&&allpack"
            >
        </el-pagination>
        <el-pagination
             background
            @current-change="handleCurrentChange1"
            :current-page.sync="pageNo"
            :page-size="3"
            layout="total,prev, pager, next, jumper"
            :total="count1"
            :pager-count="7"
             v-show="count1>3&&mypack==true"
            >
        </el-pagination>
        </div>
    </div>
  </div>
</template>

<script>
import qs from 'qs'
import Vue from 'vue'
export default {
  name: "mypackage",
  data() {
    return {
      type: 1,
      uid:'',
      token:'',
       list:[],
      year:'',
      pageNo:1,
      idd:'',
      apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
       fenye:true,
       count:0,
        page:1,
        num:3,
        pageNo:1,
        mydata:[],
        allpack:true,
        mypack:false,
        count1:0
    };
  },
  created (){
      this.uid = localStorage.getItem("uid");
        this.token = localStorage.getItem("token");
        var date = new Date();
        this.year = date.getFullYear();
      this.kechengbao()
  },
   watch: {
		token: {
			handler: function(val) {
				if (val) {
          // this.kechengbao();
				}else{
           this.$router.push('/login')
           this.removeInfo()
        }
			}
		},deep:true
	},
  methods: {
    tab(num) {
      this.type = num;
      if(num==0){
         this.mypackage()
         this.mypack=true
         this.allpack=false
      }else{
         this.mypack=false
         this.allpack=true
      }
    },
    //获取全部课程包
    kechengbao() {
      var that = this;
      this.$axios({
        method: "post",
        url: this.apiurl + "/kecheng/get_kechengbao_list",
        data: qs.stringify({
          year: this.year,
          page: this.page,
          num: this.num
        })
      }).then(res => {
        console.log(res);
        if (res.data.status == "ok") {
          that.list = [];
          that.list = that.list.concat(res.data.data.data);
          that.count = Number(res.data.data.count);
          for (var i = 0; i < that.list.length; i++) {
            Vue.set(that.list[i], "isBuy", "0");
              Vue.set(that.list[i], "jindu", "0");
            this.isBuy(that.list[i].id);
              this.getprogress1(that.list[i].id); 

          }
        } else if (res.data.status == "error") {
          this.$message.error({ message: res.data.errormsg, duration: 1600 });
        } else if (res.data.status == "relogin") {
          that.removeInfo();
        }
      });
    },
    //各个课程包是否购买
    isBuy: function(id) {
      let that = this;
      let courseId = {
        uid: this.uid,
        token: this.token,
        kecheng_bao_id: id
      };
      this.$axios
        .post(
          this.apiurl + "/kecheng/check_kecheng_bao_is_buy",
          qs.stringify(courseId)
        )
        .then(res => {
          console.log("李四");
          console.log(res);
          if (res.data.status == "ok") {
            for (var i = 0; i < that.list.length; i++) {
              if (res.data.data["kecheng_bao_id"] == that.list[i].id) {
                Vue.set(that.list[i], "isBuy", res.data.data.check_res);
                break;
              }
            }
          } else if (res.data.status == "error") {
            this.$message.error({ message: res.data.msg, duration: 1600 });
          } else if (res.data.status == "relogin") {
            that.removeInfo();
          }
        });
    },
     //分页
          handleCurrentChange(val) {
              this.page=val
           console.log(`当前页: ${val}`);
           this.kechengbao ()
          },
            //分页
          handleCurrentChange1(val) {
              this.page=val
           console.log(`当前页: ${val}`);
           this.mypackage ()
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
    xuexi (packageid) {
        let that =this;
        let courseId={uid:localStorage.getItem("uid"),token:localStorage.getItem("token"),kecheng_bao_id:packageid}
        this.$axios.post(this.apiurl+'/kecheng/check_kecheng_bao_is_buy',qs.stringify(courseId))
          .then(res => {
            console.log("错误信息")
            console.log(res)
            if(res.data.status=="ok"){
              if(res.data.data.check_res=="0"){
                this.$router.push({path:'packagebuy',query:{packid:packageid}});
              }else if(res.data.data.check_res=="1"){
                that.gopackdetail(packageid);
              } 
            }else if(res.data.status=="error"){
              this.$message.error({message: res.data.msg,duration:1600});
            }else if(res.data.status=="relogin"){
              that.removeInfo();
            }
            
        });
         
      },
      //我的课程包
      mypackage (){
         var that=this
         var data ={
           uid:this.uid,
           token:this.token,
           year:this.year,
           page:this.page,
           num:this.num,
         }
         this.$axios.post('http://jixujiaoyu_api.songlongfei.club:1012/kecheng/get_kechengbao_list_for_buy',
         qs.stringify(data)).then(res =>{
              console.log("获取我的课程包")
              console.log(res)
              if(res.data.status){
                 that.mydata = [];
                 that.mydata=res.data.data.data
                 that.count1 = Number(res.data.data.count);
              for(var i=0;i<that.mydata.length;i++){
                    Vue.set(that.mydata[i], "jindu", "0");
                     this.getprogress(that.mydata[i].id);     
              }

              }
              
         })
      },
      //我的课程包
      getprogress(num){
         var that=this
         this.$axios.post("http://jixujiaoyu_api.songlongfei.club:1012/kecheng/get_kecheng_bao_jindu",qs.stringify({
            kecheng_bao_id:1,
            uid:this.uid,
            token:this.token
         })).then(res =>{
           console.log("获取我的课程进度")
           console.log(res)
           if(res.data.status=="ok"){
              for(var j=0;j<that.mydata.length;j++){
                  if(that.mydata[j].id==res.data.data['kecheng_bao_id']){
                      Vue.set(that.mydata[j],"jindu",res.data.data.progress)
                      break
                  }
              }
           }
         })
      },
  //全部课程包
    getprogress1(num){
         var that=this
         this.$axios.post("http://jixujiaoyu_api.songlongfei.club:1012/kecheng/get_kecheng_bao_jindu",qs.stringify({
            kecheng_bao_id:1,
            uid:this.uid,
            token:this.token
         })).then(res =>{
           console.log("获取我的课程进度")
           console.log(res)
           if(res.data.status=="ok"){
              for(var j=0;j<that.mydata.length;j++){
                  if(that.list[j].id==res.data.data['kecheng_bao_id']){
                      Vue.set(that.list[j],"jindu",res.data.data.progress)
                      break
                  }
              }
           }
         })
      },
  }
};
</script>

<style lang="less" scoped>
.mypackage {
  width: 948px;
  height:   708px;
  background-color: #fff;
  padding: 0 20px;
  box-sizing: border-box;
  .title {
    width: 100%;
    height: 50px;
    line-height: 50px;
    border-bottom: 1px solid #f4f4f4;
    margin-bottom: 20px;
    box-sizing: border-box;
    h3 {
      // color:#0169cc;
      span {
        cursor: default;
        &:nth-child(1) {
          margin-right: 30px;
        }
        &.active {
          color: #0169cc;
          padding-bottom: 13px;
          border-bottom: 2px solid #0169cc;
          box-sizing: border-box;
        }
      }
    }
  }
  .content {
    width: 100%;
    li {
      margin-bottom: 5px;
      border-bottom: 1px solid #eee;
      .one {
        margin-right: 20px;
        width: 218px;
        height: 136px;
        img {
          width: 100%;
          height: 100%;
        }
      }
      .two {
        padding: 15px 0;
        box-sizing: border-box;
        h3 {
          font-weight: bold;
          color: #111;
          margin-bottom: 25px;
        }
        p {
          line-height: 30px;
          span {
            color: #111;
            &:nth-child(1) {
              margin-right: 10px;
            }
          }
        }
      }
      .three {
        padding: 20px 10px;
        box-sizing: border-box;
        p:nth-child(1) {
          color: #111;
          font-size: 16px;
          span:nth-child(2) {
            font-size: 20px;
            color: #fe4040;
            margin-left: 10px;
          }
        }
        p:nth-child(2) {
          margin-top: 40px;
          color: red;
          width: 130px;
          height: 40px;
          line-height: 40px;
          text-align: center;
          background-color: #fe4040;
          color: #fff;
          font-size: 16px;
          cursor: pointer;
        }
        p.active{
            background-color: #0169cc;
            color:#fff;
        }
      }
    }
  }
}
</style>
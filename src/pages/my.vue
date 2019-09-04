<template>
  <div class="my">
    <div class="title">
      <div class="top w clearfix">
        <p class="fl">河南省继续教育学会在线学习平台</p>
        <p class="fr">
          <span class="spn1"  @click="more('personcenter')" >个人中心</span>
          <span @click="goback">退出</span>
        </p>
      </div>
    </div>
    <div class="content w clearfix">
        <div class="leftcon fl">
            <div class="top" >
                <div class="one">
                    <h3>{{name}}</h3>
                    <p><span>身份证号:</span><span>{{id_card}}</span></p>
                    <p class="ptype" @click="more('changemsg')">个人设置</p>
                </div>
                <div class="two clearfix">
                    <div class="left fl">
                        <p class="p1"><img src="../assets/canjia01.png" alt=""></p>
                        <p class="p3">公需课</p>
                    </div>
                    <div class="line fl"></div>
                    <div class="right fl" v-for="(item,index) in data" :key="index">
                        <p class="p2">
                            <span>可用学时:</span><span class="spn">{{item.keyong_gongxuke_xueshi_num}}</span>
                        </p>
                        <p>
                             <span>已获学时:</span><span class="spn">{{item.get_gongxuke_xueshi_num}}</span>
                        </p>
                    </div>
                </div>
                 <div class="two clearfix">
                    <div class="left fl">
                        <p class="p1"><img src="../assets/canjia02.png" alt=""></p>
                        <p class="p3">专业课</p>
                    </div>
                    <div class="line fl"></div>
                    <div class="right fl"  v-for="(item,index) in data" :key="index">
                        <p class="p2">
                            <span>可用学时:</span><span class="spn">{{item.keyong_zhuanyeke_xueshi_num}}</span>
                        </p>
                        <p>
                             <span>已获学时:</span><span class="spn">{{item.get_zhuanyeke_xueshi_num}}</span>
                        </p>
                    </div>
                </div>
            </div>
            <div class="leftbot">
                <ul>
                    <li :class="{active:bgcolor=='allCourses'}" @click="more('allCourses')">
                        <i class="iconfont icon-file"></i>
                        <span>全部课程</span>
                       <i class="iconfont icon-right fr"></i>
                    </li>
                    <li :class="{active:bgcolor=='mypackage'}" @click="more('mypackage')">
                         <i class="iconfont icon-shop"></i>
                        <span>课程包</span>
                        <i class="iconfont icon-right fr"></i>
                    </li>
                    <li :class="{active:bgcolor=='mykecheng'}" @click="more('mykecheng')">
                         <i class="iconfont icon-vipcard"></i>
                        <span>我的课程</span>
                        <i class="iconfont icon-right fr"></i>
                    </li>
                    <li :class="{active:bgcolor=='examination'}" @click="more('examination')">
                          <i class="iconfont icon-edit_light"></i>
                        <span>参加考试</span>
                       <i class="iconfont icon-right fr"></i>
                    </li>
                    <li :class="{active:bgcolor=='examrecord'}" @click="more('examrecord')">
                        <i class="iconfont icon-text"></i>
                        <span>考试记录</span>
                        <i class="iconfont icon-right fr"></i>
                    </li>
                    <li :class="{active:bgcolor=='archives'}" @click="more('archives')">
                        <i class="iconfont icon-news_light"></i>
                        <span>档案记录</span>
                       <i class="iconfont icon-right fr"></i>
                    </li>
                    <li :class="{active:bgcolor=='allOrder'}" @click="more('allOrder')">
                         <i class="iconfont icon-calendar"></i>
                        <span>我的订单</span>
                        <i class="iconfont icon-right fr"></i>
                    </li>
                </ul>
            </div>
        </div>
        <component :is="componentId" class="fl"></component>
    </div>
  </div>
</template>

<script>
import qs from 'qs'
import examination from '../components/examination'
import personcenter from '../components/personcenter'
import mypackage from '../components/mypackage'
import examrecord from '../components/examrecord'
import archives from '../components/archives'
import allCourses from '../components/allCourses'
import mykecheng from '../components/mykecheng'
import allOrder from '../components/allOrder'
import changemsg from '../components/changemsg'
export default {
  name: "my",
  data() {
    return {
      componentId:'personcenter',
       bgcolor:'',
        name:localStorage.getItem('name'),
        year:'',
        data:[],
        id_card:localStorage.getItem('id_card'),
        uid:localStorage.getItem('uid'),
        token:localStorage.getItem('token'),
        apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
    };
  },
  watch: {
      token: {
        handler: function(val) {
              if (val) {
                  
              }else{
               this.clearlocalData();
              }
        }
      },deep:true
    },
    created (){
      var date = new Date();
      this.year = date.getFullYear();
         var leng=this.id_card.length
        this.id_card = this.id_card.substr(0, 3) + '****' + this.id_card.substr(leng - 4);
      if(localStorage.getItem('types')){
         this.more( localStorage.getItem('types'))
      }
        this.getxueshinum ()
      
    },
  methods: {
     more(val){
        this.componentId=val
        this.bgcolor=val
        localStorage.setItem('types',this.componentId)
      },
      //获取学时
      getxueshinum (){
        var that=this
        this.$axios.post('http://jixujiaoyu_api.songlongfei.club:1012/dangan/get_user_year_xueshi',
         qs.stringify({
             uid:this.uid,
             token:this.token,
             year:this.year
           })

        ).then(res =>{
          console.log(res)
          if(res.data.status=='ok'){
            // that.name=res.data.data[0].name
            // that.id_card=res.data.data[0].id_card
            that.data=res.data.data
          }
        })
      },
       //返回按钮
      goback (){
          var that =this
          this.$axios.post(this.apiurl+'/user/logout',
           qs.stringify({
             uid:this.uid,
             token:this.token
           })
          ).then(res =>{
            that.$message.success({message:"退出成功",duration:1600});
            that.clearlocalData();
            
          })
      },
      //状态为relogin时清除local数据
      clearlocalData:function(){
        let that =this;
        localStorage.removeItem("login1");
        localStorage.removeItem("uid");
        localStorage.removeItem("token");
        localStorage.removeItem("sex");
        localStorage.removeItem("name");
        localStorage.removeItem("mobile");
        localStorage.removeItem("id_card");
        setTimeout(() => {
          that.$router.push({ path: 'index' });
        }, 1600);
      },
    
  },
  components:{
     examination,
     personcenter,
     mypackage,
     examrecord,
     archives,
     allCourses,
     mykecheng,
     allOrder,
     changemsg

  }
};
</script>

<style lang="less" scoped>
@family:AlibabaPuHuiTiR;
@family1:MicrosoftYaHei;
.my {
  width: 100%;
  height: 100%;
  .w {
    width: 1200px;
    margin: 0 auto;
  }
  .title {
    width: 100%;
    height: 72px;
    line-height: 72px;
    background-color: #0169cc;
    color: #fff;
    font-size: 28px;
    letter-spacing: 2px;
    cursor: pointer;
    .fr {
      font-size: 16px;
      .spn1 {
        margin-right: 45px;
      }
    }
  }
  .content {
    font-size: 14px;
    margin-top:20px;
    min-height: 680px;
    .leftcon {
      width: 242px;
      margin-right:10px;
     
      .top{
          width: 100%;
          text-align: center;
           box-sizing: border-box;
            cursor: default;
          .one{
              width: 100%;
              margin: 0 auto;
              box-sizing: border-box;
               background-color: #e1f1ff;
                padding-bottom: 20px;
              box-sizing: border-box;
              h3{
                font-weight: bold;
                font-size:18px;
                margin-bottom: 18px;
                padding-top:28px;
              }
              .ptype{
                 width: 142px;
                 height: 40px;
                 border-radius: 40px;
                 border: 1px solid #51a3ef;
                 line-height: 40px;
                 margin: 0 auto;
                 margin-top:20px;
                 color:#51a3ef;
                  cursor: pointer;
              }
          }
          .two{
             margin-top:2px;
              background-color: #e1f1ff;
              width: 100%;
              padding:24px 0 16px ;
              box-sizing: border-box;
              cursor: default;
              .left{
                width: 40%;
              }
              .right{
                width: 58%;
              }
              .line{
                width: 2px;
                height: 50px;
                background-color: #ffff;
              }
              p{
                color:#333;
                &.p1{
                  margin-bottom:3px;
                }
                &.p2{
                  margin-bottom:5px;
                }
                .spn{
                  font-weight: bold;
                  margin-left:5px;
                   color:#51a3ef;
                }
                &.p3{
                  font-weight: bold;
                }
              }
          }
         
      }
      .leftbot{
          width: 100%;
          background-color: #fff;
          ul{
              width: 100%;
              padding:20px 0 ;
              box-sizing: border-box;
              li{
              width: 100%;
              height: 45px;
              line-height: 45px;
               font-family: @family;
               font-size:16px;
               color:#1a1a1a;
               letter-spacing: 1px;
               padding: 0 20px;
              box-sizing: border-box;
              cursor: pointer;
                i.fl{
                    margin-right:20px;
                    font-size:20px;
                }
            &.active{
               color: #138bef;
            }
          }
          } 
         
      }

    }
    // 右侧内容
   

  }
}
</style>

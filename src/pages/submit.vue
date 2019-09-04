<template>
  <div class="submit">
    <div class="title">
      <div class="top w">
        <p class="clearfix">
          <span class="fl">所属课程: &nbsp;&nbsp;&nbsp;{{kecheng_title}}</span>
          <span class="fr">
            考试用时:
            <span id="stime" v-html="usetime"></span>
          </span>
        </p>
        <p class="clearfix">
          <span class="fl">试卷名称: &nbsp;&nbsp;&nbsp;{{title}}</span>
          <span
            class="fr"
          >（总分 ：{{zong_score}}分 单选题:{{datalist1.length}}道 多选题: {{datalist2.length}}道 判断题：{{datalist3.length}}道 填空题：{{datalist4.length}}道）</span>
        </p>
      </div>
    </div>
    <div class="content w clearfix">
      <div class="leftside1 fl"></div>
      <div class="leftside fl">
         <div class="back" @click="goback">
          <img src="../assets/back.png" alt="" class="image">
            返回个人中心
        </div>
        <div class="lefttop">
          <h3>考生信息</h3>
          <p class="p1">
            <span>考生姓名:</span>
            <span>{{name}}</span>
          </p>
          <p class="p2">
            <span>身份证号:</span>
            <span>{{idcord}}</span>
          </p>
        </div>
        <div class="leftbottom">
          <h3>答题卡</h3>
          <div class="first">
            <p>
              第一大题:{{datatitle1}}题
              <span>(每题{{score1}}分)</span>
            </p>
            <ul class="clearfix">
              <li :xshiti_id="item1.id" v-for="(item1,index) in datalist1" :key="index" :class="{active:bgcolor==item1.user_answer.result,active1:bgcolor1==item1.user_answer.result}"><a :href="'#'+item1.id">{{index+1}}</a></li>
           
            </ul>
          </div>
          <div class="first">
            <p>
              第二大题:{{datatitle2}}题
              <span>(每题{{score2}}分)</span>
            </p>
            <ul class="clearfix">
			   <li :xshiti_id="item2.id" v-for="(item2,index) in datalist2" :key="index" :class="{active:bgcolor==item2.user_answer.result,active1:bgcolor1==item2.user_answer.result}"><a :href="'#'+item2.id">{{index+1}}</a></li>
            </ul>
          </div>
          <div class="first">
            <p>
              第三大题:{{datatitle3}}题
              <span>(每题{{score3}}分)</span>
            </p>
            <ul class="clearfix">
			       <li :xshiti_id="item3.id" v-for="(item3,index) in datalist3" :key="index" :class="{active:bgcolor==item3.user_answer.result,active1:bgcolor1==item3.user_answer.result}"><a :href="'#'+item3.id">{{index+1}}</a></li>
            </ul>
          </div>
          <div class="first">
            <p>
              第四大题:{{datatitle4}}题
              <span>(每题{{score4}}分)</span>
            </p>
            <ul class="clearfix">
			     <li :xshiti_id="item4.id" v-for="(item4,index) in datalist4" :key="index" :class="{active:bgcolor==item4.user_answer.result,active1:bgcolor1==item4.user_answer.result}"><a :href="'#'+item4.id">{{index+1}}</a></li>
            </ul>
          </div>
        </div>
         <div class="zong_score">
            <p class="clearfix"><span class="fl score">{{score}}分</span><span class="fr">正确</span><span class="fr square"></span></p>
            <p class="clearfix"><span class="fl score1">考试得分</span><span class="fr">错误</span ><span class="fr square1"></span></p>
          </div>
      </div>
      <div class="big">
        <div class="right_type fr">
          <!--单选题  -->
          <div class="radio_type">
            <h3>{{datatitle1}}题</h3>
            <div class="radio_con">
              <ul>
                <li class="bankuai" v-for="(item,index) in datalist1" :key="index" :id="item.id">
                  <p class="tit_type">{{index+1}}、{{item.question}}</p>
                  <ul class="child shiti_select_div" :shiti_id="item.id" :shiti_type="item.type">
                    <li class="child_type">
                      <label v-for="(key,val) in xuanze" :key="val">
                        <input type="radio" :name="['danxuan_'+item.id]" :value="val" style="margin-right:5px" />
                        <span>{{val}}、</span>
                        <span>{{key}}</span>
                      </label>
                    </li>
                  </ul>
                  <p class="jiexi"><span>正确答案 :</span><span>{{item.user_answer.rigth_answer}}</span></p>
                   <p class="jiexi"><span>您的答案 :</span><span>{{item.user_answer.answer==""?"未做答":item.user_answer.answer}}</span></p>
                  <p class="jiexi"><span>解析 :</span><span>{{item.jiexi}}</span></p>
                </li>
              </ul>
            </div>
          </div>
          <!-- 多选题 -->
          <div class="check_box">
            <h3>{{datatitle2}}题</h3>
            <div class="check_con">
              <ul>
                <li
                  class="bankuai"
                  v-for="(check,index) in datalist2"
                  :key="check.id"
                  :id="check.id"
                >
                  <p class="tit_type">{{index+1}}、{{check.question}}</p>
                  <ul class="child shiti_select_div" :shiti_id="check.id" :shiti_type="check.type">
                    <li class="child_type">
                      <label v-for="(key,val) in xuanze2" >
                        <input type="checkbox" :name="['duoxuan_'+check.id+'[]']" :value="val" style="margin-right:5px" />
                        <span>{{val}}、</span>
                        <span>{{key}}</span>
                      </label>
                    </li>
                  </ul>
                   <p class="jiexi"><span>正确答案 :</span><span>{{check.user_answer.rigth_answer.join(" 、")}}</span></p>
                  <p class="jiexi"><span>您的答案 :</span><span>{{check.user_answer.answer.join(" 、")==""?"未做答":check.user_answer.answer.join(" 、")}}</span></p>
                  <p class="jiexi"><span>解析 :</span><span>{{check.jiexi}}</span></p>
                </li>
              </ul>
            </div>
          </div>
          <!-- 判断题 -->
          <div class="radio_type">
            <h3>{{datatitle3}}题</h3>
            <div class="radio_con">
              <ul>
                <li class="bankuai" v-for="(dan,index) in datalist3" :key="dan.id" :id="dan.id">
                  <p class="tit_type">{{index+1}}、{{dan.question}}</p>
                  <ul class="child shiti_select_div" :shiti_id="dan.id" :shiti_type="dan.type">
                    <li class="child_type">
                      <label v-for="(key,val) in xuanze3">
                        <input type="radio" :name="['panduan_'+dan.id]" :value="val" style="margin-right:5px" />
                        <span>{{key}}</span>
                      </label>
                    </li>
                  </ul>
                   <p class="jiexi"><span>正确答案 :</span><span>{{dan.user_answer.rigth_answer}}</span></p>
                   <p class="jiexi"><span>您的答案 :</span><span>{{dan.user_answer.answer==""?"未做答":dan.user_answer.answer}}</span></p>
                  <p class="jiexi"><span>解析 :</span><span>{{dan.jiexi}}</span></p>
                </li>
              </ul>
            </div>
          </div>
          <!-- 填空题 -->
          <div class="text_type">
            <h3>{{datatitle4}}题</h3>
            <div class="text_con">
              <ul>
                <li class="bankuai" v-for="(tian,index) in datalist4" :key="tian.id" :id="tian.id">
                  <p class="tit_type">{{index+1}}、{{tian.question}}</p>
                  <!-- <ul class="child shiti_select_div" :shiti_id="tian.id" :shiti_type="tian.type">
                    <li class="child_type" v-for="(knum,index) in tian.answer.length" :key="index">
                      {{index+1}}、
                      <input
                        type="text"
                        :shiti_tiankong="['tiankong_'+tian.id]"
                        v-model="tian.user_answer.answer.join('、')"
                      />
                    </li>
                  </ul> -->
                   <p class="jiexi"><span>正确答案 :</span><span>{{tian.user_answer.rigth_answer.join(" 、")}}</span></p>
                  <p class="jiexi" :title="tian.user_answer.answer.join('、')==''?'未做答':tian.user_answer.answer.join('、')"><span>您的答案 :</span><span>{{tian.user_answer.answer.join(" 、")==''?"未做答":tian.user_answer.answer.join(" 、")}}</span></p>
                  <p class="jiexi"><span>解析 :</span><span>{{tian.jiexi}}</span></p>
                </li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import $ from "jquery";
import qs from "qs";
import Vue from "vue";
export default {
  name: "submit",
  data() {
    return {
      uid: localStorage.getItem("uid"),
      token:  localStorage.getItem("token"),
      name:localStorage.getItem("name"),
      idcord:localStorage.getItem("id_card"),
       paperdata: [],
      kaoshi_id: "",
      datalist:'',
      data:[],
      title:'',
      usetime:'',
      time:'',
      title:"",
      error_num:'',
      empty_num:'',
      right_num:'',
      score:'',
       xuanze: {},
      xuanze2: {},
      xuanze3: {},
      xuanze4: {},
      datalist1: [],
      datalist2: [],
      datalist3: [],
      datalist4: [],
      jsondata:[],
      answer:[],
      answer2:[],
      answer3:'',
      answer4:[],
      answer5:[],
      num:'',
       B: "",
      D: "",
      F: "",
      H: "",
      bankuai:[],
      shiti:[],
      user_answer:{},
      shiti_arr:[],
      datatitle1:'',
      score1:'',
      datatitle2:'',
      score2:'',
      datatitle3:'',
      score3:'',
      datatitle4:'',
      score4:'',
      bgcolor:"wrong",
       bgcolor1:"right",
        apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
        zong_score:'',
        kecheng_title:''
    };
  },
  created() {
    this.kaoshi_id = this.$route.query.kaoshi_id;
  this.kecheng_title = this.$route.query.kecheng_title;
    if(this.token){
      this.getpaperdata();
    }else{
       this.removeInfo();
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
  computed: {},
  methods: {
    //返回按钮
      goback (){
          var that =this
          this.$axios.post(this.apiurl+'/user/logout',
           qs.stringify({
             uid:this.uid,
             token:this.token
           })
          ).then(res =>{
            if(res.data.status=="ok"){
              that.$message.success({message:"退出成功",duration:1600});
              that.clearlocalData();
              setTimeout(() => {
                that.$router.push({ path: 'index' });
              }, 1600);
            }else if((res.data.status=="error")){
              that.$message.error({message:res.data.errormsg,duration:1600});
            }else if((res.data.status=="relogin")){
              that.removeInfo();
            }
            
          })
      },
    //获取试卷信息
     getpaperdata() {
      var that = this;
      var data = {
        uid: this.uid,
        token: this.token,
        kaoshi_id: this.kaoshi_id
      };
      this.$axios
        .post(
          "http://jixujiaoyu_api.songlongfei.club:1012/kaoshi/get_kaoshi_log_info",
          qs.stringify(data)
        )
        .then(res => {
          console.log(res);
          if(res.data.status=="ok"){
             that.datalist=res.data.data.answer
             that.data=res.data.data.shijuan.shijuan_bankuai
             that.title=res.data.data.shijuan.title
             that.usetime=res.data.data.use_time
             that.empty_num=res.data.data.empty_num
             that.error_num=res.data.data.error_num
             this.right_num=res.data.data.right_num
             that.score=res.data.data.score
              that.zong_score=res.data.data.shijuan.score
             that.jsondata=JSON.parse(that.datalist)
            that.bankuai = res.data.data.shijuan.shijuan_bankuai;
            that.answer = JSON.parse(res.data.data.answer);
            that.num=that.answer.length
            // console.log(that.answer);
            for(var i=0;i<that.bankuai.length;i++){
              that.shiti_arr = that.bankuai[i]['shiti'];
              for(var j=0;j<that.shiti_arr.length;j++){
                var shiti_id = that.shiti_arr[j]['id'];
                for(var n=0;n<that.answer.length;n++){
                  if(that.answer[n]['shiti_id'] == shiti_id){
                      that.bankuai[i]['shiti'][j]["user_answer"] = that.answer[n];
                    break;
                  }
                }
                 if(that.shiti_arr[j].type==1){
                   var A = that.shiti_arr[j].answer_options;
                   var jsonobj = JSON.parse(A);
                   that.xuanze = jsonobj;
                   that.datalist1 = that.bankuai[i]['shiti'];
                   that.datatitle1=that.bankuai[i]["title"]
                   that.score1=that.bankuai[i]["score"]
                   console.log( that.datalist1)
                  
                 }
                  if(that.shiti_arr[j].type==2){
                   var C = that.shiti_arr[j].answer_options;
                   var jsonobj2 = JSON.parse(C);
                   that.xuanze2 = jsonobj2;
                    that.datalist2 = that.bankuai[i]['shiti'];
                     that.datatitle2=that.bankuai[i]["title"]
                   that.score2=that.bankuai[i]["score"]
                 }
                 if(that.shiti_arr[j].type==3){
                   var E = that.shiti_arr[j].answer_options;
                   var jsonobj3 = JSON.parse(E);
                   that.xuanze3 = jsonobj3;
                    if(that.bankuai[i]['shiti'][j]["user_answer"]['answer'] != ""){
                     that.bankuai[i]['shiti'][j]["user_answer"]['answer'] = jsonobj3[that.bankuai[i]['shiti'][j]["user_answer"]['answer']]
                   }                  
                     that.datalist3 = that.bankuai[i]['shiti'];
                      that.datatitle3=that.bankuai[i]["title"]
                   that.score3=that.bankuai[i]["score"]
                 }
                 if(that.shiti_arr[j].type==4){
                   var G = that.shiti_arr[j].answer_options;
                   var jsonobj4 = JSON.parse(G);
                   that.xuanze4 = jsonobj4;
                     that.datalist4 = that.bankuai[i]['shiti'];
                      that.datatitle4=that.bankuai[i]["title"]
                   that.score4=that.bankuai[i]["score"]
                 }
              }
           
            }
           
          }else if((res.data.status=="error")){
            that.$message.error({message:res.data.errormsg,duration:1600});
          }else if((res.data.status=="relogin")){
            that.removeInfo();
          }
        });
    },
     goback (){
       this.$router.push('/my')
    }
     }
};
</script>

<style lang="less" scoped>
.submit {
  width: 100%;
  height: 100%;
  cursor: pointer;
  .w {
    width: 1200px;
    margin: 0 auto;
  }
  .title {
    width: 100%;
    height: 85px;
    line-height: 30px;
    padding-top: 10px;
    box-sizing: border-box;
    background-color: #0169cc;
    color: #fff;
    font-size: 16px;
    cursor: default;
    .fr {
      font-size: 16px;
      .spn1 {
        margin-right: 45px;
      }
    }
  }
  .content {
    margin-top: 20px;
    .leftside1 {
      width: 242px;
      height: 10px;
      margin-right: 10px;
    }
    .leftside {
      width: 242px;
      margin-right: 10px;
      position: fixed;
      top: 100px;
      bottom: 10px;
      overflow-y: auto;
      // left:0;
       .back{
        width: 100%;
        height: 40px;
        line-height: 40px;
        background-color: #0169cc;
        color:#fff;
        text-align: center;
        margin-bottom: 5px;
        font-size:14px;
        .image{
          width: 25px;
        }
       }
      .lefttop {
        background-color: #fff;
        h3 {
          background-color: #e1f1ff;
          height: 53px;
          line-height: 53px;
          color: #111;
          padding-left: 10px;
          box-sizing: border-box;
        }
        p {
          padding-left: 10px;
          box-sizing: border-box;
          font-size: 14px;
          &.p1 {
            padding-top: 27px;
            box-sizing: border-box;
          }
          &.p2 {
            padding-top: 20px;
            padding-bottom: 27px;
            box-sizing: border-box;
          }
        }
      }
      .leftbottom {
        margin-top: 5px;
        background-color: #fff;
        padding-bottom: 20px;
        box-sizing: border-box;
        h3 {
          background-color: #e1f1ff;
          height: 53px;
          line-height: 53px;
          color: #111;
          padding-left: 10px;
          box-sizing: border-box;
        }
        .first {
          width: 100%;
          p {
            font-size: 16px;
            height: 40px;
            line-height: 40px;
            padding-left: 10px;
            box-sizing: border-box;
            margin-top: 10px;
            span {
              font-size: 12px;
            }
          }
          ul {
            margin-top: 10px;
            padding-left: 10px;
            box-sizing: border-box;
            li {
              float: left;
              width: 36px;
              height: 36px;
              margin-right: 10px;
              background-color: #f4f4f4;
              text-align: center;
              line-height: 36px;
			       margin-bottom: 10px;
              &.active {
			        background-color:#fe0000;
              a{
                  color: #fff;	
              }
              }
                &.active1 {
			        background-color:#00cb1c;
              a{
                  color: #fff;	
              }
              }
            }
          }
        }
        .submit {
          margin: 0 auto;
          margin-top: 50px;
          box-sizing: border-box;
          width: 90%;
          height: 40px;
          line-height: 40px;
          text-align: center;
          color: #fff;
          background-color: #0169cc;
        }
      }
      .zong_score{
        width: 100%;
        background-color: #fff;
        margin-top:7px;
        padding:5px 10px;
        box-sizing: border-box;
        p{
          line-height: 30px;
          color:#111; 
            span.score{
              color:#00cb1c;
              font-size: 22px;
           }
           span.square{
             width: 15px;
             height: 15px;
              background-color: #00cb1c;
              margin-top:7px;
              margin-right:5px;
           }
             span.score1{
              font-size: 18px;
           }
           span.square1{
             width: 15px;
             height: 15px;
              background-color: #fe0000;
              margin-top:7px;
              margin-right:5px;
           }
        }
      }
    }
    .big {
      position: relative;
    }
    .right_type1 {
      width: 948px;
      height: 10px;
    }
    .right_type {
      width: 948px;
      background: #fff;
      padding: 20px 10px 20px 10px;
      box-sizing: border-box;
      height: 1000px;
      position: absolute;
      top: -5px;
      left: 242px;
      bottom: 10px;
      overflow-y: scroll;
      .radio_type {
        width: 100%;
        h3 {
          font-weight: bold;
          font-size: 16px;
          letter-spacing: 1px;
          color: #000;
        }
        .radio_con {
          margin-top: 10px;
          color: #1a1a1a;
          .bankuai {
            margin-bottom: 10px;
            .child {
              width: 100%;
              margin-top: 10px;
              li.child_type {
                label {
                  // height: 30px;
                  line-height: 30px;
                  display: block;
                  input {
                    vertical-align: 0px;
                  }
                }
              }
            }
          }
        }
      }
      .check_box {
        width: 100%;
        h3 {
          font-weight: bold;
          font-size: 16px;
          letter-spacing: 1px;
          color: #000;
        }
        .check_con {
          margin-top: 10px;
          color: #1a1a1a;
          .bankuai {
            margin-bottom: 10px;
            .child {
              width: 100%;
              margin-top: 10px;
              li.child_type {
                label {
                  // height: 30px;
                  line-height: 30px;
                  display: block;
                  input {
                    vertical-align: 0px;
                  }
                }
              }
            }
          }
        }
      }
      .text_type {
        width: 100%;
        h3 {
          font-weight: bold;
          font-size: 16px;
          letter-spacing: 1px;
          color: #000;
        }
        .text_con {
          margin-top: 10px;
          color: #1a1a1a;
          .bankuai {
            margin-bottom: 10px;
            .child {
              width: 100%;
              margin-top: 10px;
              li.child_type {
                height: 30px;
                line-height: 30px;
                margin-bottom: 10px;
                width: 200px;
                input {
                  // outline: none;
                  // border:none;
                  border-bottom: 1px solid #000;
                  text-indent: 10px;
                  width: 80%;
                  height: 100%;
                }
              }
            }
          }
        }
      }
      p.jiexi{
        margin-top:10px;
        line-height: 20px;
         span:nth-child(1){
           margin-right: 20px;
         }
      }
      
      
    }
  }
}
</style>
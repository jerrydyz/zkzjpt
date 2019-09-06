<template>
  <div class="kaoshi">
    <div class="title">
      <div class="top w">
        <p class="clearfix">
          <span class="fl">所属课程:&nbsp;&nbsp;&nbsp;{{kecheng_title}}</span>
          <span class="fr">
            考试倒计时:
            <span id="timer" v-html="time"></span>
          </span>
        </p>
        <p class="clearfix">
          <span class="fl" >试卷名称:&nbsp;&nbsp;&nbsp;{{tit}}</span>
          <span
            class="fr"
          >（总分 ：{{score}}分 单选题:{{datalist1.length}}道 多选题: {{datalist2.length}}道 判断题：{{datalist3.length}}道 填空题：{{datalist4.length}}道）</span>
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
              第一大题:{{msg1}}题
              <span>(每题{{score1}}分)</span>
            </p>
            <ul class="clearfix">
              <li :xshiti_id="item1.id" v-for="(item1,index) in datalist1" :key="index"  @click="returnTop(item1.id)">{{index+1}}</li>
            </ul>
          </div>
          <div class="first">
            <p>
              第二大题:{{msg2}}题
              <span>(每题{{score2}}分)</span>
            </p>
            <ul class="clearfix">
			   <li :xshiti_id="item2.id" v-for="(item2,index) in datalist2" :key="index" @click="returnTop(item2.id)">{{index+1}}</li>
            </ul>
          </div>
          <div class="first">
            <p>
              第三大题:{{msg3}}题
              <span>(每题{{score3}}分)</span>
            </p>
            <ul class="clearfix">
			  <li :xshiti_id="item3.id" v-for="(item3,index) in datalist3" :key="index" @click="returnTop(item3.id)">{{index+1}}</li>
            </ul>
          </div>
          <div class="first">
            <p>
              第四大题:{{msg4}}题
              <span>(每题{{score4}}分)</span>
            </p>
            <ul class="clearfix">
			  <li :xshiti_id="item4.id" v-for="(item4,index) in datalist4" :key="index" @click="returnTop(item4.id)">{{index+1}}</li>
            </ul>
          </div>
          <div class="submit" @click="submitpapers">交卷</div>
        </div>
      </div>
      <div class="big">
        <div class="right_type fr">
          <!--单选题  -->
          <div class="radio_type">
            <h3>{{msg1}}题</h3>
            <div class="radio_con">
              <ul>
                <li class="bankuai" v-for="(item,index) in datalist1" :key="index" :id="item.id">
                  <p class="tit_type">{{index+1}}、{{item.question}}</p>
                  <ul class="child shiti_select_div" :shiti_id="item.id" :shiti_type="item.type">
                    <li class="child_type">
                      <label v-for="(key,val) in xuanze" :key="val" @click="radio(item.id)"  style="cursor: pointer;">
                        <input type="radio" :name="['danxuan_'+item.id]" :value="val" style="margin-right:5px" />
                        <span>{{val}}、</span>
                        <span>{{key}}</span>
                      </label>
                    </li>
                  </ul>
                </li>
              </ul>
            </div>
          </div>
          <!-- 多选题 -->
          <div class="check_box">
            <h3>{{msg2}}题</h3>
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
                      <label v-for="(key,val) in xuanze2"  @click="checkbox(check.id)"  style="cursor: pointer;">
                        <input type="checkbox" :name="['duoxuan_'+check.id+'[]']" :value="val" style="margin-right:5px" />
                        <span>{{val}}、</span>
                        <span>{{key}}</span>
                      </label>
                    </li>
                  </ul>
                </li>
              </ul>
            </div>
          </div>
          <!-- 判断题 -->
          <div class="radio_type">
            <h3>{{msg3}}题</h3>
            <div class="radio_con">
              <ul>
                <li class="bankuai" v-for="(dan,index) in datalist3" :key="dan.id" :id="dan.id">
                  <p class="tit_type">{{index+1}}、{{dan.question}}</p>
                  <ul class="child shiti_select_div" :shiti_id="dan.id" :shiti_type="dan.type">
                    <li class="child_type">
                      <label v-for="(key,val) in xuanze3" @click="panduan(dan.id)"  style="cursor: pointer;">
                        <input type="radio" :name="['panduan_'+dan.id]" :value="val"  style="margin-right:5px">
                        <span>{{key}}</span>
                      </label>
                    </li>
                  </ul>
                </li>
              </ul>
            </div>
          </div>
          <!-- 填空题 -->
          <div class="text_type">
            <h3>{{msg4}}题</h3>
            <div class="text_con">
              <ul>
                <li class="bankuai" v-for="(tian,index) in datalist4" :key="tian.id" :id="tian.id">
                  <p class="tit_type">{{index+1}}、{{tian.question}}</p>
                  <ul class="child shiti_select_div" :shiti_id="tian.id" :shiti_type="tian.type">
                    <li class="child_type" v-for="(knum,index) in tian.kong_num" :key="index">
                     <span class="circle"> {{index+1}}</span>
                      <input
                        type="text"
                        :shiti_tiankong="['tiankong_'+tian.id]"
                        @focus="tiankong($event,tian.id)"
                        @blur="tiankongFous($event,tian.id)"
                      />
                    </li>
                  </ul>
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
  name: "kaoshi",
  data() {
    return {
      // 考试用时
      time: "",
      ss: 0,
      mm: 0,
      hh: 0,
      ss_str: "",
      mm_str: "",
      // 试卷信息
      uid: localStorage.getItem("uid"),
      token:  localStorage.getItem("token"),
      name:localStorage.getItem("name"),
      idcord:localStorage.getItem("id_card"),
      shijuanid: "",
      data: [],
      tit: "",
      kecheng_title:"",
      score: "",
      datalist1: [],
      datalist2: [],
      datalist3: [],
      datalist4: [],
      msg1: "", //单选题题目
      score1: "",
      msg2: "", //多选题题目
      score2: "",
      msg3: "", //判断题题目
      score3: "",
      msg4: "", //填空题题目
      score4: "",
      xuanze: {},
      xuanze2: {},
      xuanze3: {},
      xuanze4: {},
      daan: "",
      B: "",
      D: "",
      F: "",
      H: "",
      time_limit_seconds:'',
      timer:''
    };
  },
  created() {
    var that = this;
    this.shijuanid=this.$route.query.shijuan_id
    this.getshijuan();
    var that=this
  },
  computed: {
    
  },
  methods: {
    
    CountDown (){
        
           var maxtime = this.time_limit_seconds;
           var minutes,seconds,msg;
           var that =this;
            this.timer=setInterval(function(){
                 if (maxtime >0) {
                minutes = Math.floor(maxtime / 60);
                seconds = Math.floor(maxtime % 60);
                msg = minutes + "分" + seconds + "秒";
                that.time = msg;
                 maxtime -= 1;
             } else {
              that.$message.error({message:'倒计时结束,自动提交试卷',duration:5000})
               clearInterval(that.timer);
                that.submitpapers()
             }
            },1000)

    },
    
    //获取试卷信息
    getshijuan() {
      var that = this;
      this.$axios
        .post(
          "http://jixujiaoyu_api.songlongfei.club:1012/kaoshi/get_shijuan_info",
          qs.stringify({
            uid: this.uid,
            token: this.token,
            user_shijuan_id: this.shijuanid
          })
        )
        .then(res => {
          console.log("获取试卷信息");
          console.log(res);
          if (res.data.status == "ok") {
            that.score = res.data.data.score;
            that.tit = res.data.data.title;
            that.kecheng_title=res.data.data.kecheng_title
            that.data = that.data.concat(res.data.data.shijuan_bankuai);
            that.time_limit_seconds=res.data.data.time_limit_seconds
            for (var i = 0; i < that.data.length; i++) {
              if (that.data[i].type == 1) {
                that.msg1 = res.data.data.shijuan_bankuai[i].title;
                that.score1 = res.data.data.shijuan_bankuai[i].score;
                that.datalist1 = res.data.data.shijuan_bankuai[i].shiti;
                for (var j = 0; j < that.datalist1.length; j++) {
                  var A = that.datalist1[j].answer_options;
                  that.B = that.datalist1[j].question;
                  var jsonobj = JSON.parse(A);
                  that.xuanze = jsonobj;
                }
              }
              if (that.data[i].type == 2) {
                that.datalist2 = res.data.data.shijuan_bankuai[i].shiti;
                that.msg2 = res.data.data.shijuan_bankuai[i].title;
                that.score2 = res.data.data.shijuan_bankuai[i].score;
                // console.log(that.datalist2);
                for (var k = 0; k < that.datalist2.length; k++) {
                  var C = that.datalist2[k].answer_options;
                  that.D = that.datalist2[k].question;
                  var jsonobj2 = JSON.parse(C);
                  that.xuanze2 = jsonobj2;
                }
              }
              if (that.data[i].type == 3) {
                that.datalist3 = res.data.data.shijuan_bankuai[i].shiti;
                that.msg3 = res.data.data.shijuan_bankuai[i].title;
                that.score3 = res.data.data.shijuan_bankuai[i].score;
                // console.log(that.datalist3);
                for (var h = 0; h < that.datalist3.length; h++) {
                  console.log("=============================================");
                  var E = that.datalist3[h].answer_options;
                  that.F = that.datalist3[h].question;
                  var jsonobj3 = JSON.parse(E);
                  that.xuanze3 = jsonobj3;
                  // console.log("哈哈哈");
                  // console.log(that.xuanze3);
                  // that.tips = that.datalist3[i].tips;
                }
              }
              if (that.data[i].type == 4) {
                that.datalist4 = res.data.data.shijuan_bankuai[i].shiti;
                that.msg4 = res.data.data.shijuan_bankuai[i].title;
                that.score4 = res.data.data.shijuan_bankuai[i].score;
                console.log(that.datalist4);
                for (var f = 0; f < that.datalist4.length; f++) {
                  var G = that.datalist4[f].answer_options;
                  that.H = that.datalist4[f].question;
                  var jsonobj4 = JSON.parse(G);
                  that.xuanze4 = jsonobj4;
                  // console.log(that.xuanze4);
                  // console.log(that.datalist4);
                }
              }
            }
           that.CountDown()
          } else if (res.data.status == "error") {
            that.$message.error({ message: res.data.errormsg, duration: 1600 });
          } else if (res.data.status == "relogin") {
            that.removeInfo();
          }
        });
    },
    //提交试卷
    submitpapers() {
      var that = this;
      clearInterval(that.timer)
      var data = {
        uid: this.uid,
        token: this.token,
        user_shijuan_id: this.shijuanid,
        use_time: this.time,
        answers: this.shouji_input()
      };
      this.$axios
        .post(
          "http://jixujiaoyu_api.songlongfei.club:1012/kaoshi/submit_shijuan",
          data
        )
        .then(res => {
          // console.log("提交考试试卷");
          // console.log(res);
          if (res.data.status == "ok") {
            var kaoshi_id = res.data.data.kaoshi_id;
            this.$router.push({
              path:'/submit',
              query:{
                 kaoshi_id:kaoshi_id,
                //  kecheng_title:that.kecheng_title,
              }
            });
          } else if (res.data.status == "error") {
            that.$message.error({ message: res.data.errormsg, duration: 1600 });
          } else if (res.data.status == "relogin") {
            that.removeInfo();
          }
        });
    },
    //获取单选按钮的值
    radio(id) {
      $("li[xshiti_id='" + id + "']").addClass("active");
    },
    //获取多选按钮的值
    checkbox(id) {
      $("li[xshiti_id='" + id + "']").addClass("active");
    },
    //获取判断的值
    panduan(id) {
      $("li[xshiti_id='" + id + "']").addClass("active");
    },
    //获取填空的值
    tiankong(e, id) {
      if (e.target.value == "") {
        $("li[xshiti_id='" + id + "']").removeClass("active");
      } else {
        $("li[xshiti_id='" + id + "']").addClass("active");
      }
    },
    tiankongFous(e, id) {
      if (e.target.value == "") {
        $("li[xshiti_id='" + id + "']").removeClass("active");
      } else {
        $("li[xshiti_id='" + id + "']").addClass("active");
      }
    },
    //获取答案
    shouji_input() {
      //$(".")
      var json_data = new Array();
      //console.log($(".shiti_select_div"));
      $(".shiti_select_div").each(function(k, v) {
        //alert(k);
        var shiti_id = $(v).attr("shiti_id");
        var shiti_type = $(v).attr("shiti_type");
        switch (shiti_type) {
          case "1": //单选
            var val = $("input[name='danxuan_" + shiti_id + "']:checked").val();
            if (val == undefined) {
              val = "";
            }
            console.log(shiti_id + "=" + val);
            var json_data_item = { shiti_id: shiti_id, answer: val };
            json_data.push(json_data_item);
            break;
          case "2": //多选
            var answer = new Array();
            $('input[name="duoxuan_' + shiti_id + '[]"]:checked').each(
              function() {
                answer.push($(this).val());
              }
            );
            var json_data_item = { shiti_id: shiti_id, answer: answer };
            json_data.push(json_data_item);
            break;
          case "3": //判断
            var val = $("input[name='panduan_" + shiti_id + "']:checked").val();
            if (val == undefined) {
              val = "";
            }
            console.log(shiti_id + "=" + val);
            var json_data_item = { shiti_id: shiti_id, answer: val };
            json_data.push(json_data_item);
            break;
          case "4": //填空
            var answer = new Array();
            $('input[shiti_tiankong="tiankong_' + shiti_id + '"]').each(
              function(kk, vv) {
                answer.push($(vv).val());
              }
            );
            var json_data_item = { shiti_id: shiti_id, answer: answer };
            json_data.push(json_data_item);
            break;
          default:
            console.log("错误类型编号:" + shiti_type);
        }
      });
      //console.log(json_data);
      return json_data;
    },
    goback (){
       this.$router.push('/my')
    },
     returnTop (num){
      document.getElementById(num).scrollIntoView(true);
    }
  },
  destroyed (){
    var that=this
     clearInterval(that.timer)
  }
};
</script>

<style lang="less" scoped>
.kaoshi {
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
               a{
                 color:#111;
               }
              &.active {
			         	background-color: #0169cc;
                   color: #fff;	
           
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
                width:  200px;
                input {
                  // outline: none;
                  // border:none;
                  border-bottom: 1px solid #000;
                  text-indent: 10px;
                  width: 80%;
                  height: 100%;
                }
                .circle{
                  display: inline-block;
                  width: 15px;
                  height: 15px;
                  border-radius: 50%;
                  border:1px solid #000;
                  line-height: 15px;
                  text-align: center;
                  font-size:12px;
                }
              }
            }
          }
        }
      }
    }
  }
}
</style>
<template>
  <div class="checkzhengshu">
    <div class="shuru-box">
        <input type="text" placeholder="请输入姓名" v-model="name">
        <input type="text" placeholder="请输入身份证号" v-model="idcard" maxlength="18">
        <div class="worktimes">
            <select name id @change="gongzuotime" v-model="year">
              <option value="">请选择证书年份</option>
              <option v-for="(item,index) in arrYear" :key="index">{{item}}</option>
            </select>
        </div>
        <div class="btn-find" @click="nowfind">立即查询</div>
    </div>
    <div class="zhengshu-box" v-show="!nodata">
        <div class="pic-box" v-for="(item,index) in zhengshuinfo" :key="index">
            <img class="tupian" :src="item.picurl" alt="">
            <div class="fangda" @click="picfangda(index)">
                <img :src="item.picurl" alt="">
                <div class="graybeijing"><div class="jiahao"></div></div>
            </div>
        </div>
    </div>
    <div class="nodata" v-show="nodata"></div>
    <div class="picscale" v-show="picstate"><img :src="changebase64" alt=""><span @click="closepic"></span></div>
    <div class="my-certificate" ref="certificate">
        <img src="/static/images/personal/certificate.png" class="certificateimg" />
        <div class="certificate-box">
            <p class="certificate-title">
            河南省专业技术人员继续教育网络学习
            <br />合格证明
            </p>
            <div class="info1 info">
            <div class="name-box">
                姓名：
                <span class="name">{{newname}}</span>
            </div>
            <div class="idcard-box">
                身份证号：
                <span class="idcard">{{newidcard}}</span>
            </div>
            </div>
            <div class="info2 info">
            <div class="sex-box">
                性别：
                <span class="sex">{{sex=='1'?"男":'女'}}</span>
            </div>
            <div class="year-box">
                培训年度：
                <span class="year">{{newyear}}年</span>
            </div>
            </div>
            <div class="xueshi">
            该学员参加年度专业技术人员继续教育专业科目的网络学习，通过考核。
            <br />累计完成
            <span class="alltime">{{xueshinum}}</span>学时。
            </div>
            <div class="tczm">特此证明。</div>
            <div class="date" v-html="riqi"></div>
            <div class="blackline"></div>
        </div>
        <img src="/static/images/personal/seal.png" class="seal" />
    </div>
    <div class="my-certificate" ref="certificate2">
        <img src="/static/images/personal/certificate.png" class="certificateimg" />
        <div class="certificate-box">
            <p class="certificate-title">
            河南省专业技术人员继续教育网络学习
            <br />合格证明
            </p>
            <div class="info1 info">
            <div class="name-box">
                姓名：
                <span class="name">{{newname}}</span>
            </div>
            <div class="idcard-box">
                身份证号：
                <span class="idcard">{{newidcard}}</span>
            </div>
            </div>
            <div class="info2 info">
            <div class="sex-box">
                性别：
                <span class="sex">{{sex=='1'?"男":'女'}}</span>
            </div>
            <div class="year-box">
                培训年度：
                <span class="year">{{newyear}}年</span>
            </div>
            </div>
            <div class="xueshi">
            该学员参加年度专业技术人员继续教育专业科目的网络学习，通过考核。
            <br />累计完成
            <span class="alltime">{{xueshinum2}}</span>学时。
            </div>
            <div class="tczm">特此证明。</div>
            <div class="date" v-html="riqi"></div>
            <div class="blackline"></div>
        </div>
        <img src="/static/images/personal/seal.png" class="seal" />
    </div>
  </div>
</template>

<script>
import qs from "qs";
import html2canvas from "html2canvas";
import { Message } from 'element-ui';
export default {
  name: 'checkzhengshu',
  data () {
    return {
      nodata: true,
      arrYear:[],
      year:'',
      name: "",
      idcard: "",
      newyear:'',
      newname: "",
      newidcard: "",
      xueshinum: "",
      xueshinum2: "",
      riqi: "",
      sex: 0,
      zhengshuinfo:'',
      base64:'',
      base64_2:'',
      changebase64:'',
      picstate:false,
      apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
    }
  },
  created(){
    var date = new Date();       
    var thisYear = date.getFullYear();  // 获取当年年份
    var Section = thisYear - 1980;  // 声明一个变量 获得当前年份至想获取年份差
    for(var i = 0;i<=Section;i++){
        this.arrYear.push(thisYear--)
    } // 遍历并添加年份到数组


    this.year = date.getFullYear();
    var month = date.getMonth() + 1;
    var strDate = date.getDate();
    if (month >= 1 && month <= 9) {month = "0" + month; }
    if (strDate >= 0 && strDate <= 9) { strDate = "0" + strDate;}
    var currentdate = this.year + '<span style="margin: 0 7px;">年</span>' +  month + '<span style="margin: 0 7px">月</span>' + strDate + '<span style="margin-left:7px;">日</span>';
    this.riqi = currentdate;

  },
  methods:{
    gongzuotime(e){
        this.year = e.target.value;
        console.log(this.year)
    },
    nowfind(){
        this.newyear=this.year;
        this.newname=this.name;
        this.newidcard=this.idcard;
        if(this.name==''){
            this.$message.error({message:"请输入要查找的姓名",duration:1600});
        }else if(this.idcard==''){
            this.$message.error({message:"请输入要查找的身份证号",duration:1600});
        }else{
            let that=this;
            let userinfo={year:that.year,id_card:that.idcard,name:that.name}
            this.$axios.post(this.apiurl+'/dangan/zhengshu_query',qs.stringify(userinfo) ).then(res=>{
                if(res.data.status=="ok"){
                    if(res.data.data.length==0){
                        this.nodata=true;
                        this.$message.error({message:"未查到相关证书",duration:1600});
                    }else if(res.data.data.length==1){
                        this.nodata=false;
                        that.zhengshuinfo=res.data.data;
                        that.sex=that.zhengshuinfo[0].sex;
                        that.xueshinum = Number(parseFloat(that.zhengshuinfo[0].get_gongxuke_xueshi_num).toFixed(1))+Number(parseFloat(that.zhengshuinfo[0].get_zhuanyeke_xueshi_num).toFixed(1));
                        console.log(that.zhengshuinfo);
                        setTimeout(() => {
                            html2canvas(that.$refs.certificate).then(function(canvas) {
                                that.base64 = canvas.toDataURL("image/jpeg", 1);
                                that.$set(that.zhengshuinfo[0],'picurl',that.base64);
                            });
                        }, 500);
                    }else if(res.data.data.length==2){
                        this.nodata=false;
                        that.zhengshuinfo=res.data.data;
                        that.sex=that.zhengshuinfo[0].sex;
                        that.xueshinum = Number(parseFloat(that.zhengshuinfo[0].get_gongxuke_xueshi_num).toFixed(1))+Number(parseFloat(that.zhengshuinfo[0].get_zhuanyeke_xueshi_num).toFixed(1));
                        that.xueshinum2 = Number(parseFloat(that.zhengshuinfo[1].get_gongxuke_xueshi_num).toFixed(1))+Number(parseFloat(that.zhengshuinfo[1].get_zhuanyeke_xueshi_num).toFixed(1));
                        console.log(that.zhengshuinfo);
                        setTimeout(() => {
                            html2canvas(that.$refs.certificate).then(function(canvas) {
                                that.base64 = canvas.toDataURL("image/jpeg", 1);
                                that.$set(that.zhengshuinfo[0],'picurl',that.base64);
                            });
                        }, 500);
                        setTimeout(() => {
                            html2canvas(that.$refs.certificate2).then(function(canvas) {
                                that.base64_2 = canvas.toDataURL("image/jpeg", 1);
                                that.$set(that.zhengshuinfo[1],'picurl',that.base64_2);
                            });
                        }, 500);
                    }
                    
                }else if((res.data.status=="error")){
                    this.$message.error({message:res.data.errormsg,duration:1600});
                }else if((res.data.status=="relogin")){
                    this.$message.error({message:"重新登录",duration:1600});
                    that.removeInfo();
                } 
            });
        }
        
    },
    //状态为relogin时清除local数据
    removeInfo(){
        localStorage.removeItem("uid");
        localStorage.removeItem("token");
        localStorage.removeItem("sex");
        localStorage.removeItem("name");
        localStorage.removeItem("mobile");
        localStorage.removeItem("id_card");
        localStorage.setItem("types",'rate');
        setTimeout(() => {
            this.$router.push({ path: '/login' });
        }, 1600);
    },
    picfangda(index){
        this.picstate=true;
        if(index==0){
           this.changebase64=this.base64;
        }else if(index==1){
            this.changebase64=this.base64_2;
        }
        

    },
    closepic(){
        this.picstate=false;
    }

  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
    .checkzhengshu{width: 1200px;min-height: 560px;margin: 20px auto;background-color: #fff;overflow: hidden;
        .shuru-box{width: 990px;height: 42px; margin: 36px auto 45px; display: flex;justify-content: space-between;
            input{width: 260px;border: 1px solid #eee;padding-left: 10px;color: #aaa;}
            ::-webkit-input-placeholder { color: #aaa;}
            .worktimes{width: 260px;height: 42px;border: 1px solid #eee;box-sizing:border-box;padding-left: 10px;
                select{width: 100%;height: 100%;color:#aaa;}

            }
            .btn-find{background-color: #189470;width: 160px;color: #fff;text-align: center;line-height: 42px;cursor: pointer;}
        }
        .zhengshu-box{width: 990px;margin:0 auto;
            .pic-box{width: 100%;height: 332px;display: flex;justify-content: space-between;margin-bottom: 45px;
                .tupian{width: 446px;height: 100%;display: block;}
                .fangda{width: 446px;height: 332px;position: relative;
                    .graybeijing{width: 100%;height: 100%;background-color: rgba(0,0,0,.5);position: absolute;left: 0;top:0;
                        .jiahao{width: 103px;height: 103px;background-image: url(/static/images/index/scale.png);position: absolute;left: 50%;top: 50%;margin-left: -51px;margin-top: -51px;
                            
                        }
                    }
                    img{width: 446px;height: 100%; display: block;}
                }
            }
        }
        .nodata {
            width: 212px;
            height: 240px;
            margin: 80px auto 0;
            background-image: url("../assets/nodata.png");
        }
        .picscale{position: absolute;left: 0;top:0;background-color: rgba(0,0,0,.5);width: 100%;height: 146%;
            img{width: 1000px;position: absolute;left: 50%;margin-left: -500px;top:80px;}
            span{width: 60px;height: 60px;background-image: url(/static/images/index/scale.png);transform: rotate(45deg);background-size: contain;position: absolute;bottom: 35%;left: 50%;margin-left: -30px;}
        }
    }
    .my-certificate {
    width: 822px;
    height: 590px;
    position: absolute;
    left: -850px;
    top: -600px;
    overflow: hidden;
    // left: 1200px;
    // top: 100px;
  }
  .certificate-box {
    overflow: hidden;
    width: 100%;
    height: 100%;
    position: absolute;
    left: 0;
    top: 0;
    z-index: 1;
  }
  .certificate-box .certificate-title {
    width: 560px;
    line-height: 42px;
    margin: 0 auto;
    margin-top: 100px;
    font-size: 32px;
    text-align: center;
    color: #48538d;
  }
  .certificate-box .info {
    width: 512px;
    display: flex;
  }
  .certificate-box .info1 {
    margin: 60px auto 0;
  }
  .certificate-box .info2 {
    margin: 25px auto 0;
  }
  .certificate-box div {
    font-size: 18px;
    font-family: "KaiTi";
  }
  .certificate-box div span {
    font-size: 18px;
    font-family: "Microsoft YaHei";
  }
  .certificate-box .name-box {
    width: 220px;
  }
  .certificate-box .sex-box {
    width: 220px;
  }
  .certificate-box .xueshi {
    width: 648px;
    margin: 20px auto 0;
    text-indent: 68px;
    font-size: 18px;
    line-height: 30px;
  }
  .certificate-box .alltime {
    width: 60px;
    height: 30px;
    line-height: 30px;
    font-size: 18px;
    display: inline-block;
    text-indent: 0;
    text-align: center;
  }
  .certificate-box .blackline {
    width: 60px;
    height: 1px;
    background-color: #000;
    position: absolute;
    left: 160px;
    top: 394px;
  }

  .certificate-box .tczm {
    width: 648px;
    margin: 20px auto 0;
    text-indent: 72px;
    font-size: 18px;
  }
  .certificate-box .date {
    position: absolute;
    right: 108px;
    bottom: 75px;
    font-size: 18px;
    font-family: "Microsoft YaHei";
  }
  .certificate-box .date span {
    font-size: 18px;
    font-family: "KaiTi";
  }
  .my-certificate .certificateimg {
    width: 100%;
    height: 100%;
    position: absolute;
    left: 0;
    top: 0;
  }
  .my-certificate .seal {
    position: absolute;
    right: 100px;
    bottom: 90px;
    width: 146px;
  }
</style>

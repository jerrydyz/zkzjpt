<template>
  <div class="examination">
    <div class="title">
      <h3><span>考试记录</span></h3>
    </div>
    <div class="content">
      <div class="nav">
        <span class="spn1">试卷名称</span>
        <span class="spn2">考试时间</span>
        <span class="spn3">分数</span>
        <span class="spn4">总分数</span>
        <span class="spn5">状态</span>
        <span class="spn6">操作</span>
      </div>
      <ul>
        <li v-for="(item,index) in data" :key="index">
            <span class="spn1">{{item.shijuan_title}}</span>
            <span class="spn2">{{item.time}}</span>
            <span class="spn3">{{item.score}}</span>
            <span class="spn4">{{item.zong_score}}</span>
            <span class="spn5 active1"  v-if="item.is_pass=='0'">{{item.is_pass=='0'?'未通过':'考试通过'}}</span>
             <span class="spn5  active" v-else>{{item.is_pass=='0'?'未通过':'考试通过'}}</span>
            <span class="spn6"  @click="todetail(item.id,item.shijuan_title)">查看详情</span>
        </li>
      </ul>
       <div class="nodata" v-show="nodata">
        </div>
        <div class="block" style="text-align:center;margin-top:10px;">
            <el-pagination
                 background
                @current-change="handleCurrentChange"
                :current-page.sync="pageNo"
                :page-size="13"
                layout="total,prev, pager, next, jumper"
                :total="count"
                :pager-count="7"
                v-show="count>13"
                >
            </el-pagination>
        </div>
    </div>
  </div>
</template>

<script>
import qs from 'qs'
export default {
  name: "examination",
  data (){
      return {
            nodata: true,
            uid: "",
            token: "",
            page: 1,
            num: 13,
            data: [],
            apiurl: "http://jixujiaoyu_api.songlongfei.club:1012",
            pageNo: 1,
            currentPage: 1, //当前页数 ，默认为1
            currentPageData: [], //当前页显示内容
            pageSize: 1,
            fenye: true,
            count: 0,
             pagevalue:false,
      }
  },
   watch: {
		token: {
			handler: function(val) {
				if (val) {
            // this.getdata ()
				}else{
           this.removeInfo()
        }
			}
		},deep:true
	},
  created (){
        var that=this
        var date=new Date;
        this.year=date.getFullYear()
        
        this.uid=localStorage.getItem('uid')
        this.token=localStorage.getItem('token')
        if(this.token){
          this.kaoshi ()
    }else{
        this.removeInfo()
    }
  },
  methods:{
      //分页
          handleCurrentChange(val) {
              this.page=val
           console.log(`当前页: ${val}`);
           localStorage.setItem('pagenum',this.page)
           this.kaoshi ()
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
        //获取考试记录
       kaoshi() {
      //获取考试记录
      var that = this;
      var data = {
        page: this.page,
        uid: this.uid,
        token: this.token,
        num: this.num
      };
      this.$axios({
        method: "post",
        url: this.apiurl + "/kaoshi/get_kaoshi_log",
        data: qs.stringify(data)
      }).then(res => {
        console.log(res);
        if (res.data.status == "ok") {
          that.data = [];
          that.data = res.data.data.data;
          that.count = Number(res.data.data.count);
          that.pageSize = res.data.data.pageSize;
          // that.kecheng_title=res.data.data.shijuan_title
          if (res.data.data.data.length == 0) {
            that.nodata = true;
            that.fenye = false;
          } else {
            that.nodata = false;
            that.fenye = true;
          }
        } else if (res.data.status == "relogin") {
          that.removeInfo();
        } else {
          that.$message.error({ message: res.data.errormsg, duration: 1600 });
        }
      });
    },
    //查看详情
    todetail(num,tit) {
      this.$router.push({
        path: "/submit",
        query: {
          kaoshi_id: num,
          // kecheng_title:tit,
        }
      });
    },
       //去学习
        gostady (e,num){
           var that=this
           console.log(e,num)
           if(e.target.innerHTML=="继续学习"){
               that.$emit('more','allCourses')
           }
           if(e.target.innerHTML=="去考试"){
               that.$router.push({
                        path:'/kaoshi',
                        query:{
                            shijuan_id:num
                        }
                    })
           }
       },
       //切换分页
        current_change (currentPage){
           this.currentPage=currentPage
           console.log(currentPage)
       }

  }
};
</script>

<style lang="less" scoped>
.examination{
    width: 948px;
    background-color: #fff;
    padding:0 20px;
          box-sizing: border-box;
    .title{
        width: 100%;
        height: 50px;
        line-height: 50px;
        border-bottom: 1px solid #f4f4f4;
        margin-bottom:20px;
          box-sizing: border-box;
        h3{
            color:#0169cc;
            span{
                padding-bottom:13px;
                border-bottom:2px solid #0169cc;
                box-sizing: border-box;
                cursor: default;
            }
        }
    }
    .content{
        width: 100%;
        height: 640px;
        .nav{
            height: 40px;
            line-height: 40px;
            background-color: #e1f1ff;
            color:#1a1a1c;
             font-size:0;
             text-align: center;
           
            span{
                font-size:14px;
                width: 15%;
                display: inline-block;
                 overflow: hidden;
                    text-overflow:ellipsis;
                    white-space: nowrap;
                    cursor: default;
                &.spn1{
                    width: 20%;
                }
                &.spn2{
                    width: 20%;
                }
                //  &.spn4,&.spn5,&.spn6{
                //     width: 7%;
                // }
            }
        }
        ul{
            li{
            height: 40px;
            line-height: 40px;
            color:#1a1a1c;
             font-size:0;
             text-align: center;
            span{
                font-size:14px;
                width: 15%;
                display: inline-block;
                 overflow: hidden;
                    text-overflow:ellipsis;
                    white-space: nowrap;
                     cursor: default;
                &.spn2{
                    width: 20%;
                }
                &.spn1{
                    width: 20%;
                }
                &.spn6{
                     cursor: pointer;
                }
                &.active{
                    color:#66d668;
                }
                &.active1{
                    color:#ff6767;
                }
                &.active2{
                    color:#006acc;
                }

            }
            &:nth-child(2n){
                 background-color: #f4f4f4;
            }
        }
        }
        .nodata{
                width: 212px;
                height: 240px;
                margin: 80px auto;
                background-image: url('../assets/nodata.png');
            }

    }
}
</style>
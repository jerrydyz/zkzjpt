<template>
  <div class="examination">
    <div class="title">
      <h3><span>参加考试</span></h3>
    </div>
    <div class="content">
      <div class="nav">
        <span class="spn1">年度</span>
        <span class="spn2">试卷名称</span>
        <span class="spn3">所属课程</span>
        <span class="spn4">总分数</span>
         <span class="spn5">公需课学时</span>
        <span class="spn6">专业课学时</span>
        <span class="spn7">状态</span>
        <span class="spn8">操作</span>
      </div>
      <ul>
        <li v-for="item in data" :key="item.id">
            <span class="spn1">{{item.year}}</span>
            <span class="spn2" :title="item.shijuan_info.title">{{item.shijuan_info.title}}</span>
            <span class="spn3" :title="item.kecheng_title">{{item.kecheng_title}}</span>
            <span class="spn4">{{item.shijuan_info.score}}</span>
            <span class="spn5">{{item.gongxuke_xueshi_num}}</span>
            <span class="spn5">{{item.zhuanyeke_xueshi_num}}</span>
            <span class="spn6 active1"  v-if="item.is_pass=='0'">{{item.is_pass=="0"?'未通过':"考试通过"}}</span>
             <span class="spn6 active"  v-else>{{item.is_pass=="0"?'未通过':"考试通过"}}</span>
            <span class="spn7" @click="gostady($event,item.id)">{{item.is_pass==1?'--':item.is_pass==0&&item.enable_kaoshi==0?'继续学习':item.is_pass==0&&item.enable_kaoshi==1?'去考试':'' }}</span>
        </li>
      </ul>
       <div class="nodata" v-show="nodata">
        </div>
        <div class="block" v-show="fenye" style="text-align:right;margin-top:20px;">
                <el-pagination
                    @current-change="handleCurrentChange"
                    :current-page.sync="pageNo"
                    :page-size="11"
                    layout="total,prev, pager, next, jumper"
                    :total="count"
                    :pager-count="7"
                    v-show="count>11"
                    background
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
           nodata:false,
            data:[],
            year:'',
            uid:'',
            token:'',
            page:1,
            currentPage:1,
            apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
            fenye:true,
             page:1,
            num:11,
            count:0,
              pageNo:1,
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
         this.getdata ()
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
           this.getdata ()
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
        //获取数据
        getdata (){
            var that=this
            this.$axios.post(this.apiurl+'/kaoshi/get_shijuan_list',
                qs.stringify({
                    year:this.year,
                    uid:this.uid,
                    token:this.token,
                    page:this.page,
                    num:this.num
                })
            ).then(res =>{
                if(res.data.status=="ok"){
                    console.log("获取考试信息")
                    console.log(res)
                    that.data=[]
                    that.data=that.data.concat(res.data.data.data)
                     that.count=Number(res.data.data.count)
                    console.log(that.data)
                    if(res.data.data.data){
                        that.nodata=false
                    }else{
                        that.nodata=true
                    }
                }else if((res.data.status=="error")){
                    that.$message.error({message:res.data.errormsg,duration:1600});
                }else if((res.data.status=="relogin")){
                    that.removeInfo();
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
            }
        }
    }
    .content{
        width: 100%;
        height: 553px;
        .nav{
            height: 40px;
            line-height: 40px;
            background-color: #e1f1ff;
            color:#1a1a1c;
             font-size:0;
             text-align: center;
           
            span{
                font-size:14px;
                width: 10%;
                display: inline-block;
                 overflow: hidden;
                    text-overflow:ellipsis;
                    white-space: nowrap;
                    cursor: default;
                &.spn2{
                    width: 25%;
                }
                &.spn3{
                    width: 15%;
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
                width: 10%;
                display: inline-block;
                 overflow: hidden;
                    text-overflow:ellipsis;
                    white-space: nowrap;
                     cursor: default;
                &.spn2{
                    width: 25%;
                }
                &.spn3{
                    width: 15%;
                }
                &.spn7{
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
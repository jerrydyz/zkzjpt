<template>

  <div class="news-details">
    <div class="nav-tips">
        <span>您所在的当前位置：</span>
        <span><router-link to="/index" style="color:#000;">首页</router-link></span>
        <span>&gt;<router-link :to="pathurl" style="color:#000;">{{title}}</router-link></span>
        <span>&gt; 新闻详情</span>
    </div>
    <div v-html="msg"></div>
  </div>
</template>

<script>
import qs from 'qs'
export default {
  name: 'newsDetails',
  data () {
    return {
      msg:'',
      pathurl: '',
      title:'',
      //请求接口域名
      apiurl:'http://jixujiaoyu_api.songlongfei.club:1012',
    }
  },
  created(){
    this.title=sessionStorage.getItem("title");
    this.pathurl=sessionStorage.getItem("lujing");
  },
  mounted() {
    let that=this;
    //url里传递过来的新闻唯一id this.$route.params 
    let datanewsid={id:this.$route.params.newsId};
    this.$axios.post(this.apiurl+'/news/get_contents',qs.stringify(datanewsid)).then(response => {
        console.log(response.data);
        console.log(response.data.data.contents);
        if(response.data.status=="ok"){
          that.msg=response.data.data.contents;
        }else if((response.data.status=="error")){
          that.$message.error({message:response.data.errormsg,duration:1600});
        }else if((response.data.status=="relogin")){
          that.removeInfo();
        }
      });
  },
  methods:{
    removeInfo(){
      let that = this;
      that.$message.error({message:"重新登录",duration:1600});
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
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
  .news-details{width: 1200px;margin: 0 auto;min-height: 520px;}
  .news-details .nav-tips{margin-top: 15px;}
  .news-details .nav-tips span{font-size: 16px}
</style>

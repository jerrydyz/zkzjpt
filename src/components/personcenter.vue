<template>
    <div class="personcenter">
        <div class="title">
           <h3><span>学习说明</span></h3>
        </div>
        <div class="content">
            <ul class="clearfix">
                <li>
                    <p><img src="../assets/center01.png" alt=""></p>
                    <h4>(1) 用户注册</h4>
                    <p>新用户请点击首页上的【新学员注册】按钮进行注册。填写时请输入本人真实身份证号，注册完成后即可使用身份证号码登录，初始密码为身份证号码6为，登录后请即使修改</p> 
                </li>
                <li>
                    <p><img src="../assets/center02.png" alt=""></p>
                    <h4>(2) 选课与激活</h4>
                    <p>用户需要获得本年度可用学时说，方可选择对应可是数的课程 (可用学时数可在登陆后【个人中心】页面查看)</p>
                </li>
                <li>
                    <p><img src="../assets/center03.png" alt=""></p>
                    <h4>(3) 在线学习</h4>
                    <p>获得可用学时数后，请在 【全部课程】 或 【课程包】 中选择您要学习的课程，。选课后在 【我的课程中进行学习】。学习次数和市场由系统自动保存。</p> 
                </li>
                <li>
                    <p><img src="../assets/center04.png" alt=""></p>
                    <h4>(4) 在线考试</h4>
                    <p>达到课程在线学习时长要求的学员可在 【参加考试】 页面中随时参加该课程考试，考试结果在试卷提交后立可公布，如未通过，请您认真复习后，再次参加考试。</p>
                </li>
                <li>
                    <p><img src="../assets/center05.png" alt=""></p>
                    <h4>(5) 学时申报</h4>
                    <p>学时申报流程有系统自动完成</p>
                </li>
                <li>
                    <p><img src="../assets/center06.png" alt=""></p>
                    <h4>(6) 记录查询</h4>
                    <p>学院可在 【考试记录】 中查询考试是否通过的记录; 学院可在 【档案记录】 页面中查询学时获得以及证书登记记录;学院可在 【我的订单】 页面中查询课程购买以及小分记录。</p>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
    export default {
        
    }
</script>

<style lang="less" scoped>
.personcenter{
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
         padding-bottom:100px;
         box-sizing: border-box;
         ul{
             width: 100%;
             li{
                 float: left;
                 width: 48%;
                 margin-bottom:15px;
                //  cursor: pointer;
                &:nth-child(2n+1){
                    margin-right:30px;
                }  
                h4{
                    color:#0169cc;
                    line-height: 30px;
                }
                p{
                    line-height: 30px;
                }
             }
         }
    }
     


}

</style>
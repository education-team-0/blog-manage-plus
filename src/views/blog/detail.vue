<template>
  <div style="height: 100%;">
    <el-row style="height: 100%">
<!--      <el-col :offset="1" :span="4" style="height: 100%">-->
<!--        <el-card class="box-card author-card" shadow="hover">-->
<!--          <h1>个人信息</h1>-->
<!--        </el-card>-->
<!--      </el-col>-->

      <el-col :span="18" :offset="3">
        <el-row>
          <el-card class="box-card" shadow="never">
            <el-row style="text-align: left">
              <span><h2>{{ blogInfo.title }}</h2></span>

            </el-row>


            <el-row>
              <el-card class="box-card info-card" shadow="never">
                <!--              <span>个人信息</span>-->
                <el-row>
                  <el-col  :span="3"><span class="greyText">Author: </span>{{blogInfo.author }}</el-col>
                  <el-col :span="7"><span class="greyText">Pubish Date: </span><span>{{blogInfo.publishtime}}</span></el-col>
                  <el-col :span="7"><span class="greyText">Update Date: </span><span>{{blogInfo.updatetime}}</span></el-col>
                  <el-col :span="3"> <span class="readings"><a ><i class="el-icon-view"></i> {{blogInfo.viewfrequency}} </a></span>
                  <span class="likes"><a ><i class="el-icon-star-on"></i> {{blogInfo.collectnum}} </a></span></el-col>
                </el-row>

              </el-card>
            </el-row>
            <el-divider></el-divider>

            <el-row style="margin-top: 20px">
              <div v-html="blogInfo.content" class="markdown-body" style="text-align: left"></div>
            </el-row>
            <el-divider></el-divider>

            <el-row>
            </el-row>

          </el-card>
        </el-row>

        <el-row style="margin-top: 10px">
          <el-card  shadow="never">
<!--            <h1>评论区</h1>-->
<!--            <el-divider></el-divider>-->
            <comment :blog-id="this.id"></comment>
          </el-card>
        </el-row>
      </el-col>

    </el-row>

  </div>
</template>

<script>
import comment from "@/views/blog/components/comment";
import marked from 'marked'
import axios from "axios";
import hljs from "highlight.js"
import 'highlight.js/styles/mono-blue.css'
import 'highlight.js/styles/idea.css'
import 'highlight.js/styles/arta.css'  //不错！
import moment from 'moment'
export default {
  name: "detail",
  components:{
    comment
  },
  data(){
    return{
      detail:'',
      blogInfo:'',
      id:''
    }

  },
  created() {
    const id = this.$route.params && this.$route.params.id
    this.id=id
    this.updateView()
    // console.log(this.$route)
    // console.log(this.$route.params)
    this.fetchData(id)
    console.log(this.blogInfo)


  },
  mounted() {
    marked.setOptions({
          renderer: new marked.Renderer(),
          highlight: function(code) {
            return hljs.highlightAuto(code).value;
          },
          pedantic: false,
          gfm: true,
          tables: true,
          breaks: false,
          sanitize: false,
          smartLists: true,
          smartypants: false,
          xhtml: false,

        }
    );
    }
  ,
  methods:{

    fetchData(id){
      //请求后端接口获取数据
      var url="/blog/getBlogById?id="+id;
      axios.get(url)
          .then(
              response => {
                console.log(response)
                this.blogInfo=response.data.data
                this.blogInfo.content=marked(this.blogInfo.content)
                var date1=new Date(this.blogInfo.publishtime);
                this.blogInfo.publishtime=moment(date1).format('YYYY-MM-DD HH:mm:ss')//date1.toLocaleString().slice(0,10)+' '+date1.toLocaleString().slice(13)
                var date2=new Date(this.blogInfo.updatetime);
                this.blogInfo.updatetime=moment(date2).format('YYYY-MM-DD HH:mm:ss')
              }
          )
    },
    Out(){
      console.log("Click!")
    },
    updateView(){
      var url='/blog/updateView?blogid='+this.id
      axios.get(url) .then(
          response => {
           console.log(response);
          }
      )
    }


  },

}
</script>

<style scoped>
@import "components/css/markdown.css";
@import "https://cdn.bootcss.com/github-markdown-css/2.10.0/github-markdown.min.css";
.info-card{
  background-color: #f8f8f8;
  margin-top: 10px;
}
.author-card{
  height: 80%;
  background-color: #FFFFFF;
}
.blog-foot{
  background-color: #FFFFFF;
  margin-top: 20px;
}
.greyText{
  color: #999aaa;
}

</style>
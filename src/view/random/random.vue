<template>
  <div>
    <Row style="margin: 0 auto;width:80%">
      <Input v-model.trim="Meme.memename" :search="true" enter-button @on-search="searchMeme" @keyup.enter="searchMeme" placeholder="大家都在搜..."  size="large" style="margin-top: 15px;"/>
    </Row>
    <Row style="margin: 0 auto;width:80%">
      <Card shadow style="margin-top: 30px;min-height:400px; height:auto;">
        <p slot="title">
          {{Meme.memename}}
        </p>
        <a href="#" slot="extra" @click.prevent="submitTran(Meme.memename)" title="我来提交对应文字">
          <Icon type="md-add" size="27" color="grey"></Icon>
        </a>
        <div>
          {{Meme.memetext}}

        </div>
        <img :src="'http://localhost:3000/'+Meme.memeimgurl" onerror="this.src='http://localhost:3000/images/Space.gif'" style="display: block;margin-top: 50px">
      </Card>
    </Row>
    <Row :gutter="20" style="margin: 0 auto;width:80%">
      <i-col :md="24" :lg="12" style="margin-top: 150px;margin-bottom: 30px;">
        <RandomBox></RandomBox>
      </i-col>
      <i-col :md="24" :lg="12" style="margin-top: 150px;margin-bottom: 30px;">
        <RandomBox></RandomBox>
      </i-col>
    </Row>
    <Row :gutter="20" style="margin: 0 auto;width:80%">
      <i-col :md="24" :lg="12" style="margin-bottom: 30px;">
        <RandomBox></RandomBox>
      </i-col>
      <i-col :md="24" :lg="12" style="margin-bottom: 30px;">
        <RandomBox></RandomBox>
      </i-col>
    </Row>
    <Row :gutter="20" style="margin: 0 auto;width:80%">
      <i-col :md="24" :lg="12" style="margin-bottom: 30px;">
        <RandomBox></RandomBox>
      </i-col>
      <i-col :md="24" :lg="12" style="margin-bottom: 30px;">
        <RandomBox></RandomBox>
      </i-col>
    </Row>
    <Row :gutter="20" style="margin: 0 auto;width:80%">
      <i-col :md="24" :lg="12" style="margin-bottom: 30px;">
        <RandomBox></RandomBox>
      </i-col>
      <i-col :md="24" :lg="12" style="margin-bottom: 30px;">
        <RandomBox></RandomBox>
      </i-col>
    </Row>
  </div>
</template>

<script>
  import axios from 'axios';
  import RandomBox from '../random-box.vue'

  const submitTran = name=>{
    let text = prompt('输入词条对应文字 末尾可通过括号包裹（简略注明来源）','');

    if(!text || !text.trim || !text.trim()){
      return;
    }
    var qs = require('qs');
    // post 数据
    let postData = { name: name, text: text }
    axios.post('http://localhost:3000/addUserMemes', qs.stringify(postData),
      {headers: {'Content-Type': 'application/x-www-form-urlencoded'}}).then((res)=>{
      console.log(res.data);
    }).catch(error => {
      // 请求失败
      console.log(error)
    });
  };

  export default {
    name: "random",
    components: {
      RandomBox
    },
    data () {

      return {

        // 词条变量
        Meme: {
          //词条变量id
          id: "",
          // 词条变量名称
          memename: "",
          // 词条变量释义内容
          memetext: "",
          // 词条变量标签分类
          memetag: "",
          // 词条变量图片
          memeimgurl: ""
        },


      }
    },

    methods: {
      // 查询词条
      searchMeme () {
        // 正则表达式：只能输入中文、英文字母、数字
        var reg = /^[A-Za-z0-9\u4e00-\u9fa5\.]+$/;
        // 判断输入是否为空
        if (this.Meme.memename==null) {
          alert("请输入词条再查询");
        }
        // 判断输入内容是否非法
        else if (!reg.test(this.Meme.memename)){
          alert("输入内容存在非规范字符，请输入中文、数字或英文字母的组合");
        }
        else {
          var that = this;
          this.Meme.memetext = null;
          // ？前加入接口路径,根据?Memename="+this.Meme.memename获取对应词条接口
          axios.get('http://localhost:3000/search',{
            params: {
              name: this.Meme.memename
            }
          })
            .then(function(res){
                // 获取接口词条
                for(var item in res.data.data[0]){
                  that.Meme[item]=res.data.data[0][item];
                }

                console.log(res);
                console.log(that.Meme);
              },
              function(err){
                alert("请求查询错误");
              })
        }
      },
      randomLoad(){
        var that = this;
        axios.get("http://localhost:3000/random").then
          // 获取成功执行如下
          (function(res){
              // 获取词条
              for(var item in res.data.data[0]){
                that.Meme[item]=res.data.data[0][item];
              }

              console.log(res);
              console.log(that.Meme);
            },
            // 获取失败执行如下
            function (err) {
              alert("获取词条失败");
            })
      },
      //表单提交函数
      submitTran

    },

    mounted: function () {
      this.randomLoad();
    }
  }
</script>

<style scoped>

</style>


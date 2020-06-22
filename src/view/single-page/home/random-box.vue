<template>
  <Card>
    <p slot="title">
      {{ RandomMeme.memename }}
    </p>
    <a href="#" slot="extra" @click.prevent="refresh">
      <Icon type="ios-refresh" size="27" color="grey"></Icon>
    </a>
    <p>
      {{ RandomMeme.memetext }}
      <img :src="'http://localhost:3000/'+RandomMeme.memeimgurl" onerror="this.src='http://localhost:3000/images/None.gif'"  style="display: block; margin-top: 25px ">
    </p>

  </Card>
</template>

<script>
  import axios from 'axios'

  export default {
    name: "RandomBox",

    data () {
      return {
        RandomMeme: {
          //随机词条id
          id:"",
          // 随机词条变量名称
          memename:"",
          // 随机词条变量释义内容
          memetext:"",
          // 随机词条变量标签分类
          memetag: "",
          // 随机词条变量图片
          memeimgurl: ""
        },

      }

    },

    methods: {
      getRandomMeme(){
        //获取接口，该接口为随机获取一个词条
        axios.get("http://localhost:3000/random").then
          // 获取成功执行如下
          (function(res){
              // 获取随机词条
              for(var item in res.data.data[0]){
                this.RandomMeme[item]=res.data.data[0][item];
              }
              console.log(res);
              console.log(this.RandomMeme);
            },
            // 获取失败执行如下
            function (err) {
              alert("获取词条失败");
            })
      },


      refresh () {
        var that = this;
        //获取接口，该接口为随机获取一个词条
        axios.get("http://localhost:3000/random").then
          // 获取成功执行如下
          (function(res){
              // 获取随机词条
              for(var item in res.data.data[0]){
                that.RandomMeme[item]=res.data.data[0][item];
              }

              console.log(res);
              console.log(that.RandomMeme);
            },
            // 获取失败执行如下
            function (err) {
              alert("获取词条失败");
            })
      }
    },

    mounted: function () {
      this.refresh();
    }

  }

</script>

<style scoped>

</style>


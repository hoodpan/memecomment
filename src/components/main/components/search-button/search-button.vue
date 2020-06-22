<template>
  <div>
    <Select
      v-model="model13"
      filterable
      remote
      style="width:150px"
      :clearable="true"
      placeholder="请输入关键字搜索"
      :remote-method="remoteMethod1"
      :loading="loading3">
      <Option v-for="(item, index) in user" :value="item.Id" :key="index" style="width: 80px">{{item.name}}</Option>
    </Select>
  </div>
</template>

<script>
    import axios from "axios";

    export default {
        name: "SearchButton",
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
      methods:{
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
      }


    }

</script>

<style scoped>

</style>

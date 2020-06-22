<template xmlns:width="http://www.w3.org/1999/xhtml">
  <div>
    <Row style="margin: 0 auto;width:80%">
      <label>
        <Input v-model.trim="Meme.memename" :search="true" enter-button @on-search="searchMeme(Meme.memename)" @keyup.enter="searchMeme(Meme.memename)" placeholder="大家都在搜..."  size="large" style="margin-top: 15px;"></Input>
      </label>
    </Row>

    <Row style="margin: 0 auto;width:80%">
      <Card shadow style="margin-top: 30px;min-height:400px; height:auto;">
        <div>
          <p>{{Meme.memetext}}</p>
          <img :src="'http://localhost:3000/'+Meme.memeimgurl" onerror="this.src='http://localhost:3000/images/None.gif'" style="display: block;margin-top: 50px">
        </div>

        <!-- 提交表单_div -->
        <div class="FormFade" v-if="FormShow">
          <p style="text-align: center">尚未收录的梗，可以由你来提交</p>
          <br>
          <p>MemeText为必填项，图片最多选取一张且大小不超过2M，格式支持.jpg/.jpeg/.png/.gif</p>
          <br>
          <Form ref="FormMeme" :model="FormMeme" label-position="left" :label-width="100"> <!--:rules="FormRules"-->
            <FormItem label="MemeName">
              <Input v-model="FormMeme.memename" disabled type="text"></Input>
            </FormItem>
            <FormItem label="MemeTag">
              <Select v-model="FormMeme.memetag">
                <Option value="Character">人物</Option>
                <Option value="Entertainment">娱乐</Option>
                <Option value="Anime">动漫</Option>
                <Option value="Game">游戏</Option>
                <Option value="Film">电影</Option>
                <Option value="Other">不清楚选什么</Option>
              </Select>
            </FormItem>
            <FormItem label="MemeText">
              <Input v-model="FormMeme.memetext" type="textarea" :rows=4></Input>
            </FormItem>
            <FormItem label="MemeImage">
              <div class="demo-upload-list" v-for="item in uploadList">
                <div v-if="item.status === 'finished'">
                  <img :src="item.url">
                  <div class="demo-upload-list-cover">
                    <Icon type="ios-eye-outline" @click.native="handleView(item.url)"></Icon>
                    <Icon type="ios-trash-outline" @click.native="handleRemove(item)"></Icon>
                  </div>
                </div>
                <div v-else>
                  <Progress v-if="item.showProgress" :percent="item.percentage" hide-info></Progress>
                </div>
              </div>
              <Upload
                ref="upload"
                :show-upload-list="false"
                :default-file-list="uploadList"
                paste
                type="drag"
                :format="['jpg','jpeg','png','gif']"
                :max-size="2048"
                action="//localhost:3000/image/upload"
                :on-format-error="handleFormatError"
                :on-exceeded-size="handleMaxSize"
                :before-upload="handleBeforeUpload"
                :on-success="handleSuccess"
                style="display: inline-block;width:58px;">
                <div style="width:58px;height:58px;line-height:58px;">
                  <Icon type="ios-camera" size="20"></Icon>
                </div>
              </Upload>
              <Modal title="View Image" v-model="visible">
                <img :src="imgurl" v-if="visible" style="width:100%">
              </Modal>
            </FormItem>
            <FormItem>
              <Button type="primary" @click.prevent="FormSubmit(FormMeme)" style="margin-right:20px">确认提交</Button>
              <Button @click.prevent=FormShowChange>放弃提交</Button>
            </FormItem>
          </Form>
        </div>
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
  import RandomBox from './random-box.vue'

  export default {
    name: 'home',
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

        //表单显示状态
        FormShow: false,
        //提交表单数据
        FormMeme: {
          // 词条变量名称
          memename: "eee",
          // 词条变量释义内容
          memetext: "",
          // 词条变量标签分类
          memetag: "",
          // 词条变量图片
          memeimgurl: "",
        },

        imgurl: '',
        visible: false,
        uploadList: [],
      }
    },
    methods: {

      // 查询词条
      searchMeme (name) {
        // 正则表达式：只能输入中文、英文字母、数字
        const reg = /^[A-Za-z0-9\u4e00-\u9fa5\.]+$/;
        // 判断输入是否为空
        if (this.Meme.memename==='') {
          alert("请输入词条再查询");
        }
        // 判断输入内容是否非法
        else if (!reg.test(this.Meme.memename)){
          alert("输入内容存在非规范字符，请输入中文、数字或英文字母的组合");
        }
        else {
          //接收数据时，this指针指向会丢失，所以定义一个that指针记录this，保证指针指向当前组件
          let that = this;
          // ？前加入接口路径,根据?Memename="+this.Meme.memename获取对应词条接口
          axios.get('http://localhost:3000/search',{
            params: {
              name: that.Meme.memename
            },
          }).then(function(res){
            // console.log(res);
            if (res.data.data.length===0) {
              //返回查询结果未找到,res.data.data为空,即res.data.data.length===0

              alert("数据库中未找到该词条");
              //获取name用于记录、显示表单memename
              that.FormMeme.memename = name;
              //显示表单
              that.FormShow = true;
              //console.log(that.Meme);
            }
            else if (res.data.code===404) {
              //  后端传输错误
              alert("数据库崩溃了，查询失败");
            }
            else {
              // 获取接口词条
              for(let item in res.data.data[0]){
                that.Meme[item]=res.data.data[0][item];
              }
              //console.log(that.Meme);
            }
          },function(err){
            alert("请求查询错误");
          })
        }
      },

      //提交表单函数
      FormSubmit (FormMeme) {
        if (FormMeme.memetext==='') {
          this.$Notice.warning({
            title: 'memetext未填内容'
          });
        }
        else {
          // console.log(FormMeme);
          axios.get('http://localhost:3000/addmemes', {
              params:{
                name:FormMeme.memename,
                text:FormMeme.memetext,
                tag:FormMeme.memetag,
                imgurl:FormMeme.memeimgurl,
              }
            }
          ).then((res)=>{
            // console.log(res);
            if (res.data.code===0) {
              this.$Message.success('感谢你的提供，请期待该梗的出现');
            }
            else if(res.data.code===404) {
              this.$Notice.error({
                title: '太可惜了，梗添加失败'
              });
            }
            else {
              this.$Notice.error({
                title: '很抱歉，后端遇到不明错误，梗在去数据库的路上出错'
              });
            }
          }).catch(error => {
            // 请求失败
            // console.log(error);
            alert("请求添加错误");

            // //为实现再次尝试提交
            // if(confirm("‘" + FormMeme.memename + "’ 在去数据库的路上出错，是否放弃提交并关闭表单?\n\n选择'确认'->放弃提交,关闭表单\n选择'取消'->重新提交")) {
            // }
            // else {
            //   this.FormSubmit (FormMeme);
            // }

          });
          this.FormShow = false;
          //致表单初始化，内容为空
          this.FormMeme.memename = "";
          this.FormMeme.memetext = "";
          this.FormMeme.memetag = "";
          this.FormMeme.memeimgurl = "";
          this.uploadList = [];
        }
      },

      //放弃提交表单函数
      FormShowChange() {
        if(confirm('确定要关闭提交表单吗?')) {
          this.FormShow = false;
          //致表单初始化，内容为空
          this.FormMeme.memename = "";
          this.FormMeme.memetext = "";
          this.FormMeme.memetag = "";
          this.FormMeme.memeimgurl = "";
          this.uploadList = [];
        }
      },

      //预览功能
      handleView (url) {
        this.imgurl = url;
        this.visible = true;
      },

      //删除功能
      handleRemove (file) {
        // console.log(file);
        let that = this;
        axios.get('http://localhost:3000/image/delete',{
          params:{
            name: file.name
          }
        }).then(function(res){
          //删除成功，置预览列表为空
          // console.log("删除成功");
          // console.log(res);
          that.uploadList = [];
        },function(err){
          that.$Notice.error({
            title: '删除上传图片失败'
          });
        });
      },

      //上传图片成功后，获取返回图片信息，以便进行预览、删除操作
      handleSuccess (res, file) {
        // console.log('图片进入文件夹，信息如下');
        // console.log(file);
        // console.log(res);
        if(res.code==="0"){
          //获取图片信息，压入uploadList进行展示
          let pic = {};
          pic.name=res.data.name;
          pic.url="http://localhost:3000/"+res.data.url;
          pic.status=file.status;
          this.uploadList.push(pic);
          //表单录入图片信息
          this.FormMeme.memeimgurl = res.data.url;
        }
        else{
          this.$Notice.error({
            title: "上传图片失败"
          });
        }
        // console.log(this.uploadList);
      },

      //上传图片时，判断并限制已上传数量
      handleBeforeUpload () {
        const check = this.uploadList.length < 1;
        if (!check) {
          this.$Notice.warning({
            title: '上传图片只能为一张'
          });
        }
        return check;
      },

      //选择文件时，判断文件后缀名
      handleFormatError (file) {
        this.$Notice.warning({
          title: '文件类型不正确',
          desc: '文件：' + file.name + ' 格式非jpg 、jpeg 、png 、gif'
        });
      },

      //选择图片时，判断图片大小
      handleMaxSize (file) {
        this.$Notice.warning({
          title: '文件大小限制',
          desc: '文件：' + file.name + ' 大小超过2M'
        });
      },
    }
  }
</script>

<style lang="less">
  .count-style{
    font-size: 50px;
  }

  /*上传图片组件Upload样式*/
  .demo-upload-list{
    display: inline-block;
    width: 60px;
    height: 60px;
    text-align: center;
    line-height: 60px;
    border: 1px solid transparent;
    border-radius: 4px;
    overflow: hidden;
    background: #fff;
    position: relative;
    box-shadow: 0 1px 1px rgba(0,0,0,.2);
    margin-right: 4px;
  }
  .demo-upload-list img{
    width: 100%;
    height: 100%;
  }
  .demo-upload-list-cover{
    display: none;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background: rgba(0,0,0,.7);
  }
  .demo-upload-list:hover .demo-upload-list-cover{
    display: block;
  }
  .demo-upload-list-cover i{
    color: #fff;
    font-size: 20px;
    cursor: pointer;
    margin: 0 2px;
  }
</style>

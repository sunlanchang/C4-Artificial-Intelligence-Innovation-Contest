<template>
  <div id="myaudio">
    <div>
      <!-- 参考：https://element.eleme.cn/#/zh-CN/component/upload -->
      <el-upload
        class="upload-demo"
        name="audio"
        action="http://localhost:5000/test"
        accept=".wav, .mp3"
        :on-change="handleChangeNew"
        :file-list="fileList"
        :on-success="getResponse"
      >
        <el-button slot="trigger" size="small" type="primary">点击上传并视频、图片识别</el-button>
        <div slot="tip" class="el-upload__tip">仅支持jpg、png、mp4格式上传</div>
      </el-upload>
    </div>

    <el-input
      :disabled="true"
      type="textarea"
      autosize
      placeholder="这里显示视频识别结果..."
      v-model="response_en_sentence"
    ></el-input>
    <!-- <el-input
      :disabled="true"
      type="textarea"
      autosize
      placeholder="这里显示翻译结果..."
      v-model="response_zh_sentence"
    ></el-input>-->
  </div>
</template>

<script>
const axios = require("axios");

export default {
  name: "myaudio",
  data: function() {
    return {
      fileToUpload: "",
      response_en_sentence: "",
      response_zh_sentence: "",
      fileList: [
        {
          name: "photo.jpg",
          url:
            "https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100"
        }
        // {
        //   name: "food2.jpeg",
        //   url:
        //     "https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100"
        // }
      ]
    };
  },
  methods: {
    //input标签控制选择文件
    handleChangeOld() {
      console.log("handleChangeOld, 文件改变");
      this.fileToUpload = this.$refs.file.files[0];
      // console.log(this.fileToUpload);
    },
    //em-button控制选取文件
    handleChangeNew(file, fileList) {
      console.log("handleChangeNew, 文件改变");
      console.debug(file);
      console.debug(fileList);
    },
    getResponse(response) {
      this.response_en_sentence = response["en_sentence"];
      this.response_zh_sentence = response["zh_sentence"];
      console.log(this.response_en_sentence);
      console.log(this.response_zh_sentence);
    },
    //input标签和em-button共用的提交服务器函数
    submitUpload() {
      let formData = new FormData();
      formData.append("audio", this.fileToUpload);

      function getDataPromise() {
        return axios
          .post("http://localhost:5000/test", formData, {
            headers: {
              "Content-Type": "multipart/form-data"
            }
          })
          .then(function(res) {
            console.log("SUCCESS!!");
            return res.data;
          })
          .catch(function(err) {
            return console.error(err);
          });
      }
      //关键的一步！！！！！
      // https://stackoverflow.com/questions/48786445/stumped-how-do-i-get-data-in-vue-from-axios-to-display
      var self = this;
      getDataPromise().then(function(response) {
        self.class_probs = response;
        console.log("后端返回的response：", response);
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
.inputfile {
  width: 0.1px;
  height: 0.1px;
  opacity: 0;
  overflow: hidden;
  position: absolute;
  z-index: -1;
}
.inputfile + label {
  font-size: 1.25em;
  font-weight: 700;
  color: white;
  /* background-color: black; */
  background-color: #c9c7c7;
  display: inline-block;
}

.inputfile:focus + label,
.inputfile + label:hover {
  background-color: rgb(55, 173, 112);
  /* background-color: white; */
}
.inputfile + label {
  cursor: pointer; /* "hand" cursor */
}
/* h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
} */
</style>

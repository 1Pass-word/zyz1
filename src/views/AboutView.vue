<template>
  <div>
    <h3>作业1：</h3>
    <input type="text" v-model="nams"><br>
    <input type="text" v-model="role"><br>
    打印结果：{{ jieguo }}
    <hr>
    <h3>作业2：</h3>
    <div class="box">
      <input type="file" ref="fileInput" @change="handleFileChange" />
      头像:<img style="width: 50px;height: 50px;" :src="imageUrl" alt="" v-if="imageUrl">
      <br>
      名字:<el-input v-model="names" class="el-input" placeholder="请输入内容"></el-input><br><br>
      ID:<el-input v-model="userid" class="el-input" placeholder="请输入id"></el-input><br><br>
      性别:<el-radio v-model="sex" label="0">男</el-radio>
      <el-radio v-model="sex" label="1">女</el-radio><br><br>
      手机号:<el-input v-model="iphone" class="el-input" placeholder="请输入电话"></el-input><br><br>
      <textarea name="" id="" v-model="mydes" cols="30" rows="10"></textarea><br><br>
      <el-button type="primary" @click="add()">确定添加</el-button>
      <el-button @click="qu()">取消添加</el-button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import md5 from 'md5';
export default {
  data() {
    return {
      nams: '12123',
      role: '123',
      jieguo: '',
      file: null,
      previewImage: '',
      imageUrl: '',
      imageFile: null,
      names: '',
      userid: '',
      sex: 1,
      iphone: '',
      mydes: '',
      img: ''
    }
  },
  methods: {
    add() {
      let arr = {
        name: this.names,
        userid: this.userid,
        sex: this.sex,
        iphone: this.iphone,
        mydes: this.mydes,
        img: this.imageUrl
      }
      axios.post("https://www.zzgoodqc.cn/index.php/index/index/mycenter", arr).then(res => {
        console.log(res.data);
        console.log(arr)
        if (res.data.code == 0) {
          this.$message({
            message: '恭喜你，添加成功',
            type: 'success'
          });
        } else {
          this.$message.error(res.data.msg);
        }

      })
    },
    handleFileChange(event) {
      const file = event.target.files[0];
      const reader = new FileReader();
      reader.onload = (e) => {
        const base64Data = e.target.result;
        const formData = new FormData();
        formData.append('imgurl', base64Data);
        axios.post('https://www.zzgoodqc.cn/index.php/index/upload/uploadimg', formData)
          .then(res => {
            const imagePath = res.data.data.url;
            // 拼接接口域名或IP
            this.imageUrl = `https://www.zzgoodqc.cn/${imagePath}`;
            console.log(this.imageUrl);
          })
          .catch(error => {
            console.error(error);
            // 根据需要处理错误
          });
      }
      reader.readAsDataURL(file);
    },

  },



  created() {
    let res = md5(this.nams + this.role + 'nbsp123ok' + 12)
    let obj = {
      name: this.nams,
      role: this.role,
      sign: res
    }
    axios.post('https://www.zzgoodqc.cn/index.php/index/callcenter/getheaders', obj, {
      headers: {
        'strheader': '12',
      }
    })
      .then(response => {
        this.jieguo = response.data
      })
      .catch(error => {
        console.error(error);
      });
  },
  computed: {
    fullImageUrl() {
      return this.imageUrl;
    }
  },
}
</script>

<style>
.avatar-uploader .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.box {
  width: 500px;
  height: 500px;
  border: 1px solid black;
  margin: auto;
  padding: 50px;
}

.el-input {
  width: 300px;
}

.avatar-uploader .el-upload:hover {
  border-color: #409EFF;
}

.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 178px;
  height: 178px;
  line-height: 178px;
  text-align: center;
}

.avatar {
  width: 178px;
  height: 178px;
  display: block;
}

img {
  width: 100px;
}
</style>
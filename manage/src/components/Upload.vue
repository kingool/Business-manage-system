<template>
  <div class="upload">
    <div class="upload-box" v-if="!disabled">
      <label class="upload-label">
        <input class="file" type="file" @change="fileData">
      </label>
      <div class="img-box" v-if="url != ''">
        <img class="auto-img" :src="url" alt="">
      </div>
      <div class="icon-box" v-else>
        <img class="auto-img" src="../assets/images/add.png" alt="">
      </div>
      <MsgBox></MsgBox>
    </div>
    <div class="upload-slot" v-if="id == 1 || id == 3">
      <slot></slot>
    </div>
  </div>
</template>

<script>
  import MsgBox from '../components/MsgBox.vue'

  export default {
    name: 'Upload',

    props: {
      disabled: {
        type: Boolean,
        default: false
      }, 
      id: {
        type: [Number, String],
        default: 2
      },
      size: {
        //属性值为数值类型
        type: Number,

        //默认为1MB
        default: 1
      }
    },

    data() {
      return {
        url: ''
      };
    },

    methods: {
      fileData(e) {

        let self = this;

        let file = e.target.files[0];

        // console.log('file ==> ', file);

        //控制上传图片大小
        //file.size: 单位为B(字节)
        let fileSize = file.size / 1024 / 1024;
        // console.log('fileSize ==> ', fileSize);

        if (fileSize > this.size) {

          // self.$emit('file-upload', {fileSize});

          this.$showToast({
            message: '上传图片不能超过' + this.size + 'MB',
            duration: 3000
          })

          return;
        }

        //将文件信息转换成base64

        //创建文件读取对象
        let fileReader = new FileReader();

        //监听文件读取对象是否读取完毕文件
        fileReader.onload = function (e) {
          // console.log('this.result ==> ', this.result);

          self.url = this.result;

          //触发上传组件自定义事件
          self.$emit('file-upload', {base64: this.result, type: file.type.split('/')[1]});
        }

        //读取
        fileReader.readAsDataURL(file);

      }
    },

    components: {
      MsgBox
    }
  }
</script>

<style lang="less" scoped>
  .upload{
    position: relative;
    width: 160px;
    height: 160px;
    background-color: #f2f2f2;
  }
  .upload-slot{
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    z-index: 1;
    background-color: #fff;
    border: 2px dashed #ddd;
    // pointer-events: none;
  }
  .upload-box{
    width: 100%;
    height: 100%;
    border: 2px dashed #ddd;
    position: relative;
    z-index: 2;
  }

  .upload-label{
    display: block;
    width: 100%;
    height: 100%;
    position: relative;
    z-index: 2;
  }

  .file{
    display: none;
  }

  .img-box{
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    z-index: 1;
  }

  .icon-box{
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    width: 50px;
    height: 50px;
    z-index: 1;
  }
</style>
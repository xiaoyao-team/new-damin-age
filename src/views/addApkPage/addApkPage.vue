<template>
  <div>
    <el-card>
      <div slot="header">
        <el-page-header @back="goBack" title="返回" content="新增落地页参数"></el-page-header>
      </div>
      <el-form ref="addForm" :model="addForm" label-width="150px">
        <el-form-item
          label="游戏"
          prop="appId"
          :rules="{ required: true, message: '请选择所属游戏', trigger: 'change'}"
        >
          <el-select v-model="addForm.appId" placeholder="请选择">
            <el-option
              v-for="item in appList"
              :key="item.value"
              :label="item.appName"
              :value="item.appId"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="页面名称" prop="pageTitle" :rules="{ required: true, message: '页面名称不能为空'}">
          <el-input v-model="addForm.pageTitle" />
        </el-form-item>
        <el-form-item label="生成路径" prop="filePath" :rules="{ required: true, message: '路径不能为空'}">
          <el-input v-model="addForm.filePath" />
        </el-form-item>
        <el-form-item
          label="网页title"
          prop="webTitle"
          :rules="{ required: true, message: '网页title不能为空'}"
        >
          <el-input v-model="addForm.webTitle" />
        </el-form-item>
        <el-form-item
          label="下载按钮链接"
          prop="buttonLink"
          :rules="{ required: true, message: '下载链接不能为空'}"
        >
          <el-input v-model="addForm.buttonLink" />
        </el-form-item>
        <el-form-item
          label="下载按钮文本"
          prop="buttonTxt"
          :rules="{ required: true, message: '按钮文本不能为空'}"
        >
          <el-input v-model="addForm.buttonTxt" />
        </el-form-item>
        <!-- <el-form-item
          label="对应页面地址"
          prop="address"
          :rules="{ required: true, message: '对应页面地址不能为空'}"
        >
          <el-input v-model="addForm.address" />
        </el-form-item> -->
        <el-form-item label="版本说明title">
          <el-input v-model="addForm.versionTitle" />
        </el-form-item>
        <el-form-item label="版本说明描述">
          <el-input v-model="addForm.versionDesc"  type="textarea" :autosize="{ minRows: 2, maxRows: 6}"/>
        </el-form-item>
        <el-form-item label="下载说明title">
          <el-input v-model="addForm.downloadTitle"  />
        </el-form-item>
        <el-form-item label="下载说明描述">
          <el-input v-model="addForm.downloadDesc"  type="textarea" :autosize="{ minRows: 2, maxRows: 6}"/>
        </el-form-item>
        <el-form-item label="备注">
          <el-input v-model="addForm.remark"  />
        </el-form-item>
        <el-row>
          <el-col :span="12">
            <el-form-item label="移动端icon">
              <el-upload
                  class="avatar-uploader"
                  style="width: 330px;height: 235px;border: 1px dashed #d9d9d9;"
                  ref="uploadPc"
                  action="#"
                  :show-file-list="false"
                  :before-upload="beforeUpload"
                  :on-success="uploadSuccess"
                  :on-change="(file,fileList)=>{uploadChange(file,fileList,'mo')}"
                  :auto-upload="false">
                <img v-if="moImageUrl" :src="moImageUrl" class="avatar" alt>
                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
              </el-upload>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="pc端icon">
              <el-upload
                  class="avatar-uploader"
                  style="width: 375px;height: 150px;border: 1px dashed #d9d9d9;"
                  ref="uploadMo"
                  action="#"
                  :show-file-list="false"
                  :before-upload="beforeUpload"
                  :on-success="uploadSuccess"
                  :on-change="(file,fileList)=>{uploadChange(file,fileList,'pc')}"
                  :auto-upload="false">
                <img v-if="pcImageUrl" :src="pcImageUrl" class="avatar2" alt>
                <i v-else class="el-icon-plus avatar-uploader-icon2"></i>
              </el-upload>
            </el-form-item>
          </el-col>
        </el-row>
        <el-form-item>
          <el-button type="success" @click="handleSubmit">&nbsp;&nbsp;提&nbsp;&nbsp;交&nbsp;&nbsp;</el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import { LoginModule } from "@/store/modules/login";
import { addApkModule } from "@/store/modules/addApk";
import { uploadImgToBase64 } from '@/utils/common'
import md5 from "js-md5";
export default Vue.extend({
  name: "addApkPage",
  data() {
    return {
      pcImageUrl: "",
      moImageUrl: "",
      pcFile: '',
      moFile: '',
      addForm: {
        buttonLink: "http://www.google.com",
        buttonTxt: "downloadLink",
        appId: 10092,
        address: "http://www.address.com",
        versionTitle: "versionTitle",
        versionDesc: "versionDesc",
        downloadTitle: "versionTitle",
        downloadDesc: "versionDesc",
        remark: "remark",
        webTitle: "APK-TIP",
        pageTitle: "新增落地页",
        filePath: "test-apk-tip"
      }
    };
  },
  computed: {
    appList() {
      return LoginModule.appList;
    }
  },
  methods: {
    beforeUpload(file: any){
      const isJPG = file.type === 'image/jpeg';
      const isLt2M = file.size / 1024 / 1024 < 2;
      if (!isJPG) {
      this.$message.error('上传头像图片只能是 JPG 格式!');
      }
      if (!isLt2M) {
      this.$message.error('上传头像图片大小不能超过 2MB!');
      }
      return isJPG && isLt2M;
    },
    uploadChange(file: any, fileList: any, type: string){
      uploadImgToBase64(file.raw).then((data: any) => { 
        const imageInfo = new Image();
        imageInfo.src = data.result;
        imageInfo.onload = () => {
          if (type === 'mo') {
            if (imageInfo.width!=660 || imageInfo.height!=470) {
              this.moImageUrl = '';
              return this.$message.error('图片尺寸应为660*470,请重新选择');
            }
            this.moImageUrl = data.result;
            this.moFile = file.raw;
          } else {
            if (imageInfo.width!=750 || imageInfo.height!=300) {
              this.pcImageUrl = '';
              return this.$message.error('图片尺寸应为750*300,请重新选择');
            }
            this.pcImageUrl = data.result;
            this.pcFile = file.raw;
          }
          
        }
        
      })
    },
    uploadSuccess(res: any, file: any) {
      console.log("uploadSuccess",file)
    },
    handleSubmit() {
      if (!this.pcImageUrl || !this.moImageUrl) {
        return this.$message({
          message: "请将双端Icon选择完整",
          type: "warning"
        });
      }
      (this.$refs.addForm as any).validate((valid: any) => {
        if (valid) {
          const appInfo: any = this.appList.filter(
            (item: any) => item.appId === this.addForm.appId
          )[0];
          const formData: any = new FormData()
          formData.append('buttonLink', this.addForm.buttonLink)
          formData.append('buttonTxt', this.addForm.buttonTxt)
          formData.append('appId', this.addForm.appId)
          formData.append('address', 'http://172.16.10.103:3000/'+this.addForm.filePath)
          formData.append('versionTitle', this.addForm.versionTitle)
          formData.append('versionDesc', this.addForm.versionDesc)
          formData.append('downloadTitle', this.addForm.downloadTitle)
          formData.append('downloadDesc', this.addForm.downloadDesc)
          formData.append('remark', this.addForm.remark)
          formData.append('webTitle', this.addForm.webTitle)
          formData.append('pageTitle', this.addForm.pageTitle)
          formData.append('filePath', this.addForm.filePath)
          formData.append('appName', appInfo.appName)
          formData.append('moFile', this.moFile)
          formData.append('pcFile', this.pcFile)
          formData.append('id', md5((''+new Date().getTime())+this.addForm.appId))
          addApkModule.addApkPage(formData).then(() => {
            this.$message({
              message: "创建成功",
              type: "success"
            });
            setTimeout(() => {
              this.$emit("handleChange", "ApkPageList");
            }, 500);
          });
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    goBack() {
      this.$emit("handleChange", "ApkPageList");
    }
  }
});
</script>


<style lang="scss" scoped>
.avatar-uploader .el-upload {
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}
.avatar-uploader .el-upload:hover {
  border-color: #409eff;
}
.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 330px;
  height: 235px;
  line-height: 235px;
  text-align: center;
}
.avatar {
  width: 330px;
  height: 235px;
  display: block;
}
.avatar-uploader-icon2 {
  font-size: 28px;
  color: #8c939d;
  width: 375px;
  height: 150px;
  line-height: 150px;
  text-align: center;
}
.avatar2 {
  width: 375px;
  height: 150px;
  display: block;
}
</style>

<script setup>
import { ref } from 'vue'
import ChannelSelect from '@/views/article/components/ChannelSelect.vue';
import { Plus } from '@element-plus/icons-vue'
//控制抽屉显示隐藏
const visibleDrawer = ref(false)

//默认数据
const defaultForm = {
    title:'',
    cate_id:'',
    cover_img:'',
    content:'',
    state:''
}

const formModel = ref({
    ...defaultForm
})

//图片上传相关逻辑
const imgUrl = ref('')
const onSelectFile = (uploadFile) => {
    imgUrl.value = URL.createObjectURL(uploadFile.raw)  //预览图片
    formModel.value.cover_img = uploadFile.raw
}

const open = (row) => {
    visibleDrawer.value = true
    console.log(row)

    if(row.id){
    //需要基于row.id 发送请求，获取编辑对应数据，回显
    console.log('编辑回显')
 }else{
    formModel.value = { ...defaultForm }
    console.log('添加')
 }
}

defineExpose({
    open
})

</script>
<template>
<!-- 抽屉 -->
<el-drawer 
    v-model="visibleDrawer" 
    :title="formModel.id ? '编辑文章' : '添加文章'" 
    size="50%"
    >
    <!-- 发表文章表单 -->
    <el-form :model="formModel" ref="formRef">
        <el-form-item label="文章标题:" prop="title">
            <el-input
            v-model="formModel.title"
            placeholder="请输入标题"
            >
            </el-input>
        </el-form-item>
        <el-form-item label="文章分类:" prop="cate_id">
            <channel-select v-model="formModel.cate_id"></channel-select>
        </el-form-item>
        <el-form-item label="文章封面" prop="cover_img"> 
            <!-- 此处需要关闭 element-plus 的自动上传 ,不需要配置action参数 
                语法：URL.createObjectURL(...)创建本地预览的地址，来预览
            -->
            <el-upload
                class="avatar-uploader"
                :show-file-list="false"
                :auto-upload="false"
                :on-change="onSelectFile"
            >
                <img v-if="imgUrl" :src="imgUrl" class="avatar" />
                <el-icon v-else class="avatar-uploader-icon"><Plus /></el-icon>
            </el-upload>
        
        </el-form-item>
        <el-form-item label="文章内容" prop="content">
            <div class="editor">文编编辑器</div>
        </el-form-item>
        <el-form-item>
            <el-button type="primary">发布</el-button>
            <el-button type="info">草稿</el-button>
        </el-form-item>
    </el-form>
    </el-drawer>
</template>

<style lang="scss" scoped>
 .avatar-uploader {
  :deep() {
    .avatar {
      width: 178px;
      height: 178px;
      display: block;
    }
    .el-upload {
      border: 1px dashed var(--el-border-color);
      border-radius: 6px;
      cursor: pointer;
      position: relative;
      overflow: hidden;
      transition: var(--el-transition-duration-fast);
    }
    .el-upload:hover {
      border-color: var(--el-color-primary);
    }
    .el-icon.avatar-uploader-icon {
      font-size: 28px;
      color: #8c939d;
      width: 178px;
      height: 178px;
      text-align: center;
    }
  }
}

</style>








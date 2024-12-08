<script setup>
import { Delete, Edit } from '@element-plus/icons-vue'
import { ref } from 'vue'
import ChannelSelect from './components/ChannelSelect.vue'
import { artGetListService } from '@/api/article'
import { formatTime } from '@/utils/format'
import ArticleEdit from '@/views/article/components/ArticleEdit.vue'
const articleList = ref([])//文章列表
const total = ref(0)//总条数
const loading = ref(false) //loading状态

//定义请求参数对象
const params = ref({
    pagenum: 1, //当前页
    pagesize: 5, //当前生效的每页条数
    cate_id: '',
    state: ''
})

//基于params参数，获取文章列表
const getArticleList = async () => {
    loading.value = true
    const res = await artGetListService(params.value)
    articleList.value = res.data.data
    total.value = res.data.total
    loading.value = false
}
getArticleList()

//编辑逻辑
const onEditArticle = (row) => {
    articleEditRef.value.open({row})
}

const articleEditRef = ref()
//添加逻辑
const onAddArtic = () => {
    articleEditRef.value.open({})
}

//删除逻辑
const onDeleteArticle = (row) => {
    console.log(row)
}

//处理分页逻辑
const onSizeChange = size => {
    //console.log('当前每页条数', size)
    //每页条数变化，重新渲染
    params.value.pagenum = 1
    params.value.pagesize = size
    //基于第一页开始渲染
    getArticleList()
}

const onCurrentChange = page => {
    //console.log('页码变化了', page)
    params.value.pagenum = page
    //基于当前页渲染数据
    getArticleList()
}

//搜索逻辑 按照最新条件重新检索，从第一页开始展示
const onSearch = () => {
    params.value.pagenum = 1
    getArticleList()
}
//重置逻辑 将筛选条件清空，从第一页开始展示
const onReset = () => {
params.value.pagenum = 1
params.value.cate_id = ''
params.value.state = ''
getArticleList()
}



</script>

<template>
    <page-container title="文章管理">
     <template #extra>
         <el-button type="primary" @click="onAddArtic">添加文章</el-button>
     </template>
     
    <!-- 表单区域 -->
     <el-form inline>
        <el-form-item label="文章分类:">
            <!--Vue2  v-model :value 和 @input 的简写 -->
            <!--Vue3  v-model :modelValue 和 @update:modelValue 的简写 -->
            <channel-select style="width:200px" v-model="params.cate_id"></channel-select>
        </el-form-item>
        <el-form-item label="发布状态">
            <!-- 后台标记发布状态，通过中文标记，已发布/草稿 -->
            <el-select v-model="params.state" style="width:200px">
                <el-option label="已发布" value="已发布"></el-option>
                <el-option label="草稿" value="草稿"></el-option>
            </el-select>
        </el-form-item>
        <el-form-item>
            <el-button @click="onSearch" type="primary">搜索</el-button>
            <el-button @click="onReset">重置</el-button>
        </el-form-item>
     </el-form>

    <!-- 表格区域 -->
     <el-table :data="articleList" v-loading="loading">
        <el-table-column label="文章标题" prop="title">
            <template #default="{ row }">
                <el-link type="primary" :underline="false">{{ row.title }}</el-link>
            </template>
        </el-table-column>
        <el-table-column label="分类" prop="cate_name"></el-table-column>
        <el-table-column label="发表时间" prop="pub_date">
            <template #default="{ row }">
                {{ formatTime(row.pub_date) }}
            </template>
        </el-table-column>
        <el-table-column label="状态" prop="state"></el-table-column>
        <!-- 利用作用域插槽 row 可以获取当前行的数据 =>  v-for 遍历 item -->
        <el-table-column label="操作">
            <template #default="{ row }">
                <el-button 
                circle 
                plain 
                type="primary" 
                :icon="Edit" 
                @click="onEditArticle(row)"
                ></el-button>
                <el-button 
                circle 
                plain 
                type="danger" 
                :icon="Delete"  
                @click="onDeleteArticle(row)"
                ></el-button>
            </template>
        </el-table-column>
     </el-table>

     <!-- 分页区域 -->
     <el-pagination
      v-model:current-page="params.pagenum"
      v-model:page-size="params.pagesize"
      :page-sizes="[2, 3, 5, 10]"
      :background="true"
      layout="jumper, total, sizes, prev, pager, next"
      :total="total" 
      @size-change="onSizeChange"
      @current-change="onCurrentChange"
      style="margin-top: 20px; justify-content: flex-end;"
    />

    <!-- 添加编辑的抽屉 -->
     <article-edit ref="articleEditRef"></article-edit>
    </page-container>
 </template>
 
 <style lang="scss" scoped>
 
 </style>
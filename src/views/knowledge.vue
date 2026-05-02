<template>
  <div >
    <PageHead title="知识文章">
      <template #buttons>
        <el-button @click="dialogVisible = true" type="primary">新增</el-button>
      </template>
    </PageHead>
    <TableSearch :formItem = "formItem" @Search="handleSearch" />
    <el-table :data="tableData" style="width: 100%;margin-top: 25px;">
      <el-table-column label="文章标题" fixed="left">
        <template #default="scope">
          <div style="display: flex; align-items: center">
            <el-icon><timer /></el-icon>
            <span>{{ scope.row.title }}</span>
          </div>
        </template>
      </el-table-column>
      <el-table-column label="分类" width="200">
        <template #default="scope">
          <div style="display: flex; align-items: center">
            <el-icon><timer /></el-icon>
            <span>{{ categoryMap[scope.row.categoryId] }}</span>
          </div>
        </template>
      </el-table-column>
      <el-table-column label="作者" property="authorName" width="200" />
      <el-table-column label="阅读量" property="readCount" width="200" />
      <el-table-column label="发布时间" property="publishedAt" width="200" />
      <el-table-column label="操作" width="240"  fixed="right">
        <template #default="scope">
          <el-button text type="primary">编辑</el-button>
          <el-button v-if="scope.row.status === 0 || scope.row.status === 2" text type="success">发布</el-button>
          <el-button v-if="scope.row.status === 1" text type="warning">下线</el-button>
          <el-button text type="danger">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination 
      style="margin-top: 25px;"
      @change="handleChange"
      layout="prev, pager, next" 
      :total="pageParams.total" 
      :page-size="pageParams.size"/>
    <ArticleDialog v-model:modelValue="dialogVisible"/>
      
  </div>
</template>

<script setup>
import PageHead from '@/components/PageHead.vue'
import TableSearch from '@/components/TableSearch.vue'
import { onMounted,reactive, ref } from 'vue'
import { categoryTree, articlePage } from '@/api/login'
import ArticleDialog from '@/components/ArticleDialog.vue'


const formItem = [
  {comp : 'input', prop: 'title', label: '文章标题', placeholder: '请输入文章标题'},
  {comp : 'select', prop: 'category', label: '分类', placeholder: '请选择分类'},
  {comp : 'select', prop: 'status', label: '状态', placeholder: '请选择状态', options: [{
    label: '草稿',
    value: '0'
  },{
    label: '已发布',
    value: '1'
  },{
    label: '已下线',
    value: '2'
  }]}
]

const pageParams = {
  currentPage: 1,
  size: 10,
  total:5
}


// 新增和编辑弹窗显示
const dialogVisible = ref(false)

const handleChange = (page) => {
  pageParams.currentPage = page
  handleSearch()
}

// 查询方法
const handleSearch = async (formData) => {
  const searchParams = {
    ...formData,
    ...pageParams
  }
  const {records, total} = await articlePage(searchParams)
  tableData.value = records
  pageParams.total = total
}
// 分类映射
const categoryMap = reactive({})
// 分类处理
const categories = ref([])
// 列表数据
const tableData = ref([])

onMounted(async () => {
  const data = await categoryTree()
  categories.value = data.map(item => {
    categoryMap[item.id] = item.categoryName
    return {
      label: item.categoryName,
      value: item.id
    }
  })
  formItem[1].options = categories.value

  // 文章列表数据获取
  handleSearch()
})

</script>

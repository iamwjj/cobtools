<template>
  <div class="seller-list">
    <div class="search-container">
      <el-form class="search-form" ref="queryFormRef" :model="queryParams" :inline="true">
        <el-form-item label="店铺名称" prop="city">
          <el-input />
        </el-form-item>
        <el-form-item label="大类" prop="country">
          <el-select v-model="queryParams.country" placeholder="请选择" clearable>
            <el-option label="玩具与游戏" value="" />
            <el-option label="美妆与个护" value="" />
            <el-option label="户外运动" value="" />
            <el-option label="季节性产品" value="" />
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleQuery">查询</el-button>
          <el-button @click="handleReset">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-table :data="tableData" v-loading="loading" @sort-change="handleSortChange"
      :default-sort="{ prop: 'reviewCount', order: 'descending' }" border style="width: 100%">
      <el-table-column prop="asin" label="ASIN" width="160" />
      <el-table-column props="sellerName" label="店铺" width="180">
        <template #default="props">
          <a :href="`https://www.amazon.com/sp?ie=UTF8&seller=${props.row.sellerID}`" target="_blank">{{
            props.row.sellerName }}</a>
        </template>
      </el-table-column>
      <el-table-column prop="brand" label="品牌名" width="140" />
      <el-table-column prop="lm_sales" label="上个月购买量" width="140" />
      <el-table-column prop="rating" label="评论星级" width="140" />
      <el-table-column prop="reviews" label="评论数" width="140" />
      <el-table-column prop="category" label="大类" width="120" />
      <el-table-column prop="rank" label="大类排名" width="120" />
      <el-table-column prop="sub_category" label="小类" width="280" />
      <el-table-column prop="grade" label="潜力指数" width="80" />
      <el-table-column label="操作" width="120">
        <template #default="props">
          <el-button type="primary" link @click="() => handleCollect(props.row)"
            v-if="!props.row.isCollected">收藏</el-button>
          <!-- <el-button link type="primary" size="small" @click="handleClick">
            详情
          </el-button> -->
        </template>
      </el-table-column>
    </el-table>
    <div class="pagination">
      <el-pagination background :current-page="queryParams.pageNum" :page-size="queryParams.pageSize" :total="total"
        @current-change="handlePagination" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue'
import axios from 'axios'
import mockData from '../mock';

const queryFormRef = ref()
const queryParams = reactive({
  country: '',
  city: '',
  pageNum: 1,
  pageSize: 20,
  order: [],
})
const loading = ref(false)
const tableData = ref([])
const total = ref(0); // 数据总数

function handleQuery() {
  const params = {
    ...queryParams,
  }
  tableData.value = mockData
  // loading.value = true
  // axios.get('http://192.168.3.95:3001/seller/list', { params }).then(res => {
  //   console.log('axios response: ', res)
  //   tableData.value = res.data.data
  //   total.value = res.data.total
  // }).catch(err => {
  //   console.log('fetch error: ', err)
  // }).finally(() => {
  //   loading.value = false
  // })
}

function handlePagination(page: number) {
  queryParams.pageNum = page
  handleQuery()
}

function handleReset() {
  queryFormRef.value.resetFields()
  queryParams.pageNum = 1
  queryParams.city = ''
  queryParams.order = ['reviewCount', 'DESC']
  handleQuery()
}

const handleClick = (row: any) => {
  window.open(row.amzUrl)
}

function handleSelectCity(data) {
  queryParams.city = data?.[1] ?? ''
}

function handleSortChange(data: any) {
  queryParams.order = [data.prop, data.order.startsWith('desc') ? 'DESC' : 'ASC']
  handleQuery()
}

function handleCollect(row) {
  row.isCollected = true
  ElMessage({
    message: '收藏成功',
    type: 'success',
  })
}

onMounted(() => {
  handleQuery()
})
</script>

<style lang="scss" scoped>
.search-form .el-select {
  width: 120px;
}

.el-table {
  a {
    color: var(--el-color-primary);
  }
}

.pagination {
  display: flex;
  justify-content: center;
  padding: 20px 0;
}
</style>

<template>
  <div class="seller-list">
    <div class="search-container">
      <el-form class="search-form" ref="queryFormRef" :model="queryParams" :inline="true">
        <el-form-item label="国家" prop="country">
          <el-select v-model="queryParams.country" placeholder="请选择" clearable>
            <el-option label="中国" value="CN" />
            <el-option label="美国" value="US" />
          </el-select>
        </el-form-item>
        <el-form-item label="城市" prop="city">
          <el-input v-model="queryParams.city" placeholder="请输入城市" clearable style="width: 200px" />
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleQuery">查询</el-button>
          <el-button @click="handleReset">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-table :data="tableData" v-loading="loading" border style="width: 100%">
      <el-table-column prop="rank" label="排名" width="80" align="center" />
      <el-table-column props="sellerID" label="卖家ID" width="200">
        <template #default="props">
          <a :href="`https://www.amazon.com/sp?ie=UTF8&seller=${props.row.sellerID}`" target="_blank">{{
            props.row.sellerID }}</a>
        </template>
      </el-table-column>
      <el-table-column prop="sellerName" label="卖家名称" width="240" />
      <el-table-column prop="reviewCount" label="评论数" width="120" />
      <el-table-column prop="listingCount" label="商品数量" width="120" />
      <el-table-column prop="country" label="国家" width="100" />
      <el-table-column prop="brand[0].address" label="地区" width="300" />
      <el-table-column label="amzURL" width="300">
        <template #default="props">
          <a :href="props.row.amzUrl" target="_blank">{{ props.row.amzUrl }}</a>
        </template>
      </el-table-column>
      <el-table-column prop="update_at" label="更新时间" width="200" />
      <el-table-column label="操作" width="120">
        <template #default>
          <el-button link type="primary" size="small" @click="handleClick">
            详情
          </el-button>
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

const queryFormRef = ref()
const queryParams = reactive({
  pageNum: 1,
  pageSize: 15
})
const loading = ref(false)
const tableData = ref([])
const total = ref(0); // 数据总数

function handleQuery() {
  const params = {
    ...queryParams,
  }
  loading.value = true
  axios.get('http://192.168.3.95:3001/seller/list', { params }).then(res => {
    console.log('axios response: ', res)
    tableData.value = res.data.data
    total.value = res.data.total
  }).catch(err => {
    console.log('fetch error: ', err)
  }).finally(() => {
    loading.value = false
  })
}

function handlePagination(page: number) {
  queryParams.pageNum = page
  handleQuery()
}

function handleReset() {
  queryFormRef.value.resetFields()
  queryParams.pageNum = 1
  handleQuery()
}

const handleClick = (row: any) => {

}

onMounted(() => {
  handleQuery()
})
</script>

<style lang="scss" scoped>
.search-form .el-select {
  width: 120px;
}

.pagination {
  display: flex;
  justify-content: center;
  padding: 20px 0;
}
</style>

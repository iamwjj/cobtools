<template>
  <div class="seller-list">
    <div class="search-container">
      <el-form class="search-form" ref="queryFormRef" :model="queryParams" :inline="true">
        <el-form-item label="国家" prop="country">
          <el-select v-model="queryParams.country" placeholder="请选择" clearable>
            <el-option label="中国" value="CN" />
            <el-option label="中国香港" value="HK" />
            <el-option label="中国台湾" value="TW" />
            <el-option label="美国" value="US" />
          </el-select>
        </el-form-item>
        <el-form-item label="城市" prop="city" v-show="queryParams.country === 'CN' || !queryParams.country">
          <el-cascader :options="pcTextArr" v-model="queryParams.city" @change="handleSelectCity" clearable
            placeholder="请选择城市" />
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleQuery">查询</el-button>
          <el-button @click="handleReset">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-table :data="tableData" v-loading="loading" @sort-change="handleSortChange"
      :default-sort="{ prop: 'rank', order: 'ascending' }" border style="width: 100%">
      <el-table-column prop="rank" label="排名" width="80" align="center" />
      <el-table-column prop="brand[0].businessName" label="公司名称" width="260" />
      <el-table-column props="sellerName" label="店铺名称" width="200">
        <template #default="props">
          <a :href="`https://www.amazon.com/sp?ie=UTF8&seller=${props.row.sellerID}`" target="_blank">{{
            props.row.sellerName }}</a>
        </template>
      </el-table-column>

      <el-table-column prop="reviewCount" label="评论数" sortable="custom" width="120" />
      <el-table-column prop="listingCount" label="商品数量" sortable="custom" width="120" />
      <el-table-column prop="country" label="国家" width="100">
        <template #default="props">
          <span>{{ ['HK', 'TW'].includes(props.row.country) ? 'CN-' + props.row.country : props.row.country }}</span>
        </template>
      </el-table-column>
      <el-table-column prop="brand[0].city" label="城市" width="100" />
      <el-table-column prop="brand[0].district" label="辖区" width="100" />
      <el-table-column prop="brand[0].address" label="详细地址" width="400" />
      <el-table-column label="操作" width="120">
        <template #default="props">
          <a :href="props.row.amzUrl" target="_blank">详情</a>
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
import { pcTextArr } from "element-china-area-data";

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
  queryParams.city = ''
  queryParams.order = ['rank', 'ASC']
  handleQuery()
}

const handleClick = (row: any) => {

}

function handleSelectCity(data) {
  queryParams.city = data?.[1] ?? ''
}

function handleSortChange(data: any) {
  queryParams.order = [data.prop, data.order.slice(0, 4).toUpperCase()]
  handleQuery()
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

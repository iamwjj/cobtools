<template>
  <main class='fans-list'>
    <div class="search-container">
      <el-form class="search-form" ref="queryFormRef" :inline="true" :model="queryParams">
        <el-form-item label="国家">
          <el-select v-model="queryParams.country" placeholder="请选择" clearable>
            <el-option label="中国" value="CN" />
            <el-option label="美国" value="US" />
          </el-select>
        </el-form-item>
        <el-form-item label="风险评级">
          <el-select v-model="queryParams.risk" placeholder="请选择" clearable>
            <el-option label="高风险" value="high" />
            <el-option label="中风险" value="mid" />
            <el-option label="低风险" value="low" />
            <el-option label="暂无评级" value="no" />
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleQuery">查询</el-button>
          <el-button type="primary" @click="handleReset">重置</el-button>
          <el-button type="primary" @click="handleImport">导入粉丝</el-button>
        </el-form-item>
      </el-form>
    </div>
    <div class="search-result">
      <el-table :data="tableData" v-loading="loading" border style="width: 100%">
        <el-table-column prop="rank" label="姓名" width="120" />
        <el-table-column prop="country" label="国家" width="100" />
        <el-table-column prop="email" label="邮箱" width="180" />
        <el-table-column prop="rank" label="电话" width="180" />
        <el-table-column prop="age" label="年龄" width="100" />
        <el-table-column prop="sex" label="性别" width="100" />
        <el-table-column prop="from" label="来源" width="100" />
        <el-table-column prop="paypal" label="Paypal" width="120" />
        <el-table-column prop="profile" label="Profile" width="120" />
        <el-table-column prop="fb" label="FaceBook" width="120" />
        <el-table-column prop="wa" label="WhatApp" width="120" />
        <el-table-column prop="yt" label="Youtube" width="120" />
        <el-table-column prop="record" label="合作记录" width="200" />
        <el-table-column prop="riskLevel" label="风险评级" width="120" />
      </el-table>
    </div>
    <div class="pagination">
      <el-pagination background :current-page="queryParams.pageNum" :page-size="queryParams.pageSize" :total="total"
        @current-change="handlePageChange" />
    </div>
  </main>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'

const router = useRouter()

const queryParams = reactive({
  pageNum: 1,
  pageSize: 15
})
const queryFormRef = ref()
const tableData = ref([])
const total = ref(0)
const loading = ref(false)

const handleQuery = () => {
  const params = {
    ...queryParams,
  }
  loading.value = true
  axios.get('http://192.168.3.95:3000/user/list', { params }).then(res => {
    console.log('axios response: ', res)
    tableData.value = res.data.data
    total.value = res.data.total
  }).catch(err => {
    console.log('fetch error: ', err)
  }).finally(() => {
    loading.value = false
  })
}

const handlePageChange = (page) => {
  queryParams.pageNum = page
  handleQuery()
}

const handleImport = () => {
  router.push('/fans/import')
}

function handleReset() {
  queryFormRef.value.resetFields()
  queryParams.pageNum = 1
  handleQuery()
}


onMounted(() => {
  //   searchSellers()
})


</script>

<style>
.search-form .el-select {
  --el-select-width: 120px;
}

.pagination {
  margin-top: 20px;
}
</style>

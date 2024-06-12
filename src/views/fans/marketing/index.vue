<template>
  <div class="search-container">
    <el-form class="search-form" ref="queryFormRef" :inline="true" :model="queryParams">
      <el-form-item label="活动名称">
        <el-input v-model="queryParams.name" placeholder="请输入" clearable />
      </el-form-item>
      <el-form-item label="活动类别">
        <el-select v-model="queryParams.category" placeholder="请选择" clearable style="width: 180px;">
          <el-option label="EDM" value="edm" />
          <el-option label="明信片" value="postcard" />
        </el-select>
      </el-form-item>
    </el-form>
  </div>
  <div class="search-result">
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="id" label="活动ID" width="120" />
      <el-table-column prop="name" label="活动名称" width="260" />
      <el-table-column prop="type" label="活动类别" width="140" />
      <el-table-column prop="op" label="运营人员" width="140" />
      <el-table-column prop="fans_number" label="粉丝人数" width="140" />
      <el-table-column label="粉丝详情" width="140">
        <template #default="props">
          <el-button type="primary" link @click="viewDetail">详情</el-button>
        </template>
      </el-table-column>
      <el-table-column label="操作" width="180">
        <template #default="props">
          <el-button type="primary" link>导出数据</el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>

  <el-dialog v-model="visible" title="活动详情" width="600">
    <el-table border :data="actData">
      <el-table-column prop="name" label="名字" />
      <el-table-column prop="email" label="邮箱" />
      <el-table-column prop="category" label="标签" />
    </el-table>
  </el-dialog>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue'
import mockData from '../mock'

const tableData = mockData.activities;
const queryParams = reactive({})
const visible = ref(false)
const actData = ref([])

function viewDetail() {
  visible.value = true
  actData.value = [
    {
      name: 'Corey Algeciras',
      email: 'coreya141@gmail.com',
      category: '玩具与游戏'
    },
    {
      name: 'Corey Algeciras',
      email: 'coreya141@gmail.com',
      category: '玩具与游戏'
    },
    {
      name: 'Corey Algeciras',
      email: 'coreya141@gmail.com',
      category: '玩具与游戏'
    },
    {
      name: 'Corey Algeciras',
      email: 'coreya141@gmail.com',
      category: '玩具与游戏'
    },
    {
      name: 'Corey Algeciras',
      email: 'coreya141@gmail.com',
      category: '玩具与游戏'
    }
  ]
}

</script>

<style lang="scss" scoped></style>

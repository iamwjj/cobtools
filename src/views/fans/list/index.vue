<template>
  <main class="fans-list">
    <div class="search-container">
      <el-form
        class="search-form"
        ref="queryFormRef"
        :inline="true"
        :model="queryParams"
      >
        <el-form-item label="国家">
          <el-select
            v-model="queryParams.country"
            placeholder="请选择"
            clearable
          >
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
        <el-form-item label="类目">
          <el-select v-model="queryParams.risk" placeholder="请选择" clearable>
            <el-option label="高风险" value="high" />
            <el-option label="中风险" value="mid" />
            <el-option label="低风险" value="low" />
            <el-option label="暂无评级" value="no" />
          </el-select>
        </el-form-item>
        <el-form-item label="标签">
          <el-input v-model="queryParams.risk" placeholder="请输入" clearable />
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleQuery">查询</el-button>
          <el-button type="primary" @click="handleReset">重置</el-button>
          <el-button
            type="primary"
            @click="handleManager"
            v-if="multipleSelection.length > 0"
            >粉丝管理</el-button
          >
          <el-button
            type="primary"
            @click="handleMarketing"
            v-if="multipleSelection.length > 0"
            >营销活动</el-button
          >
        </el-form-item>
      </el-form>
    </div>
    <div class="search-result">
      <el-table
        :data="tableData"
        v-loading="loading"
        border
        style="width: 100%"
        @selection-change="handleSelectionChange"
      >
        <el-table-column type="selection" width="55" />
        <el-table-column prop="create_at" label="时间" width="100" />
        <el-table-column prop="id" label="粉丝ID" width="80" />
        <el-table-column prop="name" label="姓名" width="140" />
        <el-table-column prop="country" label="国家" width="60" />
        <el-table-column prop="email" label="邮箱" width="150" />
        <el-table-column prop="tel" label="电话" width="120" />
        <el-table-column prop="address" label="地址" width="300" />
        <el-table-column prop="age" label="年龄" width="60" />
        <el-table-column prop="sex" label="性别" width="60" />
        <el-table-column prop="source" label="来源" width="80" />
        <el-table-column prop="paypal" label="Paypal" width="180" />
        <el-table-column prop="profile" label="Profile" width="280" />
        <el-table-column prop="fb" label="FaceBook" width="180" />
        <el-table-column prop="wa" label="WhatApp" width="180" />
        <el-table-column prop="yt" label="Youtube" width="180" />
        <el-table-column prop="risk" label="风险评级" width="100" />
        <el-table-column prop="category" label="类目" width="120" />
        <el-table-column prop="tags" label="兴趣标签" width="120" />
        <el-table-column
          prop="orderId"
          label="订单号"
          width="180"
          fixed="right"
        >
          <template #default="props">
            <el-button type="primary" link @click="viewRecord">{{
              props.row.orderId
            }}</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <div class="pagination">
      <el-pagination
        background
        :current-page="queryParams.pageNum"
        :page-size="queryParams.pageSize"
        :total="total"
        @current-change="handlePageChange"
      />
    </div>
    <el-dialog v-model="visible" title="合作记录" width="600">
      <el-timeline>
        <el-timeline-item
          v-for="(activity, index) in activities"
          :key="index"
          :icon="activity.icon"
          :type="activity.type"
          :color="activity.color"
          :size="activity.size"
          :hollow="activity.hollow"
          :timestamp="activity.timestamp"
        >
          {{ activity.content }}
        </el-timeline-item>
      </el-timeline>
    </el-dialog>
  </main>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import mockData from "../mock";

const router = useRouter();

const queryParams = reactive({
  pageNum: 1,
  pageSize: 15,
});
const queryFormRef = ref();
const tableData = ref(mockData.fans);
const total = ref(0);
const loading = ref(false);
const visible = ref(false);
const multipleSelection = ref([]);

//mock
const activities = [
  {
    content: "下单",
    timestamp: "2024-06-01",
    color: "#0bbd87",
  },
  {
    content: "站内联系",
    timestamp: "2024-06-03",
    color: "#0bbd87",
  },
  {
    content: "邮件联系",
    timestamp: "2024-06-06",
    color: "#0bbd87",
  },
  {
    content: "邮件获取地址",
    timestamp: "2024-06-07",
    color: "#0bbd87",
  },
  {
    content: "站内补发 CONSUMER-2023830-21028",
    timestamp: "2024-06-07",
    color: "#0bbd87",
  },
];

const handleQuery = () => {
  const params = {
    ...queryParams,
  };
  loading.value = true;
  axios
    .get("http://192.168.3.95:3000/user/list", { params })
    .then((res) => {
      console.log("axios response: ", res);
      tableData.value = res.data.data;
      total.value = res.data.total;
    })
    .catch((err) => {
      console.log("fetch error: ", err);
    })
    .finally(() => {
      loading.value = false;
    });
};

const handlePageChange = (page) => {
  queryParams.pageNum = page;
  handleQuery();
};

const handleImport = () => {
  router.push("/fans/import");
};

function handleReset() {
  queryFormRef.value.resetFields();
  queryParams.pageNum = 1;
  handleQuery();
}

function viewRecord(id) {
  visible.value = true;
}

const handleSelectionChange = (val) => {
  multipleSelection.value = val;
};

onMounted(() => {
  //   searchSellers()
});
</script>

<style>
.search-form .el-select {
  --el-select-width: 120px;
}

.pagination {
  margin-top: 20px;
}
</style>

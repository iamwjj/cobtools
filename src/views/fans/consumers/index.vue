<template>
  <main class="fans-list">
    <div class="search-container">
      <el-form class="search-form" ref="queryFormRef" :inline="true" :model="queryParams">
        <el-form-item label="站点">
          <el-select v-model="queryParams.country" placeholder="请选择" clearable>
            <el-option label="美国" value="US" />
            <el-option label="英国" value="UK" />
            <el-option label="法国" value="FR" />
            <el-option label="德国" value="DE" />
            <el-option label="意大利" value="IT" />
            <el-option label="西班牙" value="ES" />
          </el-select>
        </el-form-item>
        <!-- <el-form-item label="风险评级">
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
        </el-form-item> -->
        <el-form-item label="标签">
          <el-select v-model="queryParams.tag" placeholder="请选择" clearable>
            <el-option label="玩具" value="high" />
            <el-option label="美妆" value="mid" />
            <el-option label="户外" value="low" />
            <el-option label="节日" value="no" />
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="handleQuery">查询</el-button>
          <el-button type="primary" @click="handleReset">重置</el-button>
          <el-button type="primary" @click="handleManager" v-if="multipleSelection.length > 0">粉丝管理</el-button>
          <el-button type="primary" @click="handleMarketing" v-if="multipleSelection.length > 0">营销活动</el-button>
        </el-form-item>
      </el-form>
    </div>
    <div class="search-result">
      <el-table :data="tableData" v-loading="loading" border style="width: 100%"
        @selection-change="handleSelectionChange">
        <!-- <el-table-column type="selection" width="55" /> -->
        <!-- <el-table-column prop="create_at" label="时间" width="100" /> -->
        <el-table-column prop="num" label="序号" width="60" />
        <el-table-column prop="site" label="站点" width="60" />
        <el-table-column prop="device" label="注册设备" width="100" />
        <el-table-column prop="ip" label="网络IP" />
        <el-table-column prop="name" label="名字" />
        <el-table-column prop="phone" label="电话" />
        <el-table-column prop="address" label="地址" width="240" />
        <el-table-column prop="state" label="所在州" />
        <el-table-column prop="pay_type" label="付款方式" />
        <el-table-column prop="email" label="邮箱" />
        <el-table-column prop="browser" label="浏览器" width="100" />
        <el-table-column prop="create_at" label="注册时间" width="100" />
        <el-table-column prop="phone" label="手机" />
        <el-table-column prop="record" label="下单记录" width="240">
          <template #default="props">
            <pre>{{ props.row.record }}</pre>
          </template>
        </el-table-column>
        <el-table-column prop="tag" label="标签" />
        <!-- <el-table-column prop="orderId" label="订单号" width="180" fixed="right">
          <template #default="props">
            <el-button type="primary" link @click="viewRecord">{{
              props.row.orderId
              }}</el-button>
          </template>
</el-table-column> -->
      </el-table>
    </div>
    <div class="pagination">
      <el-pagination background :current-page="queryParams.pageNum" :page-size="queryParams.pageSize" :total="total"
        @current-change="handlePageChange" />
    </div>
    <el-dialog v-model="visible" title="合作记录" width="600">
      <el-timeline>
        <el-timeline-item v-for="(activity, index) in activities" :key="index" :icon="activity.icon"
          :type="activity.type" :color="activity.color" :size="activity.size" :hollow="activity.hollow"
          :timestamp="activity.timestamp">
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
const tableData = ref(mockData.consumers);
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

const handleManager = () => {
  router.push("/fans/import");
};

const handleMarketing = () => {

}

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

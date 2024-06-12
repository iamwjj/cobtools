<script setup lang="ts"></script>

<template>
  <main class="fans-import">
    <div class="import-steps">
      <el-steps :active="1" align-center>
        <el-step title="上传文件" description="请上传制定文件" />
        <el-step title="检验数据" description="检查数据格式是否正确" />
        <el-step title="导入结果" description="导入数据结果反馈" />
      </el-steps>
    </div>
    <div style="width: 1000px; margin: 0px auto;">
      <div class="tip custom-block tishibox" data-v-a104e76a="">
        <p class="custom-block-title" data-v-a104e76a="">导入说明:</p>
        <p data-v-a104e76a="">1、点击下方文字按钮，下载模版，并按照规定格式填写数据，每次填写数据总和不超过10W条;</p>
        <p data-v-a104e76a="">2、建议尽可能多的导入亚马逊的订单信息，以便对粉丝进行更多维度的分析;</p>
        <p data-v-a104e76a="">3、导入规则：1、姓名（必填）; 2、地址、邮箱、手机（三选一）;</p>
        <p data-v-a104e76a="">4、如果已存在该用户，系统会更新用户对应的信息;</p>
        <p class='file-download'><a href="/file/粉丝导入模板.xlsx" target="_blank" class="m-xs" data-v-a104e76a=""><i
              class="iconfont icon-download"></i>粉丝导入模板.xls</a></p>
      </div>
      <div class="tip custom-block tishibox">
        <p class="custom-block-title" data-v-a104e76a="">上传文件</p>
        <p data-v-a104e76a="">上传文件格式仅支持xls，xlsx，且文件大小不得超过2M</p>
      </div>

    </div>
    <footer class="el-footer el-footer-right">
      <div class="center">

      </div>
    </footer>
  </main>

</template>

<style lang="scss" scoped>
.el-steps {
  padding: 20px 0;
}

.tishibox {
  margin-bottom: 20px;
  line-height: 24px;
}

.tip {
  padding: 8px 16px;
  background-color: #fff;
  border: 1px solid #eee;
  border-left: 5px solid var(--el-color-primary);
  margin-bottom: 10px;
}

.file-download {
  color: var(--el-color-primary);
}
</style>

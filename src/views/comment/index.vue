<template>
  <div class="container">
    <el-card>
      <div slot="header">
        <my-bread>评论管理</my-bread>
      </div>
      <el-table :data="comments">
        <el-table-column label="标题"></el-table-column>
        <el-table-column label="总评论数"></el-table-column>
        <el-table-column label="粉丝评论数"></el-table-column>
        <el-table-column label="状态"></el-table-column>
        <el-table-column label="操作"></el-table-column>
      </el-table>
      <el-pagination style="margin-top:15px" background layout="prev, pager, next" :total="1000"></el-pagination>
    </el-card>
  </div>
</template>
<script>
export default {
  data() {
    return {
      comments: []
    };
  }
};
</script>
<style scoped lang="less"></style>
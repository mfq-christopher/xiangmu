<template>
  <div class="container">
    <el-card>
      <div slot="header">
        <my-bread>内容管理</my-bread>
      </div>
      <el-form label-width="80px" size="small">
        <el-form-item label="状态：">
          <el-radio-group v-model="reqParams.status">
            <el-radio :label="null">全部</el-radio>
            <el-radio :label="0">草稿</el-radio>
            <el-radio :label="1">待审核</el-radio>
            <el-radio :label="2">审核通过</el-radio>
            <el-radio :label="3">审核失败</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="频道：">
          <my-channel v-model="reqParams.channel_id"></my-channel>
        </el-form-item>
        <el-form-item label="日期：">
          <el-date-picker
            v-model="dateArr"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            @change="changeDate"
            value-format="yyyy-MM-dd"
          ></el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="search()">筛选</el-button>
        </el-form-item>
      </el-form>
    </el-card>
    <el-card>
      <!-- 筛选结果 -->
      <div slot="header">根据筛选条件共查询到 {{ total }} 条结果:</div>
      <el-table :data="articles">
        <el-table-column label="封面" prop="img">
          <template slot-scope="scope">
            <el-image :src="scope.row.cover.images[0]" fit="cover" style="width:120px;height:80px">
              <div slot="error">
                <img src="../../assets/images/error.gif" style="width:120px;height:80px" alt />
              </div>
            </el-image>
          </template>
        </el-table-column>
        <el-table-column label="标题" prop="title"></el-table-column>
        <el-table-column label="状态">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status === 0" type="info">草稿</el-tag>
            <el-tag v-if="scope.row.status === 1">待审核</el-tag>
            <el-tag v-if="scope.row.status === 2" type="success">审核通过</el-tag>
            <el-tag v-if="scope.row.status === 3" type="warning">审核失败</el-tag>
            <el-tag v-if="scope.row.status === 4" type="danger">删除</el-tag>
          </template>
        </el-table-column>
        <el-table-column label="发布时间" prop="pubdate"></el-table-column>
        <el-table-column label="操作" width="120px">
          <template slot-scope="scope">
            <el-button plain type="primary" @click="edit(scope.row.id)" icon="el-icon-edit" circle></el-button>
            <el-button plain type="danger" @click="del(scope.row.id)" icon="el-icon-delete" circle></el-button>
          </template>
        </el-table-column>
      </el-table>
      <div style="text-align: center;margin-top: 30px;">
        <!-- :total="1000" 指定总条数 -->
        <!-- 默认一页显示10条 :page-size="reqParams.per_page" -->
        <!-- @current-change="changePager"  页码改变事件 -->
        <!-- 更新过数据后  当前页码也需要修改  选中对应的按钮 current-page -->
        <el-pagination
          background
          layout="prev, pager, next"
          :total="total"
          :page-size="reqParams.per_page"
          :current-page="reqParams.page"
          @current-change="changePager"
        ></el-pagination>
      </div>
    </el-card>
  </div>
</template>
<script>
// import MyBread from '@/components/my-bread'
export default {
  // components: { MyBread },
  data() {
    return {
      reqParams: {
        status: null,
        channel_id: null,
        begin_pubdate: null,
        end_pubdate: null,
        page: 1,
        per_page: 20
      },
      // 频道下拉选项数据
      channelOptions: [],
      // 日期数据
      dateArr: [],
      // 文章数据
      articles: [],
      //总条数
      total: 0
    }
  },
  //   watch: {
  //   'reqParams.channel_id': function (newVal, oldVal) {
  //     if (newVal === '') {
  //       this.reqParams.channel_id = null
  //     }
  //   }
  // },
  created() {
    
    //获取文章列表数据
    this.getArticles();
  },
  methods: {
    //编辑函数
    edit (id) {
      this.$router.push('/publish?id=' + id)
    },
    // 删除函数
    del (id) {
      // 弹出确认框 点击确认后  发删除请求  响应成功更新列表即可
      this.$confirm('亲，此操作将永久删除该文章, 是否继续?', '温馨提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async () => {
        // 点击确认
        await this.$http.delete(`articles/${id}`)
        // 这两句代码没有执行  上面一句代码报错  没有抛出这个错误
        this.$message.success('删除文章成功')
        this.getArticles()
      }).catch(() => {})
    },
    // 日期选择后的事件
    changeDate (dateArr) {
      // 严谨一些，清空数据考虑在内
      if (dateArr) {
        this.reqParams.begin_pubdate = dateArr[0]
        this.reqParams.end_pubdate = dateArr[1]
      } else {
        this.reqParams.begin_pubdate = null
        this.reqParams.end_pubdate = null
      }
    },
    // 筛选函数
    search() {
      // 筛选项双向绑定  拿着对应的数据发请求即可  注意：重新筛选后页码第一页
      this.reqParams.page = 1;
      this.getArticles();
    },
    // 改变分页事件对应函数
    changePager(newPage) {
      // 修改获取数据的页码
      this.reqParams.page = newPage;
      this.getArticles();
    },
    async getArticles() {
      const {
        data: { data }
      } = await this.$http.get("articles", { params: this.reqParams });
      this.articles = data.results;
      this.total = data.total_count;
    }
  }
};
</script>
<style scoped lang="less">
.el-card {
  margin-bottom: 20px;
}
</style>
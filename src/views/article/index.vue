<template>
  <div id="app">
    <!-- 筛选区域 -->
    <el-card class="filter-card">
      <div slot="header" class="clearfix">
        <span>筛选条件</span>
      </div>
      <el-form ref="form" :model="filter" label-width="80px">
        <el-form-item label="状态">
          <el-radio-group v-model="filter.status">
            <el-radio label="">全部</el-radio>
            <el-radio
            v-for="(item, index) in statTypes"
            :key="item.label"
            :label="index + ''"
            >{{ item.label }}</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="频道">
          <el-select v-model="filter.channel_id" placeholder="请选择活动区域">
            <el-option label="全部" value=""></el-option>
            <el-option
            v-for="item in channels"
            :key="item.id"
            :label="item.name"
            :value="item.id"
             ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="时间">
          <el-date-picker
            value-format='yyyy-MM-dd'
            v-model="begin_end_pubdate"
            type="daterange"
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            @change="handleChange"
          ></el-date-picker>
        </el-form-item>
        <el-form-item>
          <el-button
          type="primary"
          @click="onSubmit"
          :loading="artloading">查询</el-button>
        </el-form-item>
      </el-form>
    </el-card>
    <!-- /筛选区域 -->

    <!-- 列表 -->
    <el-card class="list-card">
      <div slot="header" class="clearfix">
        <span>共找到 <b>{{ totalCount }}</b> 条符合条件的内容</span>
      </div>

      <!-- table 表格 -->
      <el-table class="list-table"
      :data="articles"
      style="width: 100%"
      v-loading="artloading">
        <el-table-column
        prop="cover.images[0]"
        label="封面"
        width="120">
          <template slot-scope="scope">
            <img :src="scope.row.cover.images[0]" width="90" alt>
          </template>
        </el-table-column>
        <el-table-column prop="title" label="标题" width="180"></el-table-column>
        <el-table-column prop="pubdate" label="发布日期" width="180"></el-table-column>
        <!-- <el-table-column prop="status" label="状态"></el-table-column> -->
        <el-table-column label="状态">
          <template slot-scope="scope">
            <el-tag :type="statTypes[scope.row.status].type">{{ statTypes[scope.row.status].label }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
             <el-button type="success" plain>修改</el-button>
            <el-button type="danger" plain @click="handleDelete(scope.row)">删除
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- /table 表格 -->

      <!-- 数据分页 -->
      <el-pagination background
      layout="prev, pager, next"
      :total="totalCount"
      @current-change='handleCurrentChange'
      :current-page="page"
      :disabled="artloading"></el-pagination>
      <!-- /数据分页 -->
    </el-card>
    <!-- /列表 -->
  </div>
</template>

<script>

export default {
  name: 'ArticleList',
  data () {
    return {
      articles: [],
      // 列表数据
      totalCount: 0,
      // 总数据条数
      page: 1,
      // 当前页码
      artloading: false,
      // 控制文档加载中的 loading 效果
      statTypes: [
      // 文章状态
        {
          type: 'info',
          label: '草稿'
        },
        {
          type: '',
          label: '待审核'
        },
        {
          type: 'success',
          label: '审核通过'
        },
        {
          type: 'warning',
          label: '审核失败'
        },
        {
          type: 'danger',
          label: '已删除'
        }
      ],
      channels: [], // 频道列表
      filter: { // 文章查询条件参数
        status: '', // 文章状态
        channel_id: '', // 频道id
        begin_pubdate: '', // 开始时间
        end_pubdate: '' // 结束时间
      },
      begin_end_pubdate: []
    }
  },
  created () {
    // 加载文章列表
    this.loadArticles()

    // 加载频道列表
    this.loadChannels()
  },

  methods: {
    loadArticles (page = 1) {
      this.artloading = true
      // 过滤出有效的查询条件数据字段
      const filterDate = {}
      for (let key in this.filter) {
        if (this.filter[key]) {
          filterDate[key] = this.filter[key]
        }
      }
      // console.log(filter)

      this.$http({
        method: 'GET',
        url: '/articles',
        params: {
          page,
          per_page: 10,
          ...filterDate
        }
      }).then(data => {
        this.articles = data.results// 列表数据
        this.totalCount = data.total_count// 总记录数
        this.artloading = false
      })
    },
    loadChannels () {
      this.$http({
        method: 'GET',
        url: '/channels'
      }).then(data => {
        this.channels = data.channels
      })
    },
    onSubmit () {
      // console.log('submit!')
      this.page = 1
      this.loadArticles()
    },
    handleCurrentChange (page) {
      this.page = page
      this.loadArticles(page)
    },

    handleDelete (articles) {
      this.$confirm('要删除吗?', '删除提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          method: 'DELETE',
          url: `/articles/${articles.id}`
        }).then(data => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
          this.loadArticles(this.page)
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    },
    handleChange (value) {
      this.filter.begin_pubdate = value[0]
      this.filter.end_pubdate = value[1]
    }
  }
}
</script>

<style  scoped lang='less'>
.filter-card {
  margin-bottom: 20px;
}
.list-table {
  margin-bottom: 30px;
}
</style>

<template>
  <section>
    <el-row>
      <el-col :span="24">
        <br>
        <!--查询表单-->
        <el-form :inline="true" class="demo-form-inline">
          <el-form-item label="标题">
<el-input v-model="searchMap.title" placeholder="标题"></el-input></el-form-item>
          <el-form-item label="内容">
<el-input v-model="searchMap.content" placeholder="内容"></el-input></el-form-item>
          <el-form-item label="创建日期">
<el-input v-model="searchMap.createtime" placeholder="创建日期"></el-input></el-form-item>
          <el-form-item label="修改日期">
<el-input v-model="searchMap.updatetime" placeholder="修改日期"></el-input></el-form-item>
          <el-form-item label="用户ID">
<el-input v-model="searchMap.userid" placeholder="用户ID"></el-input></el-form-item>
          <el-form-item label="昵称">
<el-input v-model="searchMap.nikename" placeholder="昵称"></el-input></el-form-item>
          <el-form-item label="浏览量">
<el-input v-model="searchMap.visits" placeholder="浏览量"></el-input></el-form-item>
          <el-form-item label="点赞数">
<el-input v-model="searchMap.thumbup" placeholder="点赞数"></el-input></el-form-item>
          <el-form-item label="回复数">
<el-input v-model="searchMap.reply" placeholder="回复数"></el-input></el-form-item>
          <el-form-item label="是否解决">
<el-input v-model="searchMap.solve" placeholder="是否解决"></el-input></el-form-item>

          <el-button type="primary" @click="fetchData()">查询</el-button>
          <el-button type="primary" icon="el-icon-circle-plus" @click="handleEdit('')">新增</el-button>
        </el-form>
        <!--表格-->
        <el-table
          :data="list"
          border
          style="width: 100%">
          <el-table-column prop="id" label="ID" width="80"></el-table-column>
          <el-table-column prop="title" label="标题" width="80"></el-table-column>
          <el-table-column prop="content" label="内容" width="80"></el-table-column>
          <el-table-column prop="createtime" label="创建日期" width="80"></el-table-column>
          <el-table-column prop="updatetime" label="修改日期" width="80"></el-table-column>
          <el-table-column prop="userid" label="用户ID" width="80"></el-table-column>
          <el-table-column prop="nikename" label="昵称" width="80"></el-table-column>
          <el-table-column prop="visits" label="浏览量" width="80"></el-table-column>
          <el-table-column prop="thumbup" label="点赞数" width="80"></el-table-column>
          <el-table-column prop="reply" label="回复数" width="80"></el-table-column>
          <el-table-column prop="solve" label="是否解决" width="80"></el-table-column>

          <el-table-column label="操作">
            <template slot-scope="scope">
              <el-button type="warning" plain size="small" @click="handleEdit(scope.row.id)" >修改</el-button>
              <el-button type="warning" plain size="small" @click="handleDelete(scope.row.id)" >删除</el-button>
            </template>
          </el-table-column>
        </el-table>
        <div class="block">
          <el-pagination
            @current-change="fetchData"
            :current-page="currentPage"
            :page-size="10"
            layout="prev, pager, next, jumper"
            :total="total">
          </el-pagination>
        </div>
        <!--编辑窗口-->
        <el-dialog title="编辑" :visible.sync="dialogFormVisible" >
          <el-form ref="form" :model="pojo" label-width="80px">
        <el-form-item label="标题"><el-input v-model="pojo.title"></el-input></el-form-item>
        <el-form-item label="内容"><el-input v-model="pojo.content"></el-input></el-form-item>
        <el-form-item label="创建日期"><el-input v-model="pojo.createtime"></el-input></el-form-item>
        <el-form-item label="修改日期"><el-input v-model="pojo.updatetime"></el-input></el-form-item>
        <el-form-item label="用户ID"><el-input v-model="pojo.userid"></el-input></el-form-item>
        <el-form-item label="昵称"><el-input v-model="pojo.nikename"></el-input></el-form-item>
        <el-form-item label="浏览量"><el-input v-model="pojo.visits"></el-input></el-form-item>
        <el-form-item label="点赞数"><el-input v-model="pojo.thumbup"></el-input></el-form-item>
        <el-form-item label="回复数"><el-input v-model="pojo.reply"></el-input></el-form-item>
        <el-form-item label="是否解决"><el-input v-model="pojo.solve"></el-input></el-form-item>

            <el-form-item>
              <el-button type="primary" @click="handleSave" >修改</el-button>
              <el-button @click="dialogFormVisible = false">取消</el-button>
            </el-form-item>
          </el-form>
        </el-dialog>
      </el-col>
    </el-row>
  </section>
</template>
<script>
  import problem from '@/api/problem'
  import message from '@/utils/message'
  export default {
    data() {
      return {
        list: null,
        listLoading: true,
        total: 0,
        currentPage: 1,
        searchMap: {},
        pojo: {},
        dialogFormVisible: false,
        id: ''
      }
    },
    created() {
      this.fetchData()
    },
    methods: {
      fetchData() {
        this.listLoading = true
        problem.search(this.currentPage, 10, this.searchMap).then(response => {
          if (response.flag === true) {
            this.list = response.data.rows
            this.total = response.data.total
          }
          this.listLoading = false
        })
      },
      handleEdit(id) {
        this.dialogFormVisible = true
        this.id = id
        if (this.id !== '') {
          problem.findById(this.id).then(response => {
            if (response.flag === true) {
              this.pojo = response.data
            }
          })
        } else {
          this.pojo = {} // 清空表单
        }
      },
      handleSave() {
        if (this.id !== '') {
          message.handleShowMessage(problem.update(this.id, this.pojo), this)
        } else {
          message.handleShowMessage(problem.save(this.id, this.pojo), this)
        }
        this.dialogFormVisible = false // 隐藏窗口
      },
      handleDelete(id) {
        this.$confirm('确定要删除吗？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          message.handleShowMessage(problem.deleteById(id), this)
        })
      }
    }
  }
</script>


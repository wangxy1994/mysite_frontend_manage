<template>
  <section>
    <el-row>
      <el-col :span="24">
        <br>
        <!--查询表单-->
        <el-form :inline="true" class="demo-form-inline">
          <el-form-item label="问题ID">
<el-input v-model="searchMap.problemid" placeholder="问题ID"></el-input></el-form-item>
          <el-form-item label="回答内容">
<el-input v-model="searchMap.content" placeholder="回答内容"></el-input></el-form-item>
          <el-form-item label="创建日期">
<el-input v-model="searchMap.createtime" placeholder="创建日期"></el-input></el-form-item>
          <el-form-item label="更新日期">
<el-input v-model="searchMap.updatetime" placeholder="更新日期"></el-input></el-form-item>
          <el-form-item label="回答人ID">
<el-input v-model="searchMap.userid" placeholder="回答人ID"></el-input></el-form-item>
          <el-form-item label="回答人昵称">
<el-input v-model="searchMap.nikename" placeholder="回答人昵称"></el-input></el-form-item>

          <el-button type="primary" @click="fetchData()">查询</el-button>
          <el-button type="primary" icon="el-icon-circle-plus" @click="handleEdit('')">新增</el-button>
        </el-form>
        <!--表格-->
        <el-table
          :data="list"
          border
          style="width: 100%">
          <el-table-column prop="id" label="编号" width="80"></el-table-column>
          <el-table-column prop="problemid" label="问题ID" width="80"></el-table-column>
          <el-table-column prop="content" label="回答内容" width="80"></el-table-column>
          <el-table-column prop="createtime" label="创建日期" width="80"></el-table-column>
          <el-table-column prop="updatetime" label="更新日期" width="80"></el-table-column>
          <el-table-column prop="userid" label="回答人ID" width="80"></el-table-column>
          <el-table-column prop="nikename" label="回答人昵称" width="80"></el-table-column>

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
        <el-form-item label="问题ID"><el-input v-model="pojo.problemid"></el-input></el-form-item>
        <el-form-item label="回答内容"><el-input v-model="pojo.content"></el-input></el-form-item>
        <el-form-item label="创建日期"><el-input v-model="pojo.createtime"></el-input></el-form-item>
        <el-form-item label="更新日期"><el-input v-model="pojo.updatetime"></el-input></el-form-item>
        <el-form-item label="回答人ID"><el-input v-model="pojo.userid"></el-input></el-form-item>
        <el-form-item label="回答人昵称"><el-input v-model="pojo.nikename"></el-input></el-form-item>

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
  import reply from '@/api/reply'
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
        reply.search(this.currentPage, 10, this.searchMap).then(response => {
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
          reply.findById(this.id).then(response => {
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
          message.handleShowMessage(reply.update(this.id, this.pojo), this)
        } else {
          message.handleShowMessage(reply.save(this.id, this.pojo), this)
        }
        this.dialogFormVisible = false // 隐藏窗口
      },
      handleDelete(id) {
        this.$confirm('确定要删除吗？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          message.handleShowMessage(reply.deleteById(id), this)
        })
      }
    }
  }
</script>


<template>
  <section>
    <el-row>
      <el-col :span="24">
        <br>
        <!--查询表单-->
        <el-form :inline="true" class="demo-form-inline">
          <el-form-item label="标签名称">
<el-input v-model="searchForm.labelname" placeholder="标签名称"></el-input></el-form-item>
          <el-form-item label="状态">
<el-input v-model="searchForm.state" placeholder="状态"></el-input></el-form-item>
          <el-form-item label="使用数量">
<el-input v-model="searchForm.count" placeholder="使用数量"></el-input></el-form-item>
          <el-form-item label="是否推荐">
<el-input v-model="searchForm.recommend" placeholder="是否推荐"></el-input></el-form-item>
          <el-form-item label="上级ID">
<el-input v-model="searchForm.pid" placeholder="上级ID"></el-input></el-form-item>

          <el-button type="primary" @click="fetchData()">查询</el-button>
          <el-button type="primary" icon="el-icon-circle-plus" @click="handleEdit('')">新增</el-button>
        </el-form>
        <!--表格-->
        <el-table
          :data="list"
          border
          style="width: 100%">
          <el-table-column prop="id" label="标签ID" width="80"></el-table-column>
          <el-table-column prop="labelname" label="标签名称" width="80"></el-table-column>
          <el-table-column prop="state" label="状态" width="80"></el-table-column>
          <el-table-column prop="count" label="使用数量" width="80"></el-table-column>
          <el-table-column prop="recommend" label="是否推荐" width="80"></el-table-column>
          <el-table-column prop="pid" label="上级ID" width="80"></el-table-column>

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
        <el-form-item label="标签名称"><el-input v-model="form.labelname"></el-input></el-form-item>
        <el-form-item label="状态"><el-input v-model="form.state"></el-input></el-form-item>
        <el-form-item label="使用数量"><el-input v-model="form.count"></el-input></el-form-item>
        <el-form-item label="是否推荐"><el-input v-model="form.recommend"></el-input></el-form-item>
        <el-form-item label="上级ID"><el-input v-model="form.pid"></el-input></el-form-item>

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
  import label from '@/api/label'
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
        label.search(this.currentPage, 10, this.searchMap).then(response => {
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
          label.findById(this.id).then(response => {
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
          message.handleShowMessage(label.update(this.id, this.pojo), this)
        } else {
          message.handleShowMessage(label.save(this.id, this.pojo), this)
        }
        this.dialogFormVisible = false // 隐藏窗口
      },
      handleDelete(id) {
        this.$confirm('确定要删除吗？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          message.handleShowMessage(label.deleteById(id), this)
        })
      }
    }
  }
</script>


<template>
  <section>
    <el-row>
      <el-col :span="24">
        <br>
        <!--查询表单-->
        <el-form :inline="true" class="demo-form-inline">
          <el-form-item label="标题">
<el-input v-model="searchMap.title" placeholder="标题"></el-input></el-form-item>
          <el-form-item label="文章正文">
<el-input v-model="searchMap.content" placeholder="文章正文"></el-input></el-form-item>          
          <el-button type="primary" @click="fetchData()">查询</el-button>
        </el-form>
        <!--表格-->
        <el-table
          :data="list"
          border
          style="width: 100%">
          <el-table-column prop="id" label="ID" width="80"></el-table-column>
          <el-table-column prop="columnid" label="专栏ID" width="80"></el-table-column>
          <el-table-column prop="userid" label="用户ID" width="80"></el-table-column>
          <el-table-column prop="title" label="标题" width="80"></el-table-column>         
          <el-table-column prop="image" label="文章封面" width="80"></el-table-column>
          <el-table-column prop="createtime" label="发表日期" width="80"></el-table-column>
          <el-table-column prop="ispublic" label="是否公开" width="80"></el-table-column>
          <el-table-column prop="istop" label="是否置顶" width="80"></el-table-column>         
          <el-table-column prop="state" label="审核状态" width="80"></el-table-column>
          <el-table-column prop="channelid" label="所属频道" width="80"></el-table-column>
          <el-table-column prop="url" label="URL" width="80"></el-table-column>
          <el-table-column prop="type" label="类型" width="80"></el-table-column>

          <el-table-column label="操作">
            <template slot-scope="scope">
              <el-button type="warning" plain size="small" @click="handleEdit(scope.row.id)" >详情</el-button>
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
        <el-dialog title="详情" :visible.sync="dialogFormVisible" >
        {{pojo.title}}
        <hr>
        <div v-html='pojo.content'></div>

        <el-button type="success" @click="handleExamine(pojo.id)" >审核通过</el-button>
        <el-button type="danger" @click="handleDelete(pojo.id)" >删除</el-button>
        <el-button @click="dialogFormVisible = false">关闭</el-button>           
        </el-dialog>
      </el-col>
    </el-row>
  </section>
</template>
<script>
  import article from '@/api/article'
  import message from '@/utils/message'
  export default {
    data() {
      return {
        list: null,
        listLoading: true,
        total: 0,
        currentPage: 1,
        searchMap: { state: '0' },
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
        article.search(this.currentPage, 10, this.searchMap).then(response => {
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
          article.findById(this.id).then(response => {
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
          message.handleShowMessage(article.update(this.id, this.pojo), this)
        } else {
          message.handleShowMessage(article.save(this.id, this.pojo), this)
        }
        this.dialogFormVisible = false // 隐藏窗口
      },
      handleDelete(id) {
        this.$confirm('确定要删除吗？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          message.handleShowMessage(article.deleteById(id), this)
          this.dialogFormVisible = false // 隐藏窗口
        })
      },
      handleExamine(id) {
        this.$confirm('确定要审核通过吗？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          message.handleShowMessage(article.examine(id), this)
          this.dialogFormVisible = false // 隐藏窗口
        })
      }
    }
  }
</script>


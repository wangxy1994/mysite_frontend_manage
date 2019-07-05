<template>
  <section>
    <el-row>
      <el-col :span="24">
        <br>
        <!--查询表单-->
        <el-form :inline="true" class="demo-form-inline">
          <el-form-item label="企业名称">
<el-input v-model="searchMap.name" placeholder="企业名称"></el-input></el-form-item>
          <el-form-item label="企业简介">
<el-input v-model="searchMap.summary" placeholder="企业简介"></el-input></el-form-item>
          <el-form-item label="企业地址">
<el-input v-model="searchMap.address" placeholder="企业地址"></el-input></el-form-item>
          <el-form-item label="标签列表">
<el-input v-model="searchMap.labels" placeholder="标签列表"></el-input></el-form-item>
          <el-form-item label="坐标">
<el-input v-model="searchMap.coordinate" placeholder="坐标"></el-input></el-form-item>
          <el-form-item label="是否热门">
<el-input v-model="searchMap.ishot" placeholder="是否热门"></el-input>

</el-form-item>

          <el-button type="primary" @click="fetchData()">查询</el-button>
          <el-button type="primary" icon="el-icon-circle-plus" @click="handleEdit('')">新增</el-button>
        </el-form>
        <!--表格-->
        <el-table
          :data="list"
          border
          style="width: 100%">
          <el-table-column prop="id" label="ID" width="80"></el-table-column>
          <el-table-column prop="name" label="企业名称" width="80"></el-table-column>
          <el-table-column prop="summary" label="企业简介" width="80"></el-table-column>
          <el-table-column prop="address" label="企业地址" width="80"></el-table-column>
          <el-table-column prop="labels" label="标签列表" width="80"></el-table-column>
          <el-table-column prop="coordinate" label="坐标" width="80"></el-table-column>
          <el-table-column prop="ishot" label="是否热门" width="80"></el-table-column>

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
        <el-form-item label="企业名称"><el-input v-model="pojo.name"></el-input></el-form-item>
        <el-form-item label="企业简介"><el-input v-model="pojo.summary" type="textarea" :rows="4"></el-input></el-form-item>
        <el-form-item label="企业地址"><el-input v-model="pojo.address"></el-input></el-form-item>
        <el-form-item label="标签列表"><el-input v-model="pojo.labels"></el-input></el-form-item>
        <el-form-item label="坐标"><el-input v-model="pojo.coordinate"></el-input></el-form-item>
        <el-form-item label="是否热门">
          <el-switch  placeholder="是否热门" on-text="" off-text=""  active-value="1" inactive-value="0" v-model="pojo.ishot" ></el-switch>
        </el-form-item>

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
  import enterprise from '@/api/enterprise'
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
        enterprise.search(this.currentPage, 10, this.searchMap).then(response => {
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
          enterprise.findById(this.id).then(response => {
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
          message.handleShowMessage(enterprise.update(this.id, this.pojo), this)
        } else {
          message.handleShowMessage(enterprise.save(this.id, this.pojo), this)
        }
        this.dialogFormVisible = false // 隐藏窗口
      },
      handleDelete(id) {
        this.$confirm('确定要删除吗？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          message.handleShowMessage(enterprise.deleteById(id), this)
        })
      }
    }
  }
</script>


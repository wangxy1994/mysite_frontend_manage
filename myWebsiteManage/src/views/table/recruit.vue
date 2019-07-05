<template>
  <section>
    <el-row>
      <el-col :span="24">
        <br>
        <!--查询表单-->
        <el-form :inline="true" class="demo-form-inline">
          <el-form-item label="职位名称">
<el-input v-model="searchMap.jobname" placeholder="职位名称"></el-input></el-form-item>
          <el-form-item label="薪资范围">
<el-input v-model="searchMap.salary" placeholder="薪资范围"></el-input></el-form-item>
          <el-form-item label="经验要求">
<el-input v-model="searchMap.condition" placeholder="经验要求"></el-input></el-form-item>
          <el-form-item label="学历要求">
<el-input v-model="searchMap.education" placeholder="学历要求"></el-input></el-form-item>
          <el-form-item label="任职方式">
<el-input v-model="searchMap.type" placeholder="任职方式"></el-input></el-form-item>
          <el-form-item label="办公地址">
<el-input v-model="searchMap.address" placeholder="办公地址"></el-input></el-form-item>
          <el-form-item label="企业ID">
<el-input v-model="searchMap.eid" placeholder="企业ID"></el-input></el-form-item>
          <el-form-item label="创建日期">
<el-input v-model="searchMap.createtime" placeholder="创建日期"></el-input></el-form-item>
          <el-form-item label="状态">
<el-input v-model="searchMap.state" placeholder="状态"></el-input></el-form-item>
          <el-form-item label="网址">
<el-input v-model="searchMap.url" placeholder="网址"></el-input></el-form-item>

          <el-button type="primary" @click="fetchData()">查询</el-button>
          <el-button type="primary" icon="el-icon-circle-plus" @click="handleEdit('')">新增</el-button>
        </el-form>
        <!--表格-->
        <el-table
          :data="list"
          border
          style="width: 100%">
          <el-table-column prop="id" label="ID" width="80"></el-table-column>
          <el-table-column prop="jobname" label="职位名称" width="80"></el-table-column>
          <el-table-column prop="salary" label="薪资范围" width="80"></el-table-column>
          <el-table-column prop="condition" label="经验要求" width="80"></el-table-column>
          <el-table-column prop="education" label="学历要求" width="80"></el-table-column>
          <el-table-column prop="type" label="任职方式" width="80"></el-table-column>
          <el-table-column prop="address" label="办公地址" width="80"></el-table-column>
          <el-table-column prop="eid" label="企业ID" width="80"></el-table-column>
          <el-table-column prop="createtime" label="创建日期" width="80"></el-table-column>
          <el-table-column prop="state" label="状态" width="80"></el-table-column>
          <el-table-column prop="url" label="网址" width="80"></el-table-column>

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
        <el-form-item label="职位名称"><el-input v-model="pojo.jobname"></el-input></el-form-item>
        <el-form-item label="薪资范围"><el-input v-model="pojo.salary"></el-input></el-form-item>
        <el-form-item label="经验要求"><el-input v-model="pojo.condition"></el-input></el-form-item>
        <el-form-item label="学历要求"><el-input v-model="pojo.education"></el-input></el-form-item>
        <el-form-item label="任职方式">
            <el-radio v-model="pojo.type" label="1">全职</el-radio>
            <el-radio v-model="pojo.type" label="2">兼职</el-radio>
        </el-form-item>

        <el-form-item label="办公地址"><el-input v-model="pojo.address"></el-input></el-form-item>
        <el-form-item label="企业ID">
          <el-select v-model="pojo.eid" filterable placeholder="请选择">
            <el-option
              v-for="item in enterpriseList"
              :key="item.id"
              :label="item.name"
              :value="item.id">
            </el-option>
          </el-select>        
        </el-form-item>
        <el-form-item label="状态">
           <el-switch  placeholder="是否热门" on-text="" off-text=""  active-value="1" inactive-value="0" v-model="pojo.state" ></el-switch>
        </el-form-item>
        <el-form-item label="网址">
          <el-input v-model="pojo.url" placeholder="请输入网址">
           <template slot="prepend">http://</template>
          </el-input></el-form-item>

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
  import recruit from '@/api/recruit'
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
        id: '',
        enterpriseList: []
      }
    },
    created() {
      this.fetchData()
      enterprise.getList().then(response => { // 企业列表
        if (response.flag === true) {
          this.enterpriseList = response.data
        }
      })
    },
    methods: {
      fetchData() {
        this.listLoading = true
        recruit.search(this.currentPage, 10, this.searchMap).then(response => {
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
          recruit.findById(this.id).then(response => {
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
          message.handleShowMessage(recruit.update(this.id, this.pojo), this)
        } else {
          message.handleShowMessage(recruit.save(this.id, this.pojo), this)
        }
        this.dialogFormVisible = false // 隐藏窗口
      },
      handleDelete(id) {
        this.$confirm('确定要删除吗？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          message.handleShowMessage(recruit.deleteById(id), this)
        })
      }
    }
  }
</script>


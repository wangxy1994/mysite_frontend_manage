<template>
  <section>
    <el-row>
      <el-col :span="24">
        <br>
        <!--查询表单-->
        <el-form :inline="true" class="demo-form-inline">
          <el-form-item label="活动名称">
            <el-input v-model="searchMap.name" placeholder="活动名称"></el-input>
          </el-form-item>
          <el-form-item label="活动日期" >
            <el-date-picker  type="date" placeholder="选择开始日期" v-model="searchMap.starttime_1" ></el-date-picker>
            <el-date-picker  type="date" placeholder="选择截止日期" v-model="searchMap.starttime_2" ></el-date-picker>
          </el-form-item>
          <el-button type="primary" @click="fetchData()">查询</el-button>
          <el-button type="primary" icon="el-icon-circle-plus" @click="handleEdit('')">新增</el-button>
        </el-form>
        <!--表格-->
        <el-table
          :data="list"
          border
          style="width: 100%">
          <el-table-column type="selection">
          </el-table-column>
          <el-table-column
            prop="id"
            label="ID"
            width="80">
          </el-table-column>
          <el-table-column
            prop="name"
            label="活动名称"
            width="180">
          </el-table-column>
          <el-table-column
            prop="address"
            label="活动地址">
          </el-table-column>
          <el-table-column
            prop="starttime"
            label="开始日期">
          </el-table-column>
          <el-table-column
            prop="endtime"
            label="截止日期">
          </el-table-column>
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
            <el-form-item label="活动名称">
              <el-input v-model="pojo.name"></el-input>
            </el-form-item>
            <el-form-item label="基本地点">
              <el-input v-model="pojo.address"></el-input>
            </el-form-item>
            <el-form-item label="开始日期">
              <el-date-picker type="date" placeholder="选择日期" v-model="pojo.starttime" style="width: 100%;" ></el-date-picker>
            </el-form-item>
            <el-form-item label="截止日期">
              <el-date-picker type="date" placeholder="选择日期" v-model="pojo.endtime" style="width: 100%;" ></el-date-picker>
            </el-form-item>
            <el-form-item label="报名截止">
              <el-date-picker type="date" placeholder="选择日期" v-model="pojo.enrolltime" style="width: 100%;" ></el-date-picker>
            </el-form-item>
            <el-form-item label="城市">
              <el-select v-model="pojo.city" placeholder="请选择">
                <el-option
                  v-for="item in cityList"
                  :key="item.id"
                  :label="item.name"
                  :value="item.id">
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="活动详情">
              <el-input v-model="pojo.detail" type="textarea"> </el-input>
            </el-form-item>
            <el-form-item label="是否可见" prop="delivery">
              <el-switch on-text="" off-text=""  active-value="1" inactive-value="0" v-model="pojo.state" ></el-switch>
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
  import gathering from '@/api/gathering'
  import city from '@/api/city'
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
        cityList: []
      }
    },
    created() {
      this.fetchData()
      city.getList().then(response => {
        this.cityList = response.data
      })
    },
    methods: {
      fetchData() {
        this.listLoading = true
        gathering.search(this.currentPage, 10, this.searchMap).then(response => {
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
          gathering.findById(this.id).then(response => {
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
          message.handleShowMessage(gathering.update(this.id, this.pojo), this)
        } else {
          message.handleShowMessage(gathering.save(this.id, this.pojo), this)
        }
        this.dialogFormVisible = false // 隐藏窗口
      },
      handleDelete(id) {
        this.$confirm('确定要删除吗？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          message.handleShowMessage(gathering.deleteById(id), this)
        })
      }
    }
  }
</script>


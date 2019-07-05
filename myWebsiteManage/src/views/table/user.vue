<template>
  <section>
    <el-row>
      <el-col :span="24">
        <br>
        <!--查询表单-->
        <el-form :inline="true" class="demo-form-inline">
          <el-form-item label="登陆名">
<el-input v-model="searchMap.loginname" placeholder="登陆名"></el-input></el-form-item>
          <el-form-item label="密码">
<el-input v-model="searchMap.password" placeholder="密码"></el-input></el-form-item>
          <el-form-item label="昵称">
<el-input v-model="searchMap.nickname" placeholder="昵称"></el-input></el-form-item>
          <el-form-item label="性别">
<el-input v-model="searchMap.sex" placeholder="性别"></el-input></el-form-item>
          <el-form-item label="出生年月日">
<el-input v-model="searchMap.birthday" placeholder="出生年月日"></el-input></el-form-item>
          <el-form-item label="头像">
<el-input v-model="searchMap.image" placeholder="头像"></el-input></el-form-item>
          <el-form-item label="手机号码">
<el-input v-model="searchMap.mobile" placeholder="手机号码"></el-input></el-form-item>
          <el-form-item label="E-Mail">
<el-input v-model="searchMap.email" placeholder="E-Mail"></el-input></el-form-item>
          <el-form-item label="注册日期">
<el-input v-model="searchMap.regdate" placeholder="注册日期"></el-input></el-form-item>
          <el-form-item label="修改日期">
<el-input v-model="searchMap.updatedate" placeholder="修改日期"></el-input></el-form-item>
          <el-form-item label="最后登陆日期">
<el-input v-model="searchMap.lastdate" placeholder="最后登陆日期"></el-input></el-form-item>
          <el-form-item label="在线时长（分钟）">
<el-input v-model="searchMap.online" placeholder="在线时长（分钟）"></el-input></el-form-item>
          <el-form-item label="兴趣">
<el-input v-model="searchMap.interest" placeholder="兴趣"></el-input></el-form-item>
          <el-form-item label="个性">
<el-input v-model="searchMap.personality" placeholder="个性"></el-input></el-form-item>
          <el-form-item label="粉丝数">
<el-input v-model="searchMap.fanscount" placeholder="粉丝数"></el-input></el-form-item>
          <el-form-item label="关注数">
<el-input v-model="searchMap.followcount" placeholder="关注数"></el-input></el-form-item>

          <el-button type="primary" @click="fetchData()">查询</el-button>
          <el-button type="primary" icon="el-icon-circle-plus" @click="handleEdit('')">新增</el-button>
        </el-form>
        <!--表格-->
        <el-table
          :data="list"
          border
          style="width: 100%">
          <el-table-column prop="id" label="ID" width="80"></el-table-column>
          <el-table-column prop="loginname" label="登陆名" width="80"></el-table-column>
          <el-table-column prop="password" label="密码" width="80"></el-table-column>
          <el-table-column prop="nickname" label="昵称" width="80"></el-table-column>
          <el-table-column prop="sex" label="性别" width="80"></el-table-column>
          <el-table-column prop="birthday" label="出生年月日" width="80"></el-table-column>
          <el-table-column prop="image" label="头像" width="80"></el-table-column>
          <el-table-column prop="mobile" label="手机号码" width="80"></el-table-column>
          <el-table-column prop="email" label="E-Mail" width="80"></el-table-column>
          <el-table-column prop="regdate" label="注册日期" width="80"></el-table-column>
          <el-table-column prop="updatedate" label="修改日期" width="80"></el-table-column>
          <el-table-column prop="lastdate" label="最后登陆日期" width="80"></el-table-column>
          <el-table-column prop="online" label="在线时长（分钟）" width="80"></el-table-column>
          <el-table-column prop="interest" label="兴趣" width="80"></el-table-column>
          <el-table-column prop="personality" label="个性" width="80"></el-table-column>
          <el-table-column prop="fanscount" label="粉丝数" width="80"></el-table-column>
          <el-table-column prop="followcount" label="关注数" width="80"></el-table-column>

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
        <el-form-item label="登陆名"><el-input v-model="pojo.loginname"></el-input></el-form-item>
        <el-form-item label="密码"><el-input v-model="pojo.password"></el-input></el-form-item>
        <el-form-item label="昵称"><el-input v-model="pojo.nickname"></el-input></el-form-item>
        <el-form-item label="性别"><el-input v-model="pojo.sex"></el-input></el-form-item>
        <el-form-item label="出生年月日"><el-input v-model="pojo.birthday"></el-input></el-form-item>
        <el-form-item label="头像"><el-input v-model="pojo.image"></el-input></el-form-item>
        <el-form-item label="手机号码"><el-input v-model="pojo.mobile"></el-input></el-form-item>
        <el-form-item label="E-Mail"><el-input v-model="pojo.email"></el-input></el-form-item>
        <el-form-item label="注册日期"><el-input v-model="pojo.regdate"></el-input></el-form-item>
        <el-form-item label="修改日期"><el-input v-model="pojo.updatedate"></el-input></el-form-item>
        <el-form-item label="最后登陆日期"><el-input v-model="pojo.lastdate"></el-input></el-form-item>
        <el-form-item label="在线时长（分钟）"><el-input v-model="pojo.online"></el-input></el-form-item>
        <el-form-item label="兴趣"><el-input v-model="pojo.interest"></el-input></el-form-item>
        <el-form-item label="个性"><el-input v-model="pojo.personality"></el-input></el-form-item>
        <el-form-item label="粉丝数"><el-input v-model="pojo.fanscount"></el-input></el-form-item>
        <el-form-item label="关注数"><el-input v-model="pojo.followcount"></el-input></el-form-item>

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
  import user from '@/api/user'
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
        user.search(this.currentPage, 10, this.searchMap).then(response => {
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
          user.findById(this.id).then(response => {
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
          message.handleShowMessage(user.update(this.id, this.pojo), this)
        } else {
          message.handleShowMessage(user.save(this.id, this.pojo), this)
        }
        this.dialogFormVisible = false // 隐藏窗口
      },
      handleDelete(id) {
        this.$confirm('确定要删除吗？', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          message.handleShowMessage(user.deleteById(id), this)
        })
      }
    }
  }
</script>


<template>
  <div>
    <el-dialog
      title="警告"
      :visible.sync="InvalidInputDialogVisible"
      width="30%"
      :show-close="false"
    >
      <span>输入无效</span>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="InvalidInputDialogVisible = false">确 定</el-button>
      </span>
    </el-dialog>
    <el-dialog
      title="修改"
      :visible.sync="updatedialogVisible"
      width="30%"
    >
      <el-form :model="updateData" :inline="true">
        <el-form-item label="序号">
          <el-input v-model="updateData.id" :disabled="true" size="small" />
        </el-form-item>
        <el-form-item label="昵称">
          <el-input v-model="updateData.username" size="small" placeholder="昵称" />
        </el-form-item>
        <!-- <el-form-item label="头像链接">
          <el-input v-model="updateData.headimg_url" size="small" placeholder="头像链接" />
        </el-form-item> -->
        <el-form-item label="学院">
          <el-input v-model="updateData.faculty" size="small" placeholder="学院" />
        </el-form-item>
        <el-form-item label="地址">
          <el-input v-model="updateData.address" size="small" placeholder="地址" />
        </el-form-item>
        <el-form-item label="QQ号">
          <el-input v-model="updateData.qqnumber" size="small" placeholder="QQ号" />
        </el-form-item>
        <el-form-item label="微信号">
          <el-input v-model="updateData.vxnumber" size="small" placeholder="微信号" />
        </el-form-item>
        <!-- <el-form-item label="权限">
          <el-select v-model="updateData.author_level" size="small">
            <el-option v-for="(item, index) in author_levels" :key="index" :label="item" :value="item" />
          </el-select>
        </el-form-item> -->
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="commitEdit">确 定</el-button>
      </span>
    </el-dialog>
    <el-divider />
    <el-row>
      <el-col :span="1"><br></el-col>
      <el-col :span="23">
        <el-form :inline="true" :model="addData">
          <el-form-item label="昵称">
            <el-input v-model="addData.username" size="small" placeholder="昵称" />
          </el-form-item>
          <el-form-item label="学院">
            <el-input v-model="addData.faculty" size="small" placeholder="学院" />
          </el-form-item>
          <el-form-item label="地址">
            <el-input v-model="addData.address" size="small" placeholder="地址" />
          </el-form-item>
          <el-form-item label="qq号">
            <el-input v-model="addData.qqnumber" size="small" placeholder="QQ号" />
          </el-form-item>
          <el-form-item label="微信号">
            <el-input v-model="addData.vxnumber" size="small" placeholder="微信号" />
          </el-form-item>

          <!-- <el-form-item label="权限">
            <el-select v-model="addData.author_level" size="small" placeholder="权限">
              <el-option v-for="(item, index) in author_levels" :key="index" :label="item" :value="item" />
            </el-select>
          </el-form-item> -->
          <el-form-item>
            <el-button type="success" size="mini" @click="AddUser">添加</el-button>
            <el-button type="primary" size="mini" @click="Search">搜索</el-button>
            <el-button type="plain" size="mini" @click="getAllUser">刷新</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
    <el-table
      id="TableTop"
      :data="tableData.slice((currentPage-1)*pageSize,currentPage*pageSize)"
      style="width: 100%"
      @sort-change="SortById"
    >
      <el-table-column type="expand">
        <template slot-scope="props">
          <el-form label-position="left" inline class="demo-table-expand">
            <el-form-item label="id">
              <span>{{ props.row.id }}</span>
            </el-form-item>
            <el-form-item label="昵称">
              <span>{{ props.row.username }}</span>
            </el-form-item>
            <!-- <el-form-item label="头像链接">
              <span>{{ props.row.headimg_url }}</span>
            </el-form-item> -->
            <el-form-item label="学院">
              <span>{{ props.row.faculty }}</span>
            </el-form-item>
            <el-form-item label="地址">
              <span>{{ props.row.address }}</span>
            </el-form-item>
            <el-form-item label="QQ号">
              <span>{{ props.row.qqnumber }}</span>
            </el-form-item>
            <el-form-item label="微信号">
              <span>{{ props.row.vxnumber }}</span>
            </el-form-item>
            <!-- <el-form-item label="权限">
              <span>{{ props.row.author_level }}</span>
            </el-form-item> -->
          </el-form>
        </template>
      </el-table-column>
      <el-table-column
        align="center"
        prop="id"
        label="ID"
        sortable="custom"
      >
        <template slot-scope="scope">
          <span style="margin-left: 10px">{{ scope.row.id }}</span>
        </template>
      </el-table-column>
      <el-table-column
        align="center"
        label="昵称"
      >
        <template slot-scope="scope">
          <span style="margin-left: 10px">{{ scope.row.username }}</span>
        </template>
      </el-table-column>
      <el-table-column
        align="center"
        label="学院"
      >
        <template slot-scope="scope">
          <span style="margin-left: 10px">{{ scope.row.faculty }}</span>
        </template>
      </el-table-column>
      <el-table-column
        align="center"
        label="地址"
      >
        <template slot-scope="scope">
          <span style="margin-left: 10px">{{ scope.row.address }}</span>
        </template>
      </el-table-column>
      <el-table-column
        align="center"
        label="QQ号"
      >
        <template slot-scope="scope">
          <span style="margin-left: 10px">{{ scope.row.qqnumber }}</span>
        </template>
      </el-table-column>
      <el-table-column
        align="center"
        label="微信号"
      >
        <template slot-scope="scope">
          <span style="margin-left: 10px">{{ scope.row.vxnumber }}</span>
        </template>
      </el-table-column>
      <el-table-column
        align="center"
        label="操作"
      >
        <template slot-scope="scope">
          <el-button
            size="mini"
            type="warning"
            @click="handleEdit(scope.row.id, scope.row)"
          >编辑</el-button>
          <el-button
            size="mini"
            type="danger"
            @click="handleDelete(scope.row.id, scope.row)"
          >删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <div style="text-align: center;margin-top: 30px;">
      <el-pagination
        :current-page="currentPage"
        :page-size="pageSize"
        :pager-count="5"
        layout="total, pager"
        :total="tableData.length"
        @current-change="handleCurrentChange"
      />
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Index',
  data() {
    return {
      currentPage: 1,
      pageSize: 6,
      tableData: [],
      allData: [],
      InvalidInputDialogVisible: false,
      updatedialogVisible: false,
      author_levels: ['', 1, 2],
      addData: {
        'id': -1,
        'username': '',
        'faculty': '',
        'address': '',
        'qqnumber': '',
        'vxnumber': '',
        'author_level': 4
      },
      updateData: {
        'id': -1,
        'username': '',
        'faculty': '',
        'address': '',
        'qqnumber': '',
        'vxnumber': '',
        'author_level': 4
      },
      sortState: 0
    }
  },
  created() {
    var that = this
    that.getAllUser()
  },
  methods: {
    getAllUser() {
      console.log()
      var that = this
      axios.get(this.$store.state.userHeadUrl + 'getalluser').then(function(response) {
        that.allData = response.data
        that.tableData = response.data
        console.log(that.allData)
      }).catch(function(error) {
        console.log(error)
      })
    },
    deleteUserById(id) {
      axios.delete(this.$store.state.userHeadUrl + 'deleteuserbyid?id=' + id).then(function(response) {
        console.log(response.data)
      }).catch(function(error) {
        console.log(error)
      })
    },
    addUser() {
      var that = this
      axios({
        method: 'post',
        url: this.$store.state.userHeadUrl + 'adduser',
        data: {
          'id': that.addData.id,
          'username': that.addData.username,
          'faculty': that.addData.faculty,
          'address': that.addData.address,
          'qqnumber': that.addData.qqnumber,
          'vxnumber': that.addData.vxnumber
        },
        transformRequest: [
          function(data) {
            let ret = ''
            for (const it in data) {
              ret += encodeURIComponent(it) + '=' + encodeURIComponent(data[it]) + '&'
            }
            ret = ret.substring(0, ret.lastIndexOf('&'))
            console.log(ret)
            return ret
          }
        ],
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      }).then(function(response) {
        that.getAllUser()
      })
    },
    updateUser() {
      var that = this
      axios({
        method: 'put',
        url: this.$store.state.userHeadUrl + 'updateuser',
        data: {
          'id': that.updateData.id,
          'username': that.updateData.username,
          'faculty': that.updateData.faculty,
          'address': that.updateData.address,
          'qqnumber': that.updateData.qqnumber,
          'vxnumber': that.updateData.vxnumber
        },
        transformRequest: [
          function(data) {
            let ret = ''
            for (const it in data) {
              ret += encodeURIComponent(it) + '=' + encodeURIComponent(data[it]) + '&'
            }
            ret = ret.substring(0, ret.lastIndexOf('&'))
            console.log(ret)
            return ret
          }
        ],
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      }).then(function(response) {
        that.getAllUser()
      })
    },
    handleCurrentChange(currentPage) {
      this.currentPage = currentPage
      location.href = '#TableTop'
    },
    IndexOfId(id) {
      var index = -1
      for (var i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].id === id) {
          index = i
        }
      }
      return index
    },
    handleDelete(id, row) {
      var index = this.IndexOfId(id)
      console.log('index:' + index)
      this.tableData.splice(index, 1)
      if (this.tableData.length % this.pageSize === 0) {
        this.currentPage--
      }
      console.log('id:' + id)
      this.deleteUserById(id)
    },
    handleEdit(id, row) {
      this.updatedialogVisible = true
      var index = this.IndexOfId(id)
      this.updateData = JSON.parse(JSON.stringify(this.tableData[index]))
    },
    commitEdit() {
      this.updateUser()
      this.updatedialogVisible = false
    },
    AddUser() {
      var that = this
      if (that.addData.username === '' ||
        that.addData.author_level === '') {
        that.InvalidInputDialogVisible = true
        return
      }
      console.log(that.addData)
      that.addUser()
    },
    Search() {
      var that = this
      that.currentPage = 1
      that.tableData = that.allData.filter(item => {
        return item.username?.includes(that.addData.username) &&
        // (item.author_level === that.addData.author_level || that.addData.author_level === that.author_levels[0]) &&
          item.faculty?.includes(that.addData.faculty) &&
          item.address?.includes(that.addData.address)
      })
      // console.log(that.tableData)
    },
    SortById() {
      this.sortState = (this.sortState + 1) % 3
      if (this.sortState === 1) {
        this.tableData.reverse()
      } else if (this.sortState === 2) {
        this.tableData.reverse()
      }
      console.log(this.sortState)
    }
  }
}
</script>

<style scoped>
.demo-table-expand {
  font-size: 0;
}
.demo-table-expand label {
  width: 90px;
  color: #99a9bf;
}
.demo-table-expand .el-form-item {
  margin-right: 0;
  margin-bottom: 0;
  width: 50%;
}
</style>

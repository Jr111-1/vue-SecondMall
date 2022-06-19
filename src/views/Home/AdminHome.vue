<template>
  <div id="admin-home">
    <div class="header"></div>
    <div class="content">
      <el-tabs v-model="tabActive" @tab-click="handleClick">
        <el-tab-pane label="注册管理" name="first">
          <div class="item-reg">
            <el-table :data="regUsers" style="width: 100%">
              <el-table-column prop="createdOn" label="日期" width="180"></el-table-column>
              <el-table-column prop="name" label="姓名" width="180"></el-table-column>
              <el-table-column prop="email" label="邮箱"></el-table-column>
              <el-table-column label="是否允许">
                <template slot-scope="scope">
                  <el-button type="primary" @click="allowReg(scope.row.id)" @click.native.prevent="deleteRow(scope.$index, regUsers)">允许</el-button>
                  <el-button type="danger" @click="refuseReg(scope.row.id)" @click.native.prevent="deleteRow(scope.$index, regUsers)">拒绝</el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </el-tab-pane>
        <el-tab-pane label="权限管理" name="second">
          <div class="item-autho">
            <el-table :data="userList" style="width: 100%">
              <el-table-column prop="id" label="id" width="50"></el-table-column>
              <el-table-column label="头像" width="100">
                <template slot-scope="scope">
                  <el-image :src="scope.row.photoUrl" width="100" fit="cover"></el-image>
                </template>
              </el-table-column>
              <el-table-column prop="name" label="名称" width="100"></el-table-column>
              <el-table-column prop="biography" label="介绍" width="180"></el-table-column>
              <el-table-column prop="email" label="邮箱" width="180"></el-table-column>
              <el-table-column prop="sellerLvl" label="卖家等级"></el-table-column>
              <el-table-column label="操作" width="180">
                <template slot-scope="scope">
                  <el-button @click="showEditInfo(scope.row.id)" type="text" size="small">
                    修改信息
                  </el-button>
                  <el-button @click="showInfo(scope.row.id)" type="text" size="small">
                    查看信息
                  </el-button>
                  <el-button @click="banUser(scope.row.id)" type="danger" size="small">
                    禁用
                  </el-button>
                </template>
              </el-table-column>
<!--              <el-table-column label="给予权限" width="180">-->
<!--                <template slot-scope="scope">-->
<!--                  <el-dropdown>-->
<!--                    <el-button type="primary">-->
<!--                      权限<i class="el-icon-arrow-down el-icon&#45;&#45;right"></i>-->
<!--                    </el-button>-->
<!--                    <el-dropdown-menu slot="dropdown">-->
<!--                      <el-dropdown-item>审核商品</el-dropdown-item>-->
<!--                      <el-dropdown-item>审核注册</el-dropdown-item>-->
<!--                      <el-dropdown-item>全部</el-dropdown-item>-->
<!--                    </el-dropdown-menu>-->
<!--                  </el-dropdown>-->
<!--                </template>-->
<!--              </el-table-column>-->
            </el-table>
          </div>
        </el-tab-pane>
        <!-- 修改用户的对话框 -->
        <el-dialog title="修改用户" :visible.sync="editDialogVisible" width="50%">
          <el-form :model="users" label-width="70px">
            <el-form-item label="用户名">
              <el-input v-model="users.name"></el-input>
            </el-form-item>
            <el-form-item label="角色">
              <el-input v-model="users.role"></el-input>
            </el-form-item>
            <el-form-item label="简介">
              <el-input v-model="users.biography"></el-input>
            </el-form-item>
            <el-form-item label="商家等级" >
              <el-input v-model="users.sellerLvl"></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="editDialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="saveInfos()">确 定</el-button>
          </span>
        </el-dialog>
        <el-dialog title="修改用户" :visible.sync="infoDialogVisible" width="50%">
          <el-form :model="userInfo" label-width="70px">
            <el-form-item label="名称" prop="name">
              <el-input v-model="userInfo.name" disabled></el-input>
            </el-form-item>
            <el-form-item label="简介">
              <el-input v-model="userInfo.biography" disabled></el-input>
            </el-form-item>
            <el-form-item label="账户">
              <el-input v-model="userInfo.email" disabled></el-input>
            </el-form-item>
            <el-form-item label="姓名">
              <el-input v-model="userInfo.xingming" disabled></el-input>
            </el-form-item>
            <el-form-item label="城市">
              <el-input v-model="userInfo.city" disabled></el-input>
            </el-form-item>
            <el-form-item label="性别">
              <el-input v-model="userInfo.sex" disabled></el-input>
            </el-form-item>
            <el-form-item label="手机号">
              <el-input v-model="userInfo.tel" disabled></el-input>
            </el-form-item>
            <el-form-item label="银行卡号">
              <el-input v-model="userInfo.bankid" disabled></el-input>
            </el-form-item>
            <el-form-item label="余额">
              <el-input v-model="userInfo.money" disabled></el-input>
            </el-form-item>
            <el-form-item label="商家等级">
              <el-input v-model="userInfo.sellerLvl" disabled></el-input>
            </el-form-item>
          </el-form>
          <span slot="footer" class="dialog-footer">
            <el-button @click="infoDialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="infoDialogVisible = false">确 定</el-button>
          </span>
        </el-dialog>
        <el-tab-pane label="商品审核" name="third">
          <div class="item-audit">
            <el-table :data="auditCommos" style="width: 100%">
              <el-table-column label="图片" width="180">
                <template slot-scope="scope">
                  <el-image :src="scope.row.photoUrl" width="150" fit="contain"></el-image>
                </template>
              </el-table-column>
              <el-table-column prop="name" label="名称" width="180"></el-table-column>
              <el-table-column prop="description" label="简述" width="180"></el-table-column>
              <el-table-column prop="price" label="价格"></el-table-column>
              <el-table-column prop="quantity" label="数量"></el-table-column>
              <el-table-column label="是否允许" width="180">
                <template slot-scope="scope">
                  <el-button type="primary" @click="allowCommo(scope.row.id)" @click.native.prevent="deleteRow(scope.$index, auditCommos)" size="small">允许</el-button>
                  <el-button type="danger" @click="refuseCommo(scope.row.id)" @click.native.prevent="deleteRow(scope.$index, auditCommos)" size="small">拒绝</el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </el-tab-pane>
        <el-tab-pane label="商家审核" name="forth">
          <div class="item-audit">
            <el-table :data="licenseList" style="width: 100%">
              <el-table-column label="图片" width="100">
                <template slot-scope="scope">
                  <el-image :src="scope.row.license_photo" width="100" fit="contain"></el-image>
                </template>
              </el-table-column>
              <el-table-column label="图片" width="100">
                <template slot-scope="scope">
                  <el-image :src="scope.row.idPhoto" width="100" fit="contain"></el-image>
                </template>
              </el-table-column>
              <el-table-column prop="id" label="id"></el-table-column>
              <el-table-column prop="name" label="用户名" width="180"></el-table-column>
              <el-table-column prop="email" label="邮箱" width="180"></el-table-column>
              <el-table-column label="是否允许" width="180">
                <template slot-scope="scope">
                  <el-button type="primary" @click="allowLicense(scope.row.id)" @click.native.prevent="deleteRow(scope.$index, auditCommos)" size="small">允许</el-button>
                  <el-button type="danger" @click="refuseCommo(scope.row.id)" @click.native.prevent="deleteRow(scope.$index, auditCommos)" size="small">拒绝</el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
        </el-tab-pane>
      </el-tabs>
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      tabActive: 'first',
      regUsers: [{ id: 0, name: '', createOn: '', email: '' }],
      auditCommos: [],
      userList: [],
      licenseList: [],
      editDialogVisible: false,
      infoDialogVisible: false,
      users: {},
      userInfo: {}
    }
  },
  methods: {
    handleClick (tab, event) {

    },
    initData () {
      this.$axios.get('/apis/users').then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.regUsers = data.data
        }
      })
      this.$axios.get('/apis/users/all/').then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.userList = data.data
        }
      })
      this.$axios.get('/apis/users/checkLicense/').then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.licenseList = data.data
        }
      })
      this.$axios.get('/apis/commodities/audit').then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.auditCommos = data.data
        }
      })
    },
    showEditInfo (id) {
      this.$axios.get('/apis/users/getInfos/' + id).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '成功读取数据',
            type: 'success'
          })
          this.users = data.data
        }
      })
      this.editDialogVisible = true
    },
    showInfo (id) {
      this.$axios.get('/apis/users/getInfos/' + id).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '成功读取数据',
            type: 'success'
          })
          this.userInfo = data.data
        }
      })
      this.infoDialogVisible = true
    },
    banUser (id) {
      this.$axios.put('/apis/users/ban/' + id).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '成功禁用该用户',
            type: 'success'
          })
        }
      })
    },
    saveInfos () {
      this.$axios.put('/apis/users/change/', this.users).then((resp) => {
        const data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '修改成功',
            type: 'success'
          })
        }
      })
      this.initData()
      this.editDialogVisible = false
    },
    allowReg (id, index, regUsers) {
      this.$axios.put('/apis/users/' + id, {
        role: 0
      }).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '处理完成',
            type: 'success'
          })
        }
      }).catch(function (error) {
        console.log(error)
      })
    },
    refuseReg (id) {
      this.$axios.put('/apis/users/' + id, {
        role: -1
      }).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '处理完成',
            type: 'success'
          })
        }
      }).catch(function (error) {
        console.log(error)
      })
    },
    deleteRow (index, rows) {
      rows.splice(index, 1)
    },
    allowCommo (id) {
      this.$axios.put('/apis/commodities/' + id + '/status', {
        status: 0
      }).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '处理完成',
            type: 'success'
          })
        }
      }).catch(function (error) {
        console.log(error)
      })
    },
    allowLicense (id) {
      this.$axios.put('/apis/users/License2/' + id).then(resp =>{
        var data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '申请通过',
            type: 'success'
          })
          this.initData()
        }
      })
    },
    refuseCommo (id) {
      this.$axios.put('/apis/commodities/' + id + '/status', {
        status: -2
      }).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '处理完成',
            type: 'success'
          })
        }
      }).catch(function (error) {
        console.log(error)
      })
    }
  },
  mounted () {
    this.initData()
  }
}
</script>
<style lang="scss" scoped>
#admin-home {
  .header {
    height: 60px;
    background: rgb(70, 70, 70);
  }
  .content {
    width: 900px;
    margin: 30px auto;
  }
}
</style>

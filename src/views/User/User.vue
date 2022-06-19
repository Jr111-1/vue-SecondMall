<template>
  <el-container id="user">
    <el-header class="header" style="height:30px">
      <div class="header-wrap">
        <a href="/home">
          <span>商城首页</span>
        </a>
      </div>
    </el-header>
    <el-container class="nav">
      <div class="nav-wrap">
        <a href="/home">
          <i class="iconfont icon-B"></i>
        </a>
      </div>
    </el-container>
    <el-container class="content">
      <div class="container">
        <el-col :span="4" class="ct-left">
          <div class="img-wrap">
            <el-upload
              class="avatar-uploader"
              :action= profilePhotoAction
              :show-file-list="false"
              :on-success="handleProfilePhotoSuccess"
              :before-upload="beforeProfilePhotoUpload">
              <img v-if="user.photoUrl" :src="user.photoUrl" class="avatar">
              <i v-else class="el-icon-plus avatar-uploader-icon"></i>
              <div class="img-bottom">上传头像</div>
            </el-upload>
          </div>
          <div class="menu-tab">
            <div v-for="item in menuTab" :key="item.id" :tabindex="item.id" :class="[ {'isActive':item.isActive}, 'menu-tab-item' ]" @click="toggleMenuItem(item)">{{ item.txt }}</div>
          </div>
        </el-col>
        <el-col :span="20" class="ct-right">
          <div class="right-container">
            <div class="user-info" v-show="menuTab[0].isActive">
              <div class="right-item">
                <div class="item-label">名称:</div>
                <div class="item-info">{{ user.name }}</div>
              </div>
              <div class="right-item">
                <div class="item-label">id:</div>
                <div class="item-info">{{ user.id }}</div>
              </div>
              <div class="right-item">
                <div class="item-label">密码:</div>
                <div class="item-info">{{ users.password }}</div>
              </div>
              <div class="right-item">
                <div class="item-label">简介:</div>
                <div class="item-info" v-if="user.biography != null  && user.biography != ''">{{ user.biography }}</div>
                <div class="item-info" v-else>暂无</div>
              </div>
              <div class="right-item">
                <div class="item-label">账户:</div>
                <div class="item-info">{{ user.email }}</div>
              </div>
              <div class="right-item">
                <div class="item-label">角色:</div>
                <div class="item-info" v-if="user.role == 0">普通用户</div>
                <div class="item-info" v-else>管理员</div>
              </div>

              <div class="right-item">
                <div class="item-label">姓名:</div>
                <div class="item-info">{{ user.xingming }}</div>
              </div>

              <div class="right-item">
                <div class="item-label">城市:</div>
                <div class="item-info">{{ user.city }}</div>
              </div>

              <div class="right-item">
                <div class="item-label">性别:</div>
                <div class="item-info">{{ user.sex }}</div>
              </div>

              <div class="right-item">
                <div class="item-label">手机号:</div>
                <div class="item-info">{{ user.tel }}</div>
              </div>

              <div class="right-item">
                <div class="item-label">银行卡号:</div>
                <div class="item-info">{{ user.bankid }}</div>
              </div>
              <div class="right-item">
                <div class="item-label">卖家等级:</div>
                <div class="item-info">{{ user.sellerLvl }}</div>
              </div>
              <el-button type="danger" @click="changeInfo">"编辑资料</el-button>
            </div>
           <el-dialog title="编辑资料" :visible.sync="dialogEditVisible">
             <el-form :model="user">
               <el-form-item label="名称" prop="name">
                 <el-input v-model="user.name" aplaceholder="请输入用户名"></el-input>
               </el-form-item>
               <el-form-item label="简介">
                 <el-input v-model="user.biography" aplaceholder="请输入简介"></el-input>
               </el-form-item>
               <el-form-item label="账户">
                 <el-input v-model="user.email" aplaceholder="请输入邮箱"></el-input>
               </el-form-item>
               <el-form-item label="密码">
                 <el-input v-model="user.password" aplaceholder="请输入密码"></el-input>
               </el-form-item>
               <el-form-item label="姓名">
                 <el-input v-model="user.xingming" aplaceholder="请输入姓名"></el-input>
               </el-form-item>
               <el-form-item label="城市">
                 <el-input v-model="user.city" aplaceholder="请输入城市"></el-input>
               </el-form-item>
               <el-form-item label="性别">
                 <el-input v-model="user.sex" aplaceholder="请输入性别"></el-input>
               </el-form-item>
               <el-form-item label="手机号">
                 <el-input v-model="user.tel" aplaceholder="请输入手机号"></el-input>
               </el-form-item>
               <el-form-item label="银行卡号">
                 <el-input v-model="user.bankid" aplaceholder="请输入银行卡号"></el-input>
               </el-form-item>
             </el-form>
             <div slot="footer" class="dialog-footer">
               <el-button @click="dialogEditVisible=false">取 消</el-button>
               <el-button type="primary"  @click="saveInfos()">提 交</el-button>
             </div>
           </el-dialog>
            <div class="history-order" v-show="menuTab[1].isActive">
              <div class="history-item" v-for="item in historyOrder" :key="item.id">
                <div class="item-top">
                  <p>订单号：{{item.id}}</p><p style="margin-left: 30px">下单时间：{{item.createdOn}}</p>
                </div>
                <div class="item-content">
                  <div class="img-wrap"><el-image :src="item.photoUrl" alt="" fit="cover"></el-image></div>
                  <div class="content-item item-name"><span>{{item.commodityName}}</span></div>
                  <div class="content-item item-num"><span>{{item.num}}</span></div>
                  <div class="content-item item-price"><span>￥{{totalPrice(item.num * item.price, 1)}}</span></div>
                  <div class="content-item item-status">
                    <span v-if="item.status === 0">等待发货</span>
                    <span v-else-if="item.status === 1">发货中</span>
                    <span v-else-if="item.status === 2">等待收货，运输中</span>
                    <span v-else-if="item.status === 3">已完成订单</span>
                    <span v-else-if="item.status === -1">已取消</span>
                    <span v-else-if="item.status === -2">申请取消中</span>
                    <span v-else-if="item.status === -3">商家拒绝取消，发货中</span>
                    <span v-else-if="item.status === 4">审核中</span>
                    <span v-else-if="item.status === 5">已经退货</span>
                    <span v-else-if="item.status === 6">拒绝退货</span>
                  </div>
                  <div class="content-item item-reason"><span v-if="item.status === -3">原因：{{item.reason}}</span></div>
                </div>
                <div class="item-bottom">
                  <div class="button-wrap">
                    <el-button type="primary" size="small" slot="reference" @click="confirm(item)" v-show="item.status === 2">确认收货</el-button>
                    <el-button type="danger" size="small" @click="cancelOrder(item)" v-show="item.status > -1 && item.status < 3">申请取消</el-button>
<!--                    <el-button type="primary" size="small" slot="reference" @click="judge(item)" v-show="item.status === 3">评价</el-button>-->
                    <el-button type="danger" size="small" slot="reference" @click="cancelOrder2(item)" v-show="item.status === 3">退货</el-button>
                  </div>
                </div>
              </div>
            </div>
<!--                        <el-dialog title="评论" :visible.sync="dialogJudgeVisible">-->

<!--                            <el-button type="primary" size="mini" @click="evaluate()">评价</el-button>-->
<!--                            <el-rate v-model="cmList.rating" :colors="['#99A9BF', '#F7BA2A', '#FF9900']"></el-rate>-->
<!--                            <el-input type="textarea" :rows="2" placeholder="请输入内容" v-model="cmList.comment"></el-input>-->
<!--                         -->
<!--                          <div slot="footer" class="dialog-footer">-->
<!--                            <el-button @click="dialogEditVisible=false">取 消</el-button>-->
<!--                            <el-button type="primary"  @click="saveInfos()">提 交</el-button>-->
<!--                          </div>-->
<!--                        </el-dialog>-->
            <div class="history-order" v-show="menuTab[2].isActive">
              <div class="history-item" v-for="item in myCommodity" :key="item.id">
                <div class="item-content">
                  <div class="img-wrap"><el-image :src="item.photoUrl" alt="" fit="cover"></el-image></div>
                  <div class="content-item item-name"><span>{{item.name}}</span></div>
                  <div class="content-item item-num"><span>{{item.quantity}}</span></div>
                  <div class="content-item item-price"><span>￥{{item.price}}</span></div>
                  <div class="content-item item-sellcontent"><span>{{item.sellcontent}}</span></div>
                </div>
                <div class="item-bottom" style="left:500px">
                  <el-button type="danger" @click="notSelling(item)">下架</el-button>
                  <el-button type="primary" @click="isSelling(item)" >上架</el-button>
                </div>
              </div>
            </div>
            <div class="publish" v-show="menuTab[3].isActive">
              <div class="right-item" style="height: 195px; line-height:195px">
                <div class="item-label">图片</div>
                <div class="item-info"><el-upload
                  class="avatar-uploader"
                  action="https://jsonplaceholder.typicode.com/posts/"
                  :show-file-list="false"
                  :on-success="handleAvatarSuccess"
                  :before-upload="beforeAvatarUpload">
                  <img v-if="imageUrl" :src="imageUrl" class="avatar">
                  <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                </el-upload>
                </div>
              </div>
              <div class="right-item">
                <div class="item-label">名称</div>
                <div class="item-info" style="width: 500px">
                  <el-input v-model="commodity.name" placeholder="请输入商品名称" maxlength="50" show-word-limit></el-input>
                </div>
              </div>
              <div class="right-item">
                <div class="item-label">描述</div>
                <div class="item-info" style="width: 500px">
                  <el-input
                    type="text"
                    placeholder="请输入商品描述"
                    v-model="commodity.description"
                    maxlength="100"
                    show-word-limit
                  >
                  </el-input>
                </div>
              </div>
              <div class="right-item">
                <div class="item-label">数量</div>
                <div class="item-info">
                  <el-input-number v-model="commodity.quantity" :min="1" :max="99" size="small"></el-input-number>
                </div>
              </div>
              <div class="right-item">
                <div class="item-label">单价</div>
                <div class="item-info" style="width:300px">
                  <el-input v-model="commodity.price" placeholder="请输入单件商品价格，单位：元"></el-input>
                </div>
              </div>
              <div class="right-item">
                <div class="item-label"></div>
                <div class="item-info">
                  <el-button type="danger" @click="publish()">发布</el-button>
                </div>
              </div>
            </div>
            <div class="pending-order" v-show="menuTab[4].isActive">
              <el-table :data="pendingOrder" stripe style="width: 100%">
                <el-table-column prop="id" label="订单号" width="70"></el-table-column>
                <el-table-column prop="createdOn" label="下单时间" width="160"></el-table-column>
                <el-table-column prop="commodityName" label="商品名称"></el-table-column>
                <el-table-column prop="num" label="商品数量"></el-table-column>
                <el-table-column prop="note" label="地址信息"></el-table-column>
                <el-table-column label="状态">
                  <div slot-scope="scope">
                    <div v-if="scope.row.status == 0"><el-tag>待处理</el-tag></div>
                    <div v-else-if="scope.row.status == 1"><el-tag>发货中</el-tag></div>
                    <div v-else-if="scope.row.status == 2"><el-tag>发货完成</el-tag></div>
                    <div v-else-if="scope.row.status == 3"><el-tag type="success">已完成</el-tag></div>
                    <div v-else-if="scope.row.status == -1"><el-tag type="danger">已取消</el-tag></div>
                    <div v-else-if="scope.row.status == -2"><el-tag>申请取消</el-tag></div>
                    <div v-else-if="scope.row.status == -3"><el-tag type="danger">拒绝取消！</el-tag></div>
                    <div v-else-if="scope.row.status == 4"><el-tag>申请退货</el-tag></div>
                    <div v-else-if="scope.row.status == 5"><el-tag>已退货</el-tag></div>
                    <div v-else-if="scope.row.status == 6"><el-tag>拒绝退货</el-tag></div>
                  </div>
                </el-table-column>
                <el-table-column label="处理" width="180">
                  <div slot-scope="scope">
                    <div v-if="scope.row.status == 0">
                      <el-button type="primary" size="small" @click="changeStatus(scope.row, true)">发货</el-button>
                      <el-button type="danger" size="small" @click="changeStatus(scope.row, false)">取消</el-button>
                    </div>
                    <div v-else-if="scope.row.status == 1">
                      <el-button type="primary" size="small" @click="changeStatus(scope.row, true)">已完成</el-button>
                    </div>
                    <div v-if="scope.row.status == -2">
                      <el-button type="primary" size="small" @click="changeStatus(scope.row, true)">同意</el-button>
                      <el-button type="danger" size="small" @click="changeStatus(scope.row, false)">拒绝</el-button>
                    </div>
                    <div v-else-if="scope.row.status == 2">
                      <el-tag>等待确认</el-tag>
                    </div>
                    <div v-else-if="scope.row.status == 3">
                      <el-button type="success" @click="checkMoney(scope.row)">查看本单收入</el-button>
                    </div>
                    <div v-if="scope.row.status == 4">
                      <el-button type="primary" size="small" @click="changeStatus(scope.row, true)">同意</el-button>
                      <el-button type="danger" size="small" @click="changeStatus(scope.row, false)">拒绝</el-button>
                    </div>
                    <div v-else-if="scope.row.status == -1">
                      <el-tag type="danger">已取消！</el-tag>
                    </div>
                    <div v-else-if="scope.row.status == -3">
                      <el-button type="primary" size="small" @click="changeStatus(scope.row, true)">发货</el-button>
                    </div>
                  </div>
                </el-table-column>
              </el-table>
            </div>
            <el-dialog title="收入明细" :visible.sync="dialogMoneyVisible">
              <div class="right-item">
                <div class="item-label">价格</div>
                <div class="item-info">{{this.orders.num * this.commoditys.price}}</div>
              </div>
              <div class="right-item">
                <div class="item-label">平台服务费</div>
                <div class="item-info">{{this.user.sellerLvl}}</div>
              </div>
              <div class="right-item">
                <div class="item-label">总收入</div>
                <div class="item-info">{{this.orders.num * this.commoditys.price - this.user.sellerLvl}}</div>
              </div>
              <div slot="footer" class="dialog-footer">
                <el-button @click="dialogMoneyVisible=false">取 消</el-button>
                <el-button type="primary"  @click="dialogMoneyVisible=false">确 定</el-button>
              </div>
            </el-dialog>
            <el-dialog title="拒绝原因" :visible.sync="reasonEditVisible">
              <el-form :model="orders">
                <el-form-item label="原因">
                  <el-input v-model="orders.reason"></el-input>
                </el-form-item>
              </el-form>
              <div slot="footer" class="dialog-footer">
                <el-button @click="reasonEditVisible=false">取 消</el-button>
                <el-button type="primary"  @click="saveReason()">提 交</el-button>
              </div>
            </el-dialog>
            <div class="history-order" v-show="menuTab[5].isActive">
              <div class="history-item" v-for="item in shopCar" :key="item.id">
                <div class="item-content">
                  <div class="img-wrap"><el-image :src="item.photoUrl" alt="" fit="cover"></el-image></div>
                  <div class="content-item item-name"><span>{{item.commodityName}}</span></div>
                  <div class="content-item item-num"><span>{{item.num}}</span></div>
                  <div class="content-item item-price"><span>￥{{totalPrice(item.num * item.price, 1)}}</span></div>
                  <div class="content-item item-status">{{item.note}}</div>
                </div>
                <div class="item-bottom" style="left:500px">
                      <el-button type="primary" @click="buyNow(item)">立即购买</el-button>
                      <el-button type="danger" @click="cancle(item)" >移出购物车</el-button>
                </div>
              </div>
            </div>
            <div class="user-info" v-show="menuTab[6].isActive">
              <div class="right-item">
                <div class="item-label">余额</div>
                <div class="item-info">{{ users.money }}</div>
              </div>
              <div class="right-item">
                <div class="item-label">积分</div>
                <div class="item-info">{{ users.score }}</div>
              </div>
              <el-button type="danger" @click="saveMoney1()">充值</el-button>
              <el-button @click="reloadMoney()" icon="reload">刷新</el-button>
              <div id="table">
                <el-table id="saveTable" :data="saveContents2">
                  <el-table-column prop="id" label="id"></el-table-column>
                  <el-table-column prop="content" label="充值记录"></el-table-column>
                </el-table>
              </div>
              <div id="tablePay">
                <el-table id="saveTable2" :data="payContents2">
                  <el-table-column prop="id" label="id"></el-table-column>
                  <el-table-column prop="content" label="消费记录"></el-table-column>
                </el-table>
              </div>
            </div>
            <div class="user-info" v-show="menuTab[7].isActive">
              <div v-if="this.users.isLicense === -1">
               <el-button type="danger" @click="isLicense()">申请</el-button>
              </div>
              <div v-if="this.users.isLicense === 1">
                <el-tag>已拥有营业执照</el-tag>
              </div>
              <div v-if="this.users.isLicense === 0">
                <el-tag>申请已提交</el-tag>
              </div>
              <div class="img-wrap">
                <el-upload
                  class="avatar-uploader"
                  :action= profilePhotoAction2
                  :show-file-list="false"
                  :on-success="handleProfilePhotoSuccess2"
                  :before-upload="beforeProfilePhotoUpload2">
                  <img v-if="user.license_photo" :src="user.license_photo" class="avatar">
                  <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                  <div class="img-bottom">上传营业执照</div>
                </el-upload>
              </div>
              <div class="img-wrap">
                <el-upload
                  class="avatar-uploader"
                  :action= profilePhotoAction3
                  :show-file-list="false"
                  :on-success="handleProfilePhotoSuccess3"
                  :before-upload="beforeProfilePhotoUpload3">
                  <img v-if="user.idPhoto" :src="user.idPhoto" class="avatar">
                  <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                  <div class="img-bottom">上传身份证照片</div>
                </el-upload>
              </div>
            </div>
            <el-dialog title="充值" :visible.sync="dialogSaveVisible">
              <el-form :model="user">
                <el-form-item label="充值金额" prop="money">
                  <el-input v-model="users.money" aplaceholder="请输入充值金额"></el-input>
                </el-form-item>
              </el-form>
              <div slot="footer" class="dialog-footer">
                <el-button @click="dialogSaveVisible=false">取 消</el-button>
                <el-button type="primary"  @click="saveMoney()">提 交</el-button>
              </div>
            </el-dialog>
          </div>
        </el-col>
      </div>
    </el-container>
  </el-container>
</template>
<script>
export default {
  data () {
    return {
      user: { id: 0, name: '', biography: '', email: '', role: '', photoUrl: '', xingming: '', sex: '', city: '', tel: '', bankid: '', money: '', saveContent: '', payContent: '', sellerLvl: '', score: '', isLicense: '', license_photo: '', idPhoto: '', password: '' },
      menuTab: [
        { id: 1, txt: '我的资料', isActive: true },
        { id: 2, txt: '购买历史', isActive: false },
        { id: 3, txt: '我的商品', isActive: false },
        { id: 4, txt: '发布商品', isActive: false },
        { id: 5, txt: '订单管理', isActive: false },
        { id: 6, txt: '购物车', isActive: false },
        { id: 7, txt: '钱包', isActive: false },
        { id: 8, txt: '申请', isActive: false }
      ],
      order: {
        // num: 1,
        // note: '',
        // commodityId: 0,
        // buyerId: 0
      },
      historyOrder: [],
      shopCar: [],
      myCommodity: [],
      Commoditys: { },
      pendingOrder: [],
      commodity: {
        name: '', description: '', quantity: 1, price: '', ownerId: this.$store.state.user.id, sellcontent: ''
      },
      imageUrl: '',
      imageFile: '',
      orders: {},
      users: {},
      owner: {},
      commoditys: {},
      money: 0,
      profilePhotoAction: '/apis/users/' + this.$store.state.user.id + '/images',
      profilePhotoAction2: '/apis/users/' + this.$store.state.user.id + '/Licenseimages',
      profilePhotoAction3: '/apis/users/' + this.$store.state.user.id + '/idimages',
      dialogEditVisible: false,
      dialogSaveVisible: false,
      reasonEditVisible: false,
      dialogMoneyVisible: false,
      dialogJudgeVisible: false,
      saveContents: [],
      saveContents2: [
      ],
      payContents: [],
      payContents2: [
      ],
      socket: null
    }
  },
  mounted () {
    this.initData()
  },
  methods: {
    initData () {
      this.user = this.$store.state.user
      this.$axios.get('/apis/users/getInfos/' + this.user.id).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.users = data.data
          this.saveContents = this.users.saveContent.split(',')
          for (let i = 1; i < this.saveContents.length; i++) {
            var list = { id: i, content: this.saveContents[i] }
            this.saveContents2.push(list)
          }
          this.payContents = this.users.payContent.split(',')
          for (let i = 1; i < this.payContents.length; i++) {
            var list1 = { id: i, content: this.payContents[i] }
            this.payContents2.push(list1)
          }

        }
      }).catch(function (error) {
        console.log(error)
      })
      // 获取购买历史
      this.$axios.get('/apis/buyer-orders/' + this.user.id).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.historyOrder = data.data
        }
      }).catch(function (error) {
        console.log(error)
      })
      // 获取购物车
      this.$axios.get('/apis/shopcar-orders/' + this.user.id).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.shopCar = data.data
        }
      }).catch(function (error) {
        console.log(error)
      })
      // 获取用户在售卖的商品
      this.$axios.get('/apis/users/' + this.user.id + '/commodities').then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.myCommodity = data.data
        }
      }).catch(function (error) {
        console.log(error)
      })

      // 获取用户已卖出的商品
      this.$axios.get('/apis/pending-orders/' + this.user.id).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.pendingOrder = data.data
        }
      }).catch(function (error) {
        console.log(error)
      })

    },
    isLicense () {
      this.$axios.put('/apis/users/License/' + this.user.id).then(resp => {
        var data = resp.data
        if (data === 200) {
          this.$notify({
            title: '成功',
            message: '申请已提交！',
            type: 'success'
          })
          this.users.isLicense = 0
        }
      })
    },

    changeStatus (item, isOk) {
      if (isOk) {
        if (item.status === -2) {
          this.$axios.put('/apis/pending-orders/' + item.id + '?status=' + (item.status + 1)).then(resp => {
            var data = resp.data
            if (data.code === 200) {
              item.status += 1
              this.$axios.put('/apis/pending-orders/return/' + item.id).then(resp2 => {
                var data2 = resp2.data
                if (data2.code === 200) {
                  this.$notify({
                    title: '退款成功',
                    message: '货款已返回用户账户！',
                    type: 'success'
                  })
                }
              })
            }
          }).catch(function (error) {
            console.log(error)
          })
        } else if (item.status === -3) {
          this.$axios.put('/apis/pending-orders/' + item.id + '?status=' + (item.status + 1)).then(resp => {
            var data = resp.data
            if (data.code === 200) {
              item.status = 1
              this.$notify({
                title: '成功',
                message: '发货成功！请及时发货！',
                type: 'success'
              })
            }
          }).catch(function (error) {
            console.log(error)
          })
        } else {
          this.$axios.put('/apis/pending-orders/' + item.id + '?status=' + (item.status + 1)).then(resp => {
            var data = resp.data
            if (data.code === 200) {
              item.status += 1
              if (item.status === 5) {
                this.$axios.put('/apis/pending-orders/return/' + item.id).then(resp2 => {
                  var data2 = resp2.data
                  if (data2.code === 200) {
                    this.$notify({
                      title: '退货成功',
                      message: '货款已返回用户账户！',
                      type: 'success'
                    })
                  }
                })
              } else {
                this.$notify({
                  title: '成功',
                  message: '发货成功！请及时发货！',
                  type: 'success'
                })
              }
            }
          }).catch(function (error) {
            console.log(error)
          })
        }
      } else {
        if (item.status === -2) {
          this.$axios.put('/apis/pending-orders/' + item.id + '?status=' + -3).then(resp => {
            var data = resp.data
            if (data.code === 200) {
              item.status = -3
              this.$axios.get('/apis/pending-orders/getReason/' + item.id).then(resp => {
                var data = resp.data
                if (data.code === 200) {
                  this.$notify({
                    title: '成功',
                    message: '成功读取数据',
                    type: 'success'
                  })
                  this.orders = data.data
                }
              })
              this.reasonEditVisible = true
            }
          }).catch(function (error) {
            console.log(error)
          })
        } else if (item.status === 0) {
          this.$axios.put('/apis/pending-orders/' + item.id + '?status=' + -1).then(resp => {
            var data = resp.data
            if (data.code === 200) {
              item.status = -1
              this.$axios.put('/apis/pending-orders/return/' + item.id).then(resp2 => {
                var data2 = resp2.data
                if (data2.code === 200) {
                  this.$notify({
                    title: '退款成功',
                    message: '货款已返回用户账户！',
                    type: 'success'
                  })
                }
              })
            }
          }).catch(function (error) {
            console.log(error)
          })
        } else {
          this.$axios.put('/apis/pending-orders/' + item.id + '?status=' + (item.status + 2)).then(resp => {
            var data = resp.data
            if (data.code === 200) {
              item.status = 6
              this.$notify({
                title: '拒绝退货',
                message: '拒绝退货',
                type: 'success'
              })
            }
          })
        }
      }
    },
    // 切换左侧菜单
    toggleMenuItem (data) {
      this.menuTab.forEach(item => {
        item.isActive = false
      })
      data.isActive = true
    },
    // element-ui 上传图片的方法 头像的！！！！
    handleProfilePhotoSuccess (res, file) {
      this.user.photoUrl = res.data
    },
    handleProfilePhotoSuccess2 (res, file) {
      this.user.license_photo = res.data
    },
    handleProfilePhotoSuccess3 (res, file) {
      this.user.idPhoto = res.data
    },
    // 上传前 头像的！！！！
    beforeProfilePhotoUpload (file) {
      const isJPG = file.type === 'image/jpeg' || file.type === 'image/jpg' || file.type === 'image/png'
      const isLt10M = file.size / 1024 / 1024 < 10

      if (!isJPG) {
        this.$message.error('上传的头像图片只能是 JPG/PNG/JPEG 格式!')
        return false
      }
      if (!isLt10M) {
        this.$message.error('上传的头像图片大小不能超过 10MB!')
        return false
      }
      // console.log(file)
      // 创建临时的路径来展示图片
      var windowURL = window.URL || window.webkitURL
      this.user.photoUrl = windowURL.createObjectURL(file)
      return true
    },
    beforeProfilePhotoUpload2 (file) {
      const isJPG = file.type === 'image/jpeg' || file.type === 'image/jpg' || file.type === 'image/png'
      const isLt10M = file.size / 1024 / 1024 < 10

      if (!isJPG) {
        this.$message.error('上传的头像图片只能是 JPG/PNG/JPEG 格式!')
        return false
      }
      if (!isLt10M) {
        this.$message.error('上传的头像图片大小不能超过 10MB!')
        return false
      }
      // console.log(file)
      // 创建临时的路径来展示图片
      var windowURL = window.URL || window.webkitURL
      this.user.license_photo = windowURL.createObjectURL(file)
      return true
    },
    beforeProfilePhotoUpload3 (file) {
      const isJPG = file.type === 'image/jpeg' || file.type === 'image/jpg' || file.type === 'image/png'
      const isLt10M = file.size / 1024 / 1024 < 10

      if (!isJPG) {
        this.$message.error('上传的头像图片只能是 JPG/PNG/JPEG 格式!')
        return false
      }
      if (!isLt10M) {
        this.$message.error('上传的头像图片大小不能超过 10MB!')
        return false
      }
      // console.log(file)
      // 创建临时的路径来展示图片
      var windowURL = window.URL || window.webkitURL
      this.user.idPhoto = windowURL.createObjectURL(file)
      return true
    },

    // element-ui 上传图片的方法 商品的！！！！！
    handleAvatarSuccess (res, file) {
      this.imageUrl = URL.createObjectURL(file.raw)
      this.imageFile = file
    },
    // 上传前 商品的！！！！！
    beforeAvatarUpload (file) {
      const isJPG = file.type === 'image/jpeg' || file.type === 'image/jpg' || file.type === 'image/png'
      const isLt2M = file.size / 1024 / 1024 < 10

      if (!isJPG) {
        this.$message.error('上传商品图片只能是 JPG/PNG/JPEG 格式!')
        return false
      }
      if (!isLt2M) {
        this.$message.error('上传商品图片大小不能超过 10MB!')
        return false
      }
      // console.log(file)
      // 创建临时的路径来展示图片
      var windowURL = window.URL || window.webkitURL
      this.imageUrl = windowURL.createObjectURL(file)
      // 重新写一个表单上传的方法
      this.imageFile = new FormData()
      this.imageFile.append('file', file, file.name)
      return false
    },
    publish () {
      if (this.users.isLicense === 1) {
        this.$axios.post('/apis/commodities', this.commodity).then(resp => {
          var data = resp.data
          if (data.code === 200) {
            var commodity = data.data
            const config = {
              headers: { 'Content-Type': 'multipart/form-data' }
            }
            this.$axios.post('/apis/commodities/' + commodity.id + '/images', this.imageFile, config).then(resp => {
              var data = resp.data
              if (data.code === 200) {
                this.$notify({
                  title: '成功',
                  message: '创建商品成功，请等待审核',
                  type: 'success'
                })
                commodity.url = data.data
                this.myCommodity.push(commodity)
                this.commodity = []
                this.menuTab[3].isActive = false
                this.menuTab[2].isActive = true
              } else {
                this.$notify({
                  title: '警告',
                  message: '创建商品成功，但是图片上传失败，请等待审核',
                  type: 'waring'
                })
              }
            }).catch(function (error) {
              this.$notify.error({
                title: '错误',
                message: '发布失败'
              })
              console.log(error)
            })
          }
        }).catch(function (error) {
          this.$notify.error({
            title: '错误',
            message: '发布失败'
          })
          console.log(error)
        })
      } else {
        this.$notify.error({
          title: '错误',
          message: '发布失败'
        })
      }
    },
    confirm (item) {
      this.$axios.put('/apis/pending-orders/' + item.id + '?status=3').then(resp => {
        if (resp.status === 200) {
          var res = resp.data
          if (res.code === 200) {
            console.log(res)
            item.status = res.data
            this.$axios.put('/apis/pending-orders/get/' + item.id).then(resp2 => {
              var data2 = resp2.data
              if (data2.code === 200) {
                this.$notify({
                  title: '收获成功',
                  message: '货款已支付到商家账户！',
                  type: 'success'
                })
              }
            })
          }
        }
      }).catch(function (error) {
        console.log(error)
      })
    },
    cancelOrder (item) {
      this.$axios.put('/apis/pending-orders/' + item.id + '?status=-2').then(resp => {
        if (resp.status === 200) {
          var res = resp.data
          if (res.code === 200) {
            item.status = res.data
            this.$notify({
              title: '成功',
              message: '已申请取消！请等待',
              type: 'success'
            })
          }
        }
      }).catch(function (error) {
        console.log(error)
      })
    },
    cancelOrder2 (item) {
      this.$axios.put('/apis/pending-orders/' + item.id + '?status=4').then(resp => {
        if (resp.status === 200) {
          var res = resp.data
          if (res.code === 200) {
            item.status = res.data
            this.$notify({
              title: '成功',
              message: '已申请取消！请等待',
              type: 'success'
            })
          }
        }
      }).catch(function (error) {
        console.log(error)
      })
    },
    // judge (item) {
    //   this.$axios.get('/apis/pending-orders/getReason/' + item.id).then(resp => {
    //     var data = resp.data
    //     if (data.code === 200) {
    //       this.orders = data.data
    //       this.$axios.get('/apis/commodities/get/' + this.orders.commodityId).then(resp2 => {
    //         var data2 = resp2.data
    //         if (data2.code === 200) {
    //           this.$notify({
    //             title: '成功',
    //             message: '成功',
    //             type: 'success'
    //           })
    //           this.commoditys = data2.data
    //         }
    //       })
    //     }
    //   })
    //   this.$store.commit('GET_COMMODITY', this.commoditys)
    //   this.$router.push({ name: 'Commodity' }) // 跳转至商品页面
    // },
    changeInfo () {
      this.dialogEditVisible = true
    },
    saveMoney1 () {
      this.dialogSaveVisible = true
    },
    saveInfos () {
      this.$axios.put('/apis/users/change/', this.user).then((resp) => {
        const data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '修改成功',
            type: 'success'
          })
        }
      })
      this.dialogEditVisible = false
    },
    saveReason () {
      this.$axios.put('/apis/pending-orders/returnReason/', this.orders).then((resp) => {
        const data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '修改成功',
            type: 'success'
          })
        }
      })
      this.reasonEditVisible = false
    },
    reloadMoney () {
      this.$axios.get('/apis/users/getInfos/' + this.user.id).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.users = data.data
        }
      }).catch(function (error) {
        console.log(error)
      })
    },
    checkMoney (item) {
      this.$axios.get('/apis/pending-orders/getReason/' + item.id).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.orders = data.data
          this.$axios.get('/apis/commodities/get/' + this.orders.commodityId).then(resp2 => {
            var data2 = resp2.data
            if (data2.code === 200) {
              this.$notify({
                title: '成功',
                message: '成功',
                type: 'success'
              })
              this.commoditys = data2.data
            }
          })
        }
        this.dialogMoneyVisible = true
      })
    },
    saveMoney () {
      this.$axios.put('/apis/users/save/', this.users).then((resp) => {
        const data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '充值成功',
            type: 'success'
          })
          this.users = data.data
          this.saveContents = this.users.saveContent.split(',')
          // // for (let i = this.saveContents.length - 1; i < this.saveContents.length; i++) {
          var list = { id: this.saveContents.length + 1, content: this.saveContents.pop() }
          this.saveContents2.push(list)
        }
      })
      this.dialogSaveVisible = false
    },
    notSelling (item) {
      this.$axios.put('/apis/commodities/sell/' + item.id).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '下架成功',
            type: 'success'
          })
          this.initData()
        }
      })
    },
    isSelling (item) {
      this.$axios.put('/apis/commodities/sell/' + item.id).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '上架成功',
            type: 'success'
          })
          this.initData()
        }
      })
    },
    buyNow (item) {
      this.$axios.post('/apis/orders', item).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '购买成功！请及时与商家联系',
            type: 'success'
          })
          this.data.quantity = this.data.quantity - this.order.num
          this.initData()
        } else if (data.code === 401) {
          this.$notify({
            title: '失败',
            message: '余额不足，请及时充值！',
            type: 'danger'
          })
        }
      })
    },
    cancle (item) {
      this.$axios.delete('/apis/orders/' + item.id).then(resp => {
        var data = resp.data
        if (data.code === 200) {
          this.$notify({
            title: '成功',
            message: '移除成功！商品已经移出购物车',
            type: 'success'
          })
        }
      })
      this.initData()
    }
  },
  computed: {
    totalPrice: function () {
      return function (f, digit) {
        var m = Math.pow(10, digit) // 设置需要乘以的倍数
        return parseInt(f * m, 10) / m // 先乘再除，解决精度问题
      }
    }
  }
}
</script>
<style lang="scss" >
#user {
  .header {
    background: #e3e4e5;
    line-height: 30px;
    .header-wrap {
      width: 1210px;
      margin: 0 auto;
      display: flex;
      align-items: center;
      font-size: 12px;

      a {
        color: black;
        display: flex;
        align-items: center;
        cursor: pointer;
      }

      a:hover {
        color: #ff0036;
      }

    }
  }

  .nav{
    background: #e2231a;
    height: 80px;
    .nav-wrap {
      width: 1210px;
      margin: 0 auto;
      display: flex;
      align-items: center;
      a {
      color: #fff;
      cursor: pointer;
      .icon-B {
        font-size: 50px;
      }
    }
    }
  }

  .content {
    background: #f5f5f5;

    .history-order {
      .history-item {
        margin-bottom: 20px;
        margin-right: 90px;
        border-bottom: #e4e3e3 solid 1px;
        .item-top {
          display: flex;
          p {
            margin: 0;
            font-size: 14px;
            color: #3c3c3c;
          }
        }
        .item-content {
          display: flex;
          margin-top: 10px;
          .img-wrap {
            img {
              height: 100px;
              width: 100px;
            }
          }
          .content-item {
            height: 100px;
            font-size: 14px;
            line-height: 100px;
            margin-left: 20px;
            color: #3c3c3c;
            span {
              display: inline-block;
              vertical-align: middle;
              line-height: 22px;
            }
          }
          .item-name {
            width: 200px;
          }
          .item-reason {
            width: 200px;
          }
          .item-num {
            width: 50px;
            text-align: center;
          }
          .item-price {
            width: 50px;
            text-align: center;
            color: #3c3c3c;
            font-weight: bolder;
          }
          .item-sellcontent {
            width: 400px;
          }

        }
        .item-bottom {
          position: relative;
          height:45px;
          .button-wrap {
            position: absolute;
            line-height: 40px;
            right: 20px;
          }
        }
      }
    }
    .carshop {
      .history-item {
        margin-bottom: 20px;
        margin-right: 90px;
        border-bottom: #e4e3e3 solid 1px;
        .item-top {
          display: flex;
          p {
            margin: 0;
            font-size: 14px;
            color: #3c3c3c;
          }
        }
        .item-content {
          display: flex;
          margin-top: 10px;
          .img-wrap {
            img {
              height: 100px;
              width: 100px;
            }
          }
          .content-item {
            height: 100px;
            font-size: 14px;
            line-height: 100px;
            margin-left: 20px;
            color: #3c3c3c;
            span {
              display: inline-block;
              vertical-align: middle;
              line-height: 22px;
            }
          }
          .item-name {
            width: 400px;
          }
          .item-num {
            width: 50px;
            text-align: center;
          }
          .item-price {
            width: 50px;
            text-align: center;
            color: #3c3c3c;
            font-weight: bolder;
          }
        }
        .item-bottom {
          position: relative;
          height:45px;
          .button-wrap {
            position: absolute;
            line-height: 40px;
            right: 20px;
          }
        }
      }
    }

    .container {
      display: flex;
      width: 1210px;
      margin: 80px auto;
      min-height: 1000px;
      background: #fff;
      border-radius: 4px;
      .ct-left{
        height: 92%;
        margin: 40px 70px;
        border-right: 1px solid #dddddd;
        .img-wrap{
          width: 120px;
          height: 120px;
          padding:5px;
          border: 1px solid #EAEAEA;
          img {
            width: 100%;
            height: 100%;
          }

          .avatar-uploader {
            height: 100%;
          }
          .el-upload {
            width: 100%;
            height: 100%;
            line-height: 120px;
            position: relative;

            .img-bottom {
              position: absolute;
              top:0;
              left: 0;
              right: 0;
              bottom: 0;
              text-align: center;
              line-height: 130px;
              background-color: rgba($color: #000000, $alpha: .2);
              font-size: 14px;
              color: #fff;
              display: none;
            }
          }

          .el-upload:hover .img-bottom {
            display: block;
          }
        }

        .menu-tab-item {
          height: 70px;
          width: 125px;
          text-align: right;
          line-height: 70px;
          font-size: 15px;
          cursor: pointer;
        }
        .isActive {
          color: #409efe;
        }
        .menu-tab-item:hover {
          color: #409efe;
        }
        .menu-tab-item:focus {
          color: #409efe;
          outline: none;
        }
      }
      .ct-right {
        height: 92%;
        margin-top: 40px;
        .right-item {
          display: flex;
          font-size: 15px;
          height: 50px;
          .item-label {
            width: 100px;
          }
        }
        .publish {
          .right-item {
            line-height: 50px;
            margin-bottom: 20px;
          }
        }
        .avatar-uploader .el-upload {
          border: 1px dashed #d9d9d9;
          border-radius: 6px;
          cursor: pointer;
          position: relative;
          overflow: hidden;
        }
        .avatar-uploader .el-upload:hover {
          border-color: #409EFF;
        }
        .avatar-uploader-icon {
          font-size: 28px;
          color: #8c939d;
          width: 178px;
          height: 178px;
          line-height: 178px;
          text-align: center;
        }
        .avatar {
          width: 178px;
          height: 178px;
          display: block;
        }
      }
    }
  }
}
</style>

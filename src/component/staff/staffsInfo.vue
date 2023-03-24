<template>
    <div>
        <!-- 卡片区域 -->
        <el-card class="box-card" shadow="hover">
            <!-- 搜索栏 -->
            <el-row :gutter="15" style="margin-bottom: 10px">
                <el-col :span="10">
                    <el-input v-model="staffName" placeholder="请输入员工名搜索员工" prefix-icon="el-icon-search">
                    </el-input>
                </el-col>
                <el-col :span="6">
                    <el-button type="success" icon="el-icon-search"
                               style="padding: 10px 20px; margin-top: 2px" @click="searchStaff">
                        搜索
                    </el-button>
					 <el-button type="success"
                               style="padding: 10px 20px; margin-top: 2px" @click="reset">
                        重置
                    </el-button>
                </el-col>
            </el-row>
          <el-row style="margin-top: 1vh;margin-left: 1vh;margin-bottom: 2vh">
            <el-button type="success" size="mini" icon="el-icon-circle-plus-outline" @click="dialogAddForm = true">添加</el-button>
          </el-row>
          <!-- 添加弹窗 开始-->
          <el-dialog title="添加员工" :visible.sync="dialogAddForm" width="80vh">
            <el-form label-width="80px" :rules="rules" ref="addStaffFrom" :model="addStaffFrom" size="mini">
              <el-form-item label="姓名" prop="sfName" >
                <el-input v-model="addStaffFrom.sfName" ></el-input>
              </el-form-item>
              <el-form-item label="证件号" prop="identityId">
                <el-input v-model="addStaffFrom.identityId" ></el-input>
              </el-form-item>
              <el-form-item label="性别" prop="sex">
                <el-radio-group v-model="addStaffFrom.sex" placeholder="请选择性别">
                  <el-radio label="男" value="0"></el-radio>
                  <el-radio label="女" value="1"></el-radio>
                </el-radio-group>
              </el-form-item>
              <el-form-item label="电话" prop="sfTel">
                <el-input v-model="addStaffFrom.sfTel"></el-input>
              </el-form-item>
              <el-form-item label="联系地址" prop="address">
                <el-input v-model="addStaffFrom.address"></el-input>
              </el-form-item>
              <el-form-item label="邮箱" prop="address">
                <el-input v-model="addStaffFrom.email"></el-input>
              </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
              <el-button @click="cancel">取 消</el-button>
              <el-button type="primary" @click="addStaff">提 交</el-button>
            </div>
          </el-dialog>
          <!-- 添加弹窗 结束-->

          <!-- 修改弹窗 开始-->
          <el-dialog title="添加员工" :visible.sync="dialogEditForm" width="80vh">
            <el-form label-width="80px" :rules="rules" ref="addStaffFrom" :model="editStaffFrom" size="mini">
              <el-form-item label="姓名" prop="sfName" >
                <el-input v-model="editStaffFrom.sfName" disabled></el-input>
              </el-form-item>
              <el-form-item label="证件号" prop="identityId">
                <el-input v-model="editStaffFrom.identityId" disabled></el-input>
              </el-form-item>
              <el-form-item label="性别" prop="sex">
                <el-radio-group v-model="editStaffFrom.sex" placeholder="请选择性别" disabled>
                  <el-radio label="男" value="0"></el-radio>
                  <el-radio label="女" value="1"></el-radio>
                </el-radio-group>
              </el-form-item>
              <el-form-item label="电话" prop="sfTel">
                <el-input v-model="editStaffFrom.sfTel"></el-input>
              </el-form-item>
              <el-form-item label="联系地址" prop="address">
                <el-input v-model="editStaffFrom.address"></el-input>
              </el-form-item>
              <el-form-item label="邮箱" prop="address">
                <el-input v-model="editStaffFrom.email"></el-input>
              </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
              <el-button @click="cancel">取 消</el-button>
              <el-button type="primary" @click="editStaff">提 交</el-button>
            </div>
          </el-dialog>
          <!-- 修改弹窗 结束-->

            <!-- 信息展示表格 -->
            <el-table :data="staffData" border stripe size="mini"
                      highlight-current-row >
                <!-- 员工对象的属性列 -->
                <el-table-column prop="sfName" fixed align="left"
                                 label="姓名" width="100">
                </el-table-column>
                <el-table-column prop="sex" label="性别"
                                 align="left" width="85">
                </el-table-column>
                <el-table-column prop="identityId" label="身份证"
                                 align="left" width="200">
                </el-table-column>
                <el-table-column prop="sfTel" label="电话号码"
                                 align="left" width="140">
                </el-table-column>
                <el-table-column prop="email" label="电子邮箱"
                                 align="left" width="200">
                </el-table-column>
                <el-table-column prop="address" label="联系地址"
                                 align="left" width="260">
                </el-table-column>
                <el-table-column prop="sfStatus" label="任职状态"
                                 align="left" width="85">
                </el-table-column>
               &lt;!&ndash; 功能列 &ndash;&gt;
                <el-table-column fixed="right" width="100"
                                 label="操作">
                    <template slot-scope="scope">
                        <el-button size="mini" type="success"
                                   @click="handleEdit(scope.$index, scope.row)">编辑
                        </el-button>
                    </template>
                </el-table-column>
            </el-table>
            <!-- 分页组件 -->
            <div class="pagination">
                <!--<el-button type="success" size="small">批量删除</el-button>-->
                <el-pagination style="text-align: center" background
                               layout="total, prev, pager, next, jumper"
                               @current-change="getPage"
                               :page-size="size"
                               :total="total">
                </el-pagination>
            </div>
        </el-card>
    </div>
</template>

<script>
    export default {
        name: "staffsInfo",
        data(){
            return {
              staffData: [],
              total: null,
              staffName: null,
              page: 1,
              size: 5,
              dialogAddForm: false,
              dialogEditForm : false,
              addStaffFrom: {
                sfName: '',
                identityId: '',
                sfTel: '',
                sex: '',
                address: '',
                email:''
              },
              editStaffFrom: {
                sfId:'',
                sfName: '',
                identityId: '',
                sfTel: '',
                sex: '',
                address: '',
                email:''
              },
              rules: {
                sfName: [
                  { required: true, message: '请输入姓名', trigger: 'blur' }
                ],
                identityId: [
                  { required: true, message: '请输入身份证号', trigger: 'blur' },
                  { min: 18, max: 18, message: '长度为18位', trigger: 'blur'  }
                ],
                sex: [
                  { required: true, message: '请选择性别', trigger: 'change' }
                ],
                sfTel: [
                  { required: true, message: '请输入电话', trigger: 'blur' }
                ],
                address: [
                  { required: true, message: '请输入地址', trigger: 'blur' }
                ],
                email: [
                  { required: true, message: '请输入邮箱', trigger: 'blur' }
                ],
              }
            }
        },
        //初始化第一页员工数据
        created() {
            let _self = this;
            document.onkeydown = function(e) {
                let key = window.event.keyCode;
                if (key === 13) {
                    _self.searchStaff();
                }
            }
          this.queryStaff();
        },
        methods: {
          //根据页码获取员工资料
          getPage(currentPage) {
            this.$http.get('/staff/basicInfo',{
              params: {
                  page: currentPage,
                  size: 5,
                  search: this.staffName
              }
            }).then(resp => {
              if (resp) {
                //获取数据成功
                this.staffData = resp.data.data;
                this.total = resp.data.recordsNum;
              } else {
                this.staffName = null;
                return;
              }
            })
          },
          //根据搜索关键词获取员工资料
          searchStaff() {
            this.queryStaff();
            this.$http.get('/staff/basicInfo',{
              params: {
                  page: 1,
                  size: 5,
                  search: this.staffName
              }
            }).then(resp => {
              if (resp) {
                //获取数据成功
                this.staffData = resp.data.data;
                this.total = resp.data.recordsNum;
              } else {
                this.staffName = null;
                return;
              }
            })
          },
          //查询员工
          queryStaff() {
            this.$http.get('/staff/basicInfo',{
                  params: {
                    page: 1,
                    size: 5,
                    search: ''
                  }
                }
            ).then(resp => {
              console.log(resp)
              if (resp) {
                //获取员工数据
                this.staffData = resp.data.data;
                //获取数据总条数
                this.total = resp.data.recordsNum;
              } else {
                this.$message.error("资料获取失败")
              }
            })
          },
          // 添加员工
          addStaff() {
            this.$http.post('/staff/addStaff',this.addStaffFrom).then(resp => {
              if (resp.status === 200){
                this.dialogAddForm = false;
                this.queryStaff();
              }else {
                this.$message.error('添加失败，请稍后重试');
              }
            })
          },
          //回显员工信息
          handleEdit(index,row) {
            this.dialogEditForm = true;
            this.isEdit = true;
            this.editStaffFrom = row;
          },
          //修改员工
          editStaff(){
            this.$http.post('/staff/editStaff',this.editStaffFrom).then(resp => {
              if (resp.status === 200){
                this.dialogEditForm = false;
                this.isEdit = false;
                this.queryStaff();
              }else {
                this.$message.error('修改失败，请稍后重试');
              }
            })
          },
          reset() {
            this.staffName = null;
            this.searchStaff();
          },
          cancel(){
            this.dialogAddForm=false;
            this.dialogEditForm=false;
          }
        }
    }
</script>

<style scoped>
    .el-table {
        font-size: 12px;
        width: 100%;
        margin-bottom: 10px;
    }
</style>

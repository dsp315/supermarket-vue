<template>
  <div>
    <!-- 卡片区域 -->
    <el-card class="box-card" shadow="hover">
      <el-row style="margin-top: 1vh;margin-left: 1vh;margin-bottom: 2vh">
        <el-button type="success" size="mini" icon="el-icon-circle-plus-outline" @click="dialogAddForm = true">添加</el-button>
      </el-row>
      <!-- 添加弹窗 开始-->
      <el-dialog title="添加供货商" :visible.sync="dialogAddForm" width="80vh">
        <el-form label-width="80px" :rules="rules" ref="addManageFrom" :model="addManageFrom" size="mini">
          <el-form-item label="名称" prop="spName" >
            <el-input v-model="addManageFrom.spName" ></el-input>
          </el-form-item>
          <el-form-item label="所在地" prop="location">
            <el-input v-model="addManageFrom.location" ></el-input>
          </el-form-item>
          <el-form-item label="联系方式" prop="spTel">
            <el-input v-model="addManageFrom.spTel"></el-input>
          </el-form-item>
          <el-form-item label="备注" prop="description">
            <el-input v-model="addManageFrom.description"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="cancel">取 消</el-button>
          <el-button type="primary" @click="addManage">提 交</el-button>
        </div>
      </el-dialog>
      <!-- 添加弹窗 结束-->
    </el-card>
    <div id="sellOrder">
      <div id="table">
        <div id="tableChild">
          <el-table
              :data="tableData" stripe size="mini" highlight-current-row
              border
              max-height="380"
          >
            <el-table-column
                fixed
                prop="spId"
                label="供货商编号"
                align="center"
                min-width="50">
            </el-table-column>
            <el-table-column
                prop="spName"
                label="供货商名"
                align="center"
                min-width="100">
            </el-table-column>
            <el-table-column
                prop="location"
                label="供货商地址"
                align="center"
                min-width="250">
            </el-table-column>
            <el-table-column
                prop="spTel"
                label="联系电话"
                align="center"
                min-width="80">
            </el-table-column>
            <el-table-column
                prop="description"
                label="描述"
                align="center"
                min-width="100">
            </el-table-column>
          </el-table>
        </div>
        <div class="block" id="pagination">
          <el-pagination
              style="float: left;margin-left: 10%"
              @size-change="handleSizeChange"
              @current-change="handleCurrentChange"
              :current-page="page"
              :page-sizes="[5, 10, 15, 20]"
              :page-size="pageSize"
              layout="total, sizes, prev, pager, next, jumper"
              :total="goodsTotal">
          </el-pagination>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
	export default {
		name: "sellOrder",
		data() {
			return {
				searchItem: {
					id: null,
					goodsName: null,
					price: null
				},
        addManageFrom: {
          spName: '',
          location: '',
          spTel: '',
          description: ''
        },
        dialogAddForm: false,  //弹窗控制
				tableData: [],   //用于渲染商品  搜索结构
				tableData1: [],  //用于重置
				tableAllData: [],   //存储所有商品项  每当add之后，都会存放在这里
				currentPage4: 4,
				data1: {
					id: '',
					sellItem: [
						{}
					]
				},
				page: 1,
				pageSize: 5,
				goodsTotal: 100,
				post: {
					id: '123',
					name: 'wsf'
				},
        rules: {
          spName: [
            { required: true, message: '请输入名称', trigger: 'blur' }
          ],
          location: [
            { required: true, message: '请输入所在地', trigger: 'blur' },
          ],
          spTel: [
            { required: true, message: '请选择联系方式', trigger: 'blur' }
          ],
        }
			}
		},
		methods: {
		  //添加
      addManage() {
        this.$http.post('/supplier/addSupplier',this.addManageFrom).then(resp => {
          if (resp.status === 200){
            this.dialogAddForm = false;
            this.getGoodsMsg()
            this.getAllGoodsMsg()
          }else {
            this.$message.error('添加失败，请稍后重试');
          }
        })
      },
			searchMessage() {
				let id = this.searchItem.id
				this.tableData = this.tableAllData.filter(function (item) {
					console.log(id)
					console.log(item)
					console.log(item.id)
					if (item.gdsId == id)
						return item
				})
				console.log(this.tableData)
			},
			reduceGoods(row) {
				console.log(row);
				if (row.num <= 0) {
					this.$message({
						message: '数量不能为负数',
						type: 'warning'
					})
				} else {
					row.num--
					this.tableAllData.forEach(function (item, index) {
						if (item.gdsId == row.gdsId) {
							item.num = row.num
						}
					})
				}
			},
			addGoods(row) {
				if (row.num >= row.amount) {
					this.$message({
						message: '数量不能超过最大存货量',
						type: 'warning'
					})
				} else {
					row.num++
					this.tableAllData.forEach(function (item, index) {
						if (item.gdsId == row.gdsId)
							item.num = row.num
					})
				}
				console.log(this.tableAllData)
			},
			handleSizeChange(val) {
				console.log(`每页 ${val} 条`);
				this.pageSize = val
				this.getGoodsMsg()
			},
			async handleCurrentChange(val) {
				console.log(`当前页: ${val}`);
				await this.getGoodsMsg(val, this.pageSize)
				console.log(this.tableAllData)
				this.tableData = this.tableAllData.filter((obj) => {
					for (let item of this.tableData) {
						if (obj.gdsId == item.gdsId) {
							return obj
						}
					}
				})
			},
			async getGoodsMsg(currentPage = this.page, pageSize = this.pageSize) {
				await this.$http('/supplier/' + currentPage + '/' + pageSize).then(resp => {
					this.tableData = []
					this.tableData = this.tableData.concat(resp.data.data)
					this.goodsTotal = resp.data.recordsNum
					this.pageSize = resp.data.pageSize
				})
			},
			getAllGoodsMsg() {
				this.$http('/sell/goods/1/1000').then(resp => {
					console.log(resp)
					for (let item of resp.data.data) {
						item.num = 0
					}
					this.tableAllData = []
					this.tableAllData = resp.data.data
				})
			},
			async commitOrder() {
				this.$http.defaults.headers.post['Content-Type'] = 'application/json';
				let sellItem = [
					{
						sellId: "null",
						gdsId: 14,
						price: 4,
						amount: 2
					}, {
						sellId: "null",
						gdsId: 15,
						price: 4,
						amount: 2
					}
				]
				sellItem = []
				this.tableAllData.forEach((item, index) => {
					if (item.num != 0) {
						sellItem = sellItem.concat({
							sellId: "null",
							gdsId: item.gdsId,
							price: item.price,
							amount: item.num
						})
						console.log(sellItem)
					}
				})
				await this.$http.post('/sell/addOrder/1', JSON.stringify(sellItem)).then(resp => {
					this.$message('添加成功')
				})
				await this.getGoodsMsg()
				await this.getAllGoodsMsg()
			},
			async reset() {
				this.searchItem = {
					id: null,
					goodsName: null,
					price: null
				}
				await this.getGoodsMsg()
				console.log(this.tableAllData)
				this.tableData = this.tableAllData.filter((obj) => {
					for (let item of this.tableData) {
						if (obj.gdsId == item.gdsId) {
							return obj
						}
					}
				})
			},
			watchNum(row){
				console.log('keyDowm')
				if (row.num >= row.amount){
					row.num = row.amount
				}
				if (row.num < 0) {
					row.num = 0
				}
			},
      cancel(){
        this.dialogAddForm=false
      }
		},
		mounted() {
			this.getGoodsMsg()
			this.getAllGoodsMsg()
		}
	}
</script>

<style scoped>
  #sellOrder {
    height: 100%;
    width: 100%;
  }

  #sellOrder #search {
    width: 100%;
    height: 15%;
  }

  #sellOrder #search #searchBox {
    width: 80%;
    height: 100%;
    margin: 0 auto;
    border-bottom: 1px solid #ccc;
    position: relative;
  }

  #sellOrder #table {
    width: 100%;
    height: 85%;
    margin-top: 50px;
    text-align: center;
  }

  #pagination {
    margin-top: 20px;
  }

  #tableChild {
    width: 80%;
    margin: 0px auto;
  }

  #addOrder {
    float: left;
    margin-left: 10px
  }
</style>

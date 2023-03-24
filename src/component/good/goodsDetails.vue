<template>
    <div id="goodsDetails">

      <!-- 卡片区域 -->
      <el-card class="box-card" shadow="hover">
        <el-row style="margin-top: 1vh;margin-left: 1vh;margin-bottom: 2vh">
          <el-button type="success" size="mini" icon="el-icon-circle-plus-outline" @click="dialogAddForm = true">添加</el-button>
        </el-row>
        <!-- 添加弹窗 开始-->
        <el-dialog title="添加商品" :visible.sync="dialogAddForm" width="80vh">
          <el-form label-width="80px" :rules="rules" ref="addGoodsFrom" :model="addGoodsFrom" size="mini">
            <el-form-item label="商品名称" prop="gdsName" >
              <el-input v-model="addGoodsFrom.gdsName" ></el-input>
            </el-form-item>
            <el-form-item label="品牌" prop="brand">
              <el-input v-model="addGoodsFrom.brand" ></el-input>
            </el-form-item>
            <el-form-item label="型号" prop="gdsmodel">
              <el-input v-model="addGoodsFrom.gdsmodel"></el-input>
            </el-form-item>
            <el-form-item label="种类" prop="category">
              <el-input v-model="addGoodsFrom.category"></el-input>
            </el-form-item>
            <el-form-item label="单价" prop="price">
              <el-input v-model="addGoodsFrom.price"></el-input>
            </el-form-item>
            <el-form-item label="库存" prop="amount">
              <el-input v-model="addGoodsFrom.amount" type="number"></el-input>
            </el-form-item>
            <el-form-item label="状态" prop="gdsStatus">
              <el-radio-group v-model="addGoodsFrom.gdsStatus">
                <el-radio label="1">正常</el-radio>
                <el-radio label="0">停产</el-radio>
              </el-radio-group>
            </el-form-item>
            <el-form-item label="单位" prop="unit">
              <el-input v-model="addGoodsFrom.unit"></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="cancel">取 消</el-button>
            <el-button type="primary" @click="addGoods">提 交</el-button>
          </div>
        </el-dialog>
        <!-- 添加弹窗 结束-->
      </el-card>
        
        <div id="table" style="margin-top:25px;width:95%;margin-left:25px">
            <div id="tableChild">
                <el-table :data="tableData" stripe size="mini" highlight-current-row
                          border
                          max-height="410">
                    <el-table-column
                            fixed
                            prop="gdsId"
                            label="商品编号"
                            align="center"
                            min-width="80">
                    </el-table-column>
                    <el-table-column
                            prop="gdsName"
                            label="商品名称"
                            align="center"
                            min-width="120">
                    </el-table-column>
                    <el-table-column
                            prop="brand"
                            label="商品品牌"
                            align="center"
                            min-width="100">
                    </el-table-column> 
                    <el-table-column
                            prop="model"
                            label="商品型号"
                            align="center"
                            min-width="150">
                    </el-table-column>
					<el-table-column
                            prop="category"
                            label="商品种类"
                            align="center"
                            min-width="100">
                    </el-table-column>
					<el-table-column
                            prop="amount"
                            label="库存"
                            align="center"
                            min-width="100">
                    </el-table-column> 
					<el-table-column
                            prop="unit"
                            label="单位"
                            align="center"
                            min-width="100">
                    </el-table-column>
                </el-table>
            </div>
            <!-- 分页组件 -->
            <div class="pagination" style="text-align: right; margin-top: 20px">
                <el-pagination background
                               layout="total, prev, pager, next, jumper"
                               @current-change="getPage"
                               :page-size="size"
                               :total="total">
                </el-pagination>
            </div>
		</div>
    </div>
</template>

<script>
  export default {
    name: "goodsDetails",
    data() {
      return {
        total: null,
        tableData: null,
        pageNo: 1,
        size: 5,
        dialogAddForm: false,
        addGoodsFrom: {
          unit:'',
          gdsStatus:'',
          amount:0,
          price: 0.0,
          category:'',
          gdsmodel:'',
          brand:'',
          gdsName:''
        },
        rules: {
          gdsName: [
            { required: true, message: '请输入名称', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '请输入单价', trigger: 'blur' }
          ]
        }
      }
    },
  methods: {
    //根据页码获取资料
    getPage(currentPage) {
      this.pageNo = currentPage;
      this.$http.get('/goods/'+this.pageNo+'/'+this.size).then(resp => {
        if (resp) {
          this.tableData = resp.data.data;
          //获取数据总条数
          this.total = resp.data.recordsNum;
        } else {
          this.$message.error("资料获取失败")
        }
      })
    },
    //添加
    addGoods(){
      this.$http.post('/goods/addGoods',this.addGoodsFrom).then(resp => {
        if (resp.status === 200){
          this.dialogAddForm = false;
          this.queryGoods();
        }else {
          this.$message.error('添加失败，请稍后重试');
        }
      })
    },
    //查询商品
    queryGoods(){
      this.$http.get('/goods/'+this.pageNo+'/'+this.size).then(resp => {
        if (resp) {
          this.tableData = resp.data.data;
          //获取数据总条数
          this.total = resp.data.recordsNum;
        } else {
          this.$message.error("资料获取失败")
        }
      })
    },
    cancel() {
      this.dialogAddForm = false;
    }
  },
  created() {
    this.queryGoods();
  }
}
</script>

<style scoped>
	.pagination {
        display: flex;
        justify-content: space-between;
        font-weight: 600;
    }
</style>
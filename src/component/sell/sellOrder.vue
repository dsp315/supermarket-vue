<template>
    <div id="sellOrder">
        <div id="search">
            <div id="searchBox">
                <el-input v-model="searchItem.id" style="width: 25%;margin-left: 25%;margin-right: 2%;margin-top: 30px"
                          placeholder="搜索商品id"></el-input>
                <el-button type="success" @click="searchMessage">搜索</el-button>
                <el-button type="success" @click="reset">重置</el-button>
                <el-button type="success" @click="dialogAddForm = true">销售单</el-button>
            </div>
        </div>
      <!-- 添加弹窗 开始-->
      <el-dialog title="添加商品" :visible.sync="dialogAddForm" width="50vh">
        <el-form label-width="80px" :rules="rules" ref="addGoodsFrom" :model="addOrderFrom">
          <el-form-item label="商品名称" prop="gdsName" size="mini">
            <el-select v-model="addOrderFrom.gdsId"
                       clearable
                       filterable
                       placeholder="请选择商品名称">
              <el-option
                  v-for="(item,index) in tableData"
                  :label="item.gdsName"
                  :value="item.gdsId"
                  :key="index"
              />
            </el-select>
<!--            <el-input  ></el-input>-->
          </el-form-item>
          <el-form-item label="售价" prop="price" size="mini">
            <el-input v-model="addOrderFrom.price" style="width: 24vh"></el-input>
          </el-form-item>
          <el-form-item label="数量" prop="amount" size="mini">
            <el-input v-model="addOrderFrom.amount" type="number" style="width: 24vh"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="cancel">取 消</el-button>
          <el-button type="primary" @click="addOrder">提 交</el-button>
        </div>
      </el-dialog>
      <!-- 添加弹窗 结束-->
        <div id="table">
            <div id="tableChild">
                <el-table
                        :data="tableData" stripe size="mini" highlight-current-row
                        border
                >
                    <el-table-column
                            fixed
                            prop="gdsId"
                            label="商品id"
                            align="center"
                            min-width="80">
                    </el-table-column>
                    <el-table-column
                            prop="gdsName"
                            label="商品名"
                            align="center"
                            min-width="120">
                    </el-table-column>
                    <el-table-column
                            prop="price"
                            label="单价"
                            align="center"
                            min-width="100">
                    </el-table-column>
                    <el-table-column
                            prop="amount"
                            label="存货量"
                            align="center"
                            min-width="100">
                    </el-table-column>
                </el-table>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "sellOrder",
        data() {
            return {
              dialogAddForm: false,
              addOrderFrom: {
                gdsId:'',
                price:'',
                amount:''
              },
                searchItem: {
                    id: null,
                    goodsName: null,
                    price: null
                },
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
                total: null,
                post: {
                    id: '123',
                    name: 'wsf'
                },
              rules: {
                price: [
                  { required: true, message: '请输入名称', trigger: 'blur' }
                ],
                amount: [
                  { required: true, message: '请输入单价', trigger: 'blur' }
                ]
              }
            }
        },
        methods: {
          //根据页码获取资料
          getPage(currentPage) {
            this.pageNo = currentPage;
            this.$http.get('/sell/goods/'+this.pageNo+'/'+this.size).then(resp => {
              if (resp) {
                this.tableData = resp.data.data;
                //获取数据总条数
                this.total = resp.data.recordsNum;
                this.tableAllData = []
                this.tableAllData = resp.data.data
              } else {
                this.$message.error("资料获取失败")
              }
            })
          },
            searchMessage() {
                var param = this.searchItem.id
                this.tableData = this.tableAllData.filter((obj) => {
                  if (obj.gdsId == param){
                    return obj
                  }
              })
            },
            getGoodsMsg() {
                this.$http.post('/sell/goods/1/100',this.searchItem.id).then(resp => {
                    this.tableAllData = resp.data.data
                    this.tableData = this.tableAllData;
                    this.total = resp.data.recordsNum;
                })
            },
          async addOrder(){
            await this.$http.post('/sell/addOrder/1', this.addOrderFrom).then(resp => {
              if (resp.status == 200){
                this.dialogAddForm = false;
                this.$message('添加成功');
              }else{
                this.$message.error(resp.msg);
              }
            })
            await this.getGoodsMsg()
          },
            async reset() {
                this.searchItem = {
                    id: null
                }
                this.getGoodsMsg();
            },
          cancel() {
            this.dialogAddForm = false;
          },
        },
        mounted() {
            this.getGoodsMsg()
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
        text-align: center;
    }

    #pagination {
        margin-top: 20px;
    }

    #tableChild {
        width: 80%;
        margin: 0px auto;
    }

</style>

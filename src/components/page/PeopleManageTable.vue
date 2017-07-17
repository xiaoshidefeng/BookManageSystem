<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>人员管理表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="handle-box">
          <el-button type="primary" icon="plus" @click="addBooks( )">添加人员</el-button>
            <!-- <el-button class="handle-del mr10">批量删除</el-button> -->
            <!-- <el-select v-model="select_cate" placeholder="筛选省份" class="handle-select mr10">
                <el-option key="1" label="浙江省" value="1"></el-option>
                <el-option key="2" label="江苏省" value="2"></el-option>
            </el-select> -->
            <el-input v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"></el-input>
            <el-button type="primary" icon="search">搜索</el-button>
        </div>
        <el-table :data="tableData" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="clientId" label="id" sortable width="130">
            </el-table-column>
            <el-table-column prop="clientname" label="姓名" width="170">
            </el-table-column>
            <el-table-column prop="clientaddress" label="地址"  >
            </el-table-column>
            <el-table-column label="操作" width="160">
                <template scope="scope">
                    <el-button size="small"
                            @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                    <el-button size="small" type="danger"
                            @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <div class="pagination">
            <el-pagination
                    @current-change ="handleCurrentChange"
                    layout="prev, pager, next"
                    :total="1000">
            </el-pagination>
        </div>

        <div id="add">
          <el-dialog title="添加人员" :visible.sync="addbook">
          <el-form :model="form">
            <el-form-item label="姓名" :label-width="formLabelWidth">
              <el-input v-model="addform.newName" auto-complete="off"></el-input>
            </el-form-item>
            <el-form-item label="住址" :label-width="formLabelWidth">
              <el-input v-model="addform.newAddress" auto-complete="off"></el-input>
            </el-form-item>

          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="addbook = false">取 消</el-button>
            <el-button type="primary" @click="addBookToSql()">确 定</el-button>
          </div>
          </el-dialog>
        </div>

        <div id="updateform">
          <el-dialog title="修改用户信息" :visible.sync="update">
            <el-form :model="form">
              <el-form-item label="用户姓名" :label-width="formLabelWidth">
                <el-input v-model="addform.newName" auto-complete="off"></el-input>
              </el-form-item>
              <el-form-item label="地址" :label-width="formLabelWidth">
                <el-input v-model="addform.newAddress" auto-complete="off"></el-input>
              </el-form-item>
           </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="update = false">取 消</el-button>
            <el-button type="primary" @click="updateToSql()">确 定</el-button>
          </div>
          </el-dialog>
        </div>

    </div>
</template>

<script>
    export default {
        data() {
            return {
                url: '../../../static/vuetable.json',
                api: 'http://118.89.159.95:8890/api/',
                tableData: [],
                cur_page: 1,
                multipleSelection: [],
                select_cate: '',
                select_word: '',
                addbook: false,
                update: false,
                updateBookload: false,
                id: '',
                addform: {
                  newName: '',
                  newAdress: ''
                },
                form: {
                  name: '',
                  region: '',
                  date1: '',
                  date2: '',
                  delivery: false,
                  type: [],
                  resource: '',
                  desc: ''
                },
                formLabelWidth: '120px'
            }
        },
        created(){
            this.getData();
        },
        methods: {
            handleCurrentChange(val){
                this.cur_page = val;
                this.getData();
            },
            getData(){
                let self = this;
                // if(process.env.NODE_ENV === 'development'){
                //     self.url = '/ms/table/list';
                // };
                self.$axios.get(self.api + 'clients').then((response) => {
                  // console.log(response.data)
                  self.tableData = response.data;
                  console.log(response.data)
                })
                // self.$axios.post(self.url, {page:self.cur_page}).then((res) => {
                //     self.tableData = res.data.list;
                // })
            },
            formatter(row, column) {
                return row.address;
            },
            filterTag(value, row) {
                return row.tag === value;
            },
            addBooks() {
                this.addbook = true;
            },
            handleEdit(index, row) {
                // this.$message('编辑第'+ row.write +'行');
                this.update = true;
                this.addform.newName = row.clientname;
                this.addform.newAddress = row.clientaddress;
                this.id = row.clientId;
            },
            updateToSql(row) {
              if(this.addform.newName == "") {
                this.$message({
                  type: 'warning',
                  message: '名字为空'
                });
              }else if(this.addform.newAddress == "") {
                this.$message({
                  type: 'warning',
                  message: '地址为空'
                });
              }else {


                var qs = require('qs');
                this.$axios.post(this.api + 'updateClient/' + this.id, qs.stringify({
                  clientname: this.addform.newName,
                  clientaddress: this.addform.newAddress,
                }),
                {
                  headers: {
                    'Content-Type': 'application/x-www-form-urlencoded'
                  }
                }).then(response => {
                  console.log(response.data);
                })
              }

            this.update = false;

            this.$message({
              type: 'warning',
              message: '更新完成'
            });

            },
            handleDelete(index, row) {
            //删除
            //this.$message.error('删除第'+(index+1)+'行');
            this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
              confirmButtonText: '确定',
              cancelButtonText: '取消',
              type: 'warning'
            }).then(() => {

              console.log(row.bookId);
              var url = this.api + 'deleteClient';
              var qs = require('qs');
              this.$axios.post(url, qs.stringify({
                // book_id: row.book_id
                clientId: row.clientId
              }),
              {
                headers: {
                  'Content-Type': 'application/x-www-form-urlencoded'
                }
              }).then(response => {
                console.log(response.data);
              })


              this.$message({
                type: 'success',
                message: '删除成功!'
              });
              this.tableData.splice(index, 1);
            }).catch(() => {
              this.$message({
                type: 'info',
                message: '已取消删除'
              });
            });

          },
          addBookToSql() {
            if(this.addform.newName == "") {
              this.$message({
                type: 'warning',
                message: '名字为空'
              });
            }else if(this.addform.newAddress == "") {
              this.$message({
                type: 'warning',
                message: '地址为空'
              });
            }else {

              var qs = require('qs');
              this.$axios.post(this.api + 'insertClient', qs.stringify({
                clientname: this.addform.newName,
                clientaddress: this.addform.newAddress
              }),
              {
                headers: {
                  'Content-Type': 'application/x-www-form-urlencoded'
                }
              }).then(response => {
                console.log(response.data);
              })
            }

            this.addbook = false;

          },
          handleSelectionChange: function(val) {
              this.multipleSelection = val;
          }
          }

    }
</script>

<style scoped>
.handle-box{
    margin-bottom: 20px;
}
.handle-del{
    border-color: #bfcbd9;
    color: #999;
}
.handle-select{
    width: 120px;
}
.handle-input{
    width: 300px;
    display: inline-block;
}
</style>

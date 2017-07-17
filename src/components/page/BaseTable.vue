<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>图书管理表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="handle-box">
          <el-button type="primary" icon="plus" @click="addBooks( )">添加图书</el-button>
            <!-- <el-button class="handle-del mr10">批量删除</el-button> -->
            <!-- <el-select v-model="select_cate" placeholder="筛选省份" class="handle-select mr10">
                <el-option key="1" label="浙江省" value="1"></el-option>
                <el-option key="2" label="江苏省" value="2"></el-option>
            </el-select> -->
            <el-input v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"></el-input>
            <el-button type="primary" icon="search">搜索</el-button>
        </div>
        <el-table :data="tableData" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
            <!-- <el-table-column type="selection" width="55"></el-table-column> -->
            <el-table-column prop="rent" label="借书" width="75">
              <template scope="scope">
                <el-button size="small" type="primary"
                        @click="handleRentBook(scope.$index, scope.row)">借书</el-button>
              </template>

            </el-table-column>

            <el-table-column prop="bookId" label="id" sortable width="60">
            </el-table-column>
            <el-table-column prop="isbn" label="ISBN" width="180">
            </el-table-column>
            <el-table-column prop="author" label="作者" width="100">
            </el-table-column>
            <el-table-column prop="publisher" label="出版社" width="125">
            </el-table-column>
            <el-table-column prop="name" label="书名">
            </el-table-column>
            <el-table-column label="操作" width="150">
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
          <el-dialog title="添加图书" :visible.sync="addbook">
          <el-form :model="form">
            <el-form-item label="图书名称" :label-width="formLabelWidth">
              <el-input v-model="addform.newBookName" auto-complete="off"></el-input>
            </el-form-item>
            <el-form-item label="作者" :label-width="formLabelWidth">
              <el-input v-model="addform.newAuthor" auto-complete="off"></el-input>
            </el-form-item>
            <el-form-item label="库存" :label-width="formLabelWidth">
              <el-input v-model="addform.newInventory" type="number" auto-complete="off"></el-input>
            </el-form-item>
            <el-form-item label="出版社" :label-width="formLabelWidth">
              <el-input v-model="addform.newpublisher" auto-complete="off"></el-input>
            </el-form-item>
            <el-form-item label="isbn" :label-width="formLabelWidth">
              <el-input v-model="addform.newisbn" auto-complete="off"></el-input>
            </el-form-item>

          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="addbook = false">取 消</el-button>
            <el-button type="primary" @click="addBookToSql()">确 定</el-button>
          </div>
          </el-dialog>
        </div>

        <div id="updateform">
          <el-dialog title="修改图书信息" :visible.sync="update">
            <el-form :model="form">
              <el-form-item label="图书名称" :label-width="formLabelWidth">
                <el-input v-model="addform.newBookName" auto-complete="off"></el-input>
              </el-form-item>
              <el-form-item label="作者" :label-width="formLabelWidth">
                <el-input v-model="addform.newAuthor" auto-complete="off"></el-input>
              </el-form-item>
              <el-form-item label="库存" :label-width="formLabelWidth">
                <el-input v-model="addform.newInventory" type="number" auto-complete="off"></el-input>
              </el-form-item>
              <el-form-item label="出版社" :label-width="formLabelWidth">
                <el-input v-model="addform.newpublisher" auto-complete="off"></el-input>
              </el-form-item>
              <el-form-item label="isbn" :label-width="formLabelWidth">
                <el-input v-model="addform.newisbn" auto-complete="off"></el-input>
              </el-form-item>
           </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="update = false">取 消</el-button>
            <el-button type="primary" @click="updateBookToSql()">确 定</el-button>
          </div>
          </el-dialog>
        </div>

        <div id="rentFrom">
          <el-dialog title="借书" :visible.sync="rentbook">
            <el-select v-model="clientId" placeholder="请选择">
              <el-option
                v-for="item in options"
                :key="item.clientId"
                :label="item.clientname"
                :value="item.clientId">
              </el-option>
            </el-select>
          <div slot="footer" class="dialog-footer">
            <el-button @click="rentbook = false">取 消</el-button>
            <el-button type="primary" @click="rentBookToSql()">借 书</el-button>
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
                rentbook: false,
                id: '',
                bid: '',
                options: [{
                  clientId: '',
                  clientname: '',
                  clientaddress: ''
                }],
                clientId: '',
                // options: [],
                // clientname: '',
                // options: [],
                addform: {
                  newBookName: '',
                  newAuthor: '',
                  newInventory: '',
                  newpublisher: '',
                  newisbn: ''
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
                self.$axios.get(self.api + 'books').then((response) => {
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
                this.addform.newBookName = row.name;
                this.addform.newAuthor = row.author;
                this.addform.newInventory = row.inventory;
                this.addform.newpublisher = row.publisher;
                this.addform.newisbn = row.isbn;
                this.id = row.bookId;
            },
            updateBookToSql(row) {
              if(this.addform.newBookName == "") {
                this.$message({
                  type: 'warning',
                  message: '书籍名字为空'
                });
              }else if(this.addform.newisbn == "") {
                this.$message({
                  type: 'warning',
                  message: 'ISBN为空'
                });
              }else if(this.addform.newAuthor == "") {
                this.$message({
                  type: 'warning',
                  message: '作者名字为空'
                });
              }else {

                var qs = require('qs');
                this.$axios.post(this.api + 'updateBook/' + this.id, qs.stringify({
                  name: this.addform.newBookName,
                  author: this.addform.newAuthor,
                  inventory: this.addform.newInventory,
                  publisher: this.addform.newpublisher,
                  isbn: this.addform.newisbn
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

            },
            handleDelete(index, row) {
            //删除
            //this.$message.error('删除第'+(index+1)+'行');
            this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
              confirmButtonText: '确定',
              cancelButtonText: '取消',
              type: 'warning'
            }).then(() => {

              console.log(row.bookid);
              var url = this.api + 'deleteBook/' + row.bookId;
              var qs = require('qs');
              this.$axios.post(url, qs.stringify({
                // book_id: row.book_id
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
            if(this.addform.newBookName == "") {
              this.$message({
                type: 'warning',
                message: '书籍名字为空'
              });
            }else if(this.addform.newisbn == "") {
              this.$message({
                type: 'warning',
                message: 'ISBN为空'
              });
            }else if(this.addform.newAuthor == "") {
              this.$message({
                type: 'warning',
                message: '作者名字为空'
              });
            }else {

              var qs = require('qs');
              this.$axios.post(this.api + 'insertBook', qs.stringify({
                name: this.addform.newBookName,
                author: this.addform.newAuthor,
                inventory: this.addform.newInventory,
                publisher: this.addform.newpublisher,
                isbn: this.addform.newisbn
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
          },
          handleRentBook(index, row) {
              this.bid = row.bookId;
              this.rentbook = true;
              let self = this;
              self.$axios.get(self.api + 'clients').then((response) => {
                self.options = response.data;
                console.log(response.data)
              })
          },
          rentBookToSql() {
              // console.log(clientname);
              var s = new Date().toLocaleString( );
              console.log(s);
              var qs = require('qs');
              this.$axios.post(this.api + 'rentBook', qs.stringify({
                clientId: this.clientId,
                bookId: this.bid,
                renttime: s
              }),
              {
                headers: {
                  'Content-Type': 'application/x-www-form-urlencoded'
                }
              }).then(response => {
                console.log(response.data);
              })
              this.rentbook = false;
              this.$message({
                type: 'success',
                message: '借书成功'
              });
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

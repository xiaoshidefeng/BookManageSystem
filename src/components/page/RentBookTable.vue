<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-menu"></i> 表格</el-breadcrumb-item>
                <el-breadcrumb-item>借书情况表</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <el-table
          :data="tableData"
          style="width: 100%">
          <el-table-column
            prop="renttime"
            label="借书日期"
            width="180">
          </el-table-column>
          <el-table-column
            prop="clientId"
            label="用户id"
            width="80">
          </el-table-column>
          <el-table-column
            prop="clientName"
            label="姓名"
            width="80">
          </el-table-column>
          <el-table-column
            prop="bookId"
            label="图书id"
            width="80">
          </el-table-column>
          <el-table-column
            prop="bookName"
            label="书名">
          </el-table-column>
          <el-table-column label="操作" width="100">
              <template scope="scope">
                  <el-button size="small"
                          @click="returnBook(scope.$index, scope.row)">还书</el-button>
              </template>
          </el-table-column>
        </el-table>

      </div>
</template>

      <script>
        export default {
          data() {
            return {
              // tableData: [{
              //   date: '2016-05-02',
              //   name: '王小虎',
              //   address: '上海市普陀区金沙江路 1518 弄'
              // }, {
              //   date: '2016-05-04',
              //   name: '王小虎',
              //   address: '上海市普陀区金沙江路 1517 弄'
              // }, {
              //   date: '2016-05-01',
              //   name: '王小虎',
              //   address: '上海市普陀区金沙江路 1519 弄'
              // }, {
              //   date: '2016-05-03',
              //   name: '王小虎',
              //   address: '上海市普陀区金沙江路 1516 弄'
              // }]
              tableData: [],
              api: 'http://118.89.159.95:8890/api/'
            }
          },
          created(){
              this.getData();
          },
          methods: {
              getData(){
                  let self = this;
                  self.$axios.get(self.api + 'rents').then((response) => {
                    self.tableData = response.data;
                    console.log(response.data)
                  })
              },
              returnBook(index, row) {

                  this.$confirm('是否还掉' + row.bookName, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                  }).then(() => {
                    var s = new Date().toLocaleString( );
                    console.log(row.clientId);
                    var qs = require('qs');
                    this.$axios.post(this.api + 'returnBook', qs.stringify({
                      clientId: row.clientId,
                      bookId: row.bookId,
                      renttime: row.renttime,
                      returntime: s
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
                      message: '还书成功!'
                    });
                    this.tableData.splice(index, 1);
                  }).catch(() => {
                    this.$message({
                      type: 'info',
                      message: '已取消还书'
                    });
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

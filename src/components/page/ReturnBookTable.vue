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
            prop="returntime"
            label="还书日期"
            width="180">
          </el-table-column>
          <el-table-column
            prop="clientName"
            label="姓名"
            width="180">
          </el-table-column>
          </el-table-column>
          <el-table-column
            prop="bookName"
            label="书名">
          </el-table-column>
        </el-table>

      </div>
</template>

      <script>
        export default {
          data() {
            return {
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
                  self.$axios.get(self.api + 'returns').then((response) => {
                    self.tableData = response.data;
                    console.log(response.data)
                  })
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

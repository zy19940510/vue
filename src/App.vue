<template>
  <div id="app">
    <section  class="main-content-wrapper wrapper" >
        <section id="main-content" >
            <div class="row">
                <div class="col-md-12">
                    <!-- 用户列表-->
                    <el-table :data="users"
                              stripe
                              v-loading="loading"
                              element-loading-text="拼命加载中..."
                              style="width: 100%"
                              height="600"
                              border
                              @sort-change="tableSortChange"
                              @selection-change="tableSelectionChange">
                        <el-table-column type="selection"
                                         width="100">
                        </el-table-column>
                        <el-table-column sortable="custom" prop="title"
                                         label="title"
                                         width="600">
                        </el-table-column>
                        <el-table-column prop="url"
                                         label="链接"
                                         width="600">
                        </el-table-column>
                        <el-table-column prop="date" sortable="custom" inline-template
                                         label="日期">
                            <div>{{ row.date }}</div>
                        </el-table-column>
                        <el-table-column inline-template
                                         label="操作"
                                         width="250">
                            <span>
                                <el-button type="primary" size="mini" @click="removeUser(row)">删除</el-button>
                                <el-button type="primary" size="mini" @click="setCurrent(row)">编辑</el-button>
                            </span>
                        </el-table-column>
                    </el-table>
                    <!--分页begin-->
                    <el-pagination class="tc mg"
                                   :current-page="filter.page"
                                   :page-sizes="[10, 20, 50, 100]"
                                   :page-size="filter.pagesize"
                                   layout="total, sizes, prev, pager, next, jumper"
                                   :total="total_rows"
                                   @size-change="pageSizeChange"
                                   @current-change="pageCurrentChange">
                    </el-pagination>
                    <!--分页end-->
                </div>
            </div>
        </section>
    </section>
  </div>
</template>
<script>
export default {
  name: "news",
  data: function() {
    return {
      url: "http://10.116.19.116:3002/news",
      users: [],
      filter: {
        pagesize: 10, // 页大小
        page: 1, // 当前页
        date: "",
        url: "",
        title: "",
        sortby: "",
      },
      total_rows: 0,
      loading: true,
      selected: [], //已选择项
      dialogUpdateVisible: false, //编辑对话框的显示状态
      updateLoading: false
    };
  },
  mounted: function() {
    this.getUsers();
  },
  methods: {
    tableSelectionChange(val) {
      this.selected = val;
    },
    tableSortChange(val) {
      console.log(`排序属性: ${val.prop}`);
      console.log(`排序: ${val.order}`);
      if (val.prop != null) {
        if (val.order == "descending") {
          this.filter.sorts = "-" + val.prop;
        } else {
          this.filter.sorts = "" + val.prop;
        }
      } else {
        this.filter.sorts = "";
      }
      this.getUsers();
    },
    pageSizeChange(val) {
      console.log(`每页 ${val} 条`);
      this.filter.pagesize = val;
      this.getUsers();
    },
    pageCurrentChange(val) {
      console.log(`当前页: ${val}`);
      this.filter.page = val;
      this.getUsers();
    },
    // 获取用户列表
    getUsers() {
      this.loading = true;

      var resource = this.$resource(this.url);
      resource
        .query(this.filter)
        .then(response => {
          this.users = response.body.results;
          this.total_rows = response.data.total_rows;
          this.loading = false;
          this.selected.splice(0, this.selected.length);
        })
        .catch(response => {
          this.$message.error("错了哦，这是一条错误消息");
          this.loading = false;
        });
    }
  }
};
</script>
<style>
@import "./assets/plugins/bootstrap/css/bootstrap.min.css";
@import "./assets/css/global.css";
@import "./assets/css/main.min.css";
</style>
<template>
  <div>
    <div>
      <el-button type="primary" class="add myBtn" @click="$router.push(`/admin_users/create`)">
        <i class="el-icon-s-custom"></i> 添加管理员
      </el-button>
    </div>
    <div class="card">
      <h1 class="card-header">管理员列表</h1>
      <el-table :data="items" stripe border :header-cell-style="{background:'#eee'}">
        <el-table-column prop="id" label="ID" width="240"></el-table-column>
        <el-table-column prop="username" label="用户名"></el-table-column>
        <el-table-column prop="mobile" label="手机号"></el-table-column>
        <el-table-column prop="email" label="邮箱"></el-table-column>
        <el-table-column fixed="right" label="操作" align="center" width="180">
          <template slot-scope="scope">
            <el-tooltip effect="light" content="编辑" placement="bottom">
              <el-button
                size="mini"
                type="primary"
                plain
                icon="el-icon-edit"
                @click="$router.push(`/admin_users/edit/${scope.row.id}`)"
              ></el-button>
            </el-tooltip>

            <el-tooltip effect="light" content="删除" placement="bottom">
              <el-button
                size="mini"
                type="danger"
                plain
                icon="el-icon-delete"
                @click="remove(scope.row)"
              ></el-button>
            </el-tooltip>
            <!-- <el-button
              type="text"
              size="small"
              @click="$router.push(`/admin_users/edit/${scope.row.id}`)"
            ><i class="el-icon-edit" title="编辑"></i></el-button>
            <el-button type="text" size="small" @click="remove(scope.row)"><i class="el-icon-delete" title="删除"></i></el-button>-->
          </template>
        </el-table-column>
      </el-table>

      <!-- <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="[3, 6, 9, 12]"
          :page-size="3"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total">
      </el-pagination>-->

      <el-pagination
        background
        layout="prev, pager, next"
        :page-size="pageSize"
        @next-click="nextClick"
        @prev-click="prevClick"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :total="total"
      ></el-pagination>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      items: [],
      pageSize: 8,
      total: 0,
      pageIndex: 1
    };
  },
  methods: {
    nextClick(val) {
      this.pageIndex += 1;
      this.fetch();
    },
    prevClick(val) {
      this.pageIndex -= 1;
      this.fetch();
    },
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      this.pageIndex = val;
      this.fetch();
    },
    async fetch() {
      const res = await this.$http.get("admin/page", {
        params: { page_index: this.pageIndex, page_size: this.pageSize }
      });
      console.log(res.data);
      if (res.code == 200) {
        this.items = res.data.result;
        this.total = res.data.total_count;
      }
    },
    remove(row) {
      this.$confirm(`确定要删除管理员："${row.username}" 吗？`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(async () => {
        const res = await this.$http.post(`admin/delete?ids=${row.id}`);
        if (res.code == 200) {
          this.$message({
            type: "success",
            message: "删除成功!"
          });
          this.fetch();
        } else {
          this.$message({
            type: "error",
            message: `错误：${res.msg}`
          });
        }
      });
    }
  },
  created() {
    this.fetch();
  }
};
</script>


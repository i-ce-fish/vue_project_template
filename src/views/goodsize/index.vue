<template>
  <div class="app-container">
    <y-form
      ref="goodsizeForm"
      :model="goodsizeForm"
      label-width="80px"
    >
      <el-row type="flex" justify="end">
        <el-form-item>
          <el-button type="success" @click="add">添加尺码表</el-button>
        </el-form-item>
      </el-row>

    </y-form>

    <y-table
:data="goodsizesData"
:pagination="pagination"
@sortBy="sortBy"
             @changePage4List="getList">
      <template>

        <el-table-column
          prop="size_name"
          label="尺码名"

        >

        </el-table-column>

        <el-table-column
          prop="display_name"
          label="尺寸显示名"

        >

        </el-table-column>

        <el-table-column
          prop="description"
          label="尺寸解释"

        >

        </el-table-column>

        <el-table-column
          prop="color_id"
          label="ColorID"

          width="100px"
          align='center'>

        </el-table-column>

        <el-table-column
          prop="inventory"
          label="库存数量"

        >

        </el-table-column>

        <el-table-column
          prop="good_id"
          label="货品ID"

          width="100px"
          align='center'>

        </el-table-column>

        <el-table-column label="操作" width="100px">
          <template slot-scope="{row}">
            <el-button type="text" size="small" @click="edit(row.id)">修改</el-button>
            <el-button type="text" size="small" @click="del(row.id)">删除</el-button>
          </template>
        </el-table-column>
      </template>
    </y-table>
  </div>
</template>
<script>
import { getGoodsizes, delGoodsize } from "@/api/goodsize"

export default {
  data() {
    return {
      goodsizeForm: {},
      goodsizesData: [],
      pagination: {
        pageNumber: 1,
        pageSize: 10
      }

    }
  },
  created() {
    this.getList()
  },
  methods: {
    async getList(param) {
      const response = await getGoodsizes(
        {
          ...param,
          page: this.pagination.pageNumber,
          pagesize: this.pagination.pageSize
        }
      )
      this.goodsizesData = response.data.list
      this.pagination.total = parseInt(response.data.pagination.total, 10)
    },

    add() {
      this.$router.push({ path: "add" })
    },
    edit(id) {
      this.$router.push({
        path: "edit",
        query: { id }
      })
    },
    del(id) {
      this.$confirm("是否删除?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          delGoodsize(id)
            .then((response) => {
              this.$message({
                type: "success",
                message: "删除成功!"
              })
              this.getList()
            })
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          })
        })
    },
    onSearch(sort) {
      this.getList({ ...this.goodsizeForm, ...sort })
    },
    sortBy(e) {
      this.onSearch(e)
    },
    reset() {
      this.goodsizeForm = {}
      this.getList()
    }
  }
}
</script>

<style lang='scss' scope>
  .app-container {
    padding: 20px;

    .no-margin {
      margin: 0;
    }
  }
</style>

<template>
    <div class="app-container">
        <y-form
                ref="categoryForm"
                :model="categoryForm"
                label-width="80px"
        >
            <el-row type="flex" justify="end">
                <el-form-item>
                    <el-button type="success" @click="add">添加商品分类</el-button>
                </el-form-item>
            </el-row>

            <el-row type="flex" justify="space-between">
                <el-col :span="20">
                    <el-row>

                        <el-col :span="6">
                            <el-form-item label="类别名:" prop="name">

                                <y-input

                                v-model="categoryForm.name"

                                />
                            </el-form-item>
                        </el-col>

                    </el-row>
                </el-col>
                <el-col :span="4">
                    <el-row type="flex" justify="end">
                        <el-form-item>
                            <el-button type="primary" @click="onSearch">查询</el-button>
                            <el-button @click="reset" class="no-margin">重置</el-button>
                        </el-form-item>
                    </el-row>
                </el-col>
            </el-row>

        </y-form>

        <y-table :data="categoriesData" :pagination="pagination" @sortBy="sortBy" @changePage4List="getList">
            <template>

                        <el-table-column
prop="name"
label="类别名"

                                          >

                        </el-table-column>

                        <el-table-column
prop="sort"
label="排序"

                                          >

                        </el-table-column>

                        <el-table-column
prop="parent_id"
label="父ID"

                                          width="100px"
                                           align='center'   >

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
import { getCategories, delCategory } from "@/api/category"

export default {
  data() {
    return {
      categoryForm: {},
      categoriesData: [],
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
      const response = await getCategories(
        {
          ...param,
          page: this.pagination.pageNumber,
          pagesize: this.pagination.pageSize
        }
      )
      this.categoriesData = response.data.list
      this.pagination.total = parseInt(response.data.pagination.total, 10)
    },

    add() {
      this.$router.push({ path: "add" })
    },
    edit(id) {
      this.$router.push({ path: "edit", query: { id }})
    },
    del(id) {
      this.$confirm("是否删除?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          delCategory(id).then((response) => {
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
      this.getList({ ...this.categoryForm, ...sort })
    },
    sortBy(e) {
      this.onSearch(e)
    },
    reset() {
      this.categoryForm = {}
      this.getList()
    }
  }
}
</script>

<style lang='scss' scope>
    .app-container {
        padding: 20px;
        .no-margin{
            margin: 0;
        }
    }
</style>

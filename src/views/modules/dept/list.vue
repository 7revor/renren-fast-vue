<template>
  <div class="mod-menu">
    <el-form :inline="true" :model="dataForm">
      <el-form-item>
        <el-button v-if="isAuth('sys:menu:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
      </el-form-item>
    </el-form>
    <el-table
            :data="dataList"
            row-key="menuId"
            border
            style="width: 100%; ">
      <el-table-column
              prop="name"
              header-align="center"
              min-width="150"
              align="center"
              label="名称" >
      </el-table-column>
      <el-table-column
              prop="code"
              header-align="center"
              align="center"
              width="120"
              label="编码">
      </el-table-column>
      <el-table-column
              header-align="center"
              label="部门职能"
              align="center">
        <template slot-scope="scope">
          <span> {{ getFunction(scope.row.functions) }}</span>
        </template>
      </el-table-column>
      <el-table-column
              prop="headMan"
              header-align="center"
              align="center"
              label="负责人">
      </el-table-column>
      <el-table-column
              prop="tel"
              header-align="center"
              align="center"
              label="联系电话">
      </el-table-column>
      <el-table-column
              prop="address"
              header-align="center"
              align="center"
              width="150"
              :show-overflow-tooltip="true"
              label="联系地址">
      </el-table-column>
      <el-table-column
              prop="memo"
              header-align="center"
              align="center"
              width="150"
              :show-overflow-tooltip="true"
              label="备注">
      </el-table-column>
      <el-table-column
              fixed="right"
              header-align="center"
              align="center"
              width="150"
              label="操作">
        <template slot-scope="scope">
          <el-button v-if="isAuth('sys:menu:delete')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button v-if="isAuth('sys:menu:delete')" type="text" size="small" @click="deleteHandle(scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './add-or-update'
import {DeptType} from '@/utils'
  export default {
    data () {
      return {
        dataForm: {},
        dataList: [],
        dataListLoading: false,
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/dept/list'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          this.dataList = data.list
        })
      },
      getFunction (functions) {
        const type = Object.values(DeptType).find(t => t.code === parseInt(functions))
        return type.name
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle (dept) {
        this.$confirm(`确定删除 ${dept.name} ?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl(`/dept/delete/${dept.id}`),
            method: 'post',
            data: this.$http.adornData()
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        }).catch(() => {})
      }
    }
  }
</script>

<template>
  <div class="mod-menu">
    <el-form :inline="true" :model="dataForm">
      <el-form-item>
        <el-input v-model="query.name" class="query-input" placeholder="请输入单位名称"></el-input>
        <dept-select :filter="DeptType.FEED.code" v-model="query.deptId"/>
        <el-button  @click="getDataList">查询</el-button>
        <el-button v-if="isAuth('sys:menu:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
      </el-form-item>
    </el-form>
    <el-table
            :data="dataList"
            v-loading="dataListLoading"
            row-key="id"
            border
            style="width: 100%; ">
      <el-table-column
              prop="name"
              header-align="center"
              width="150"
              align="center"
              label="名称" >
      </el-table-column>
      <el-table-column
              prop="num"
              header-align="center"
              align="center"
              width="150"
              label="流水号">
      </el-table-column>
      <el-table-column
              prop="component"
              header-align="center"
              label="营养成分"
              width="200"
              align="center">
      </el-table-column>
      <el-table-column
              prop="exp"
              header-align="center"
              align="center"
              width="200"
              label="说明">
      </el-table-column>
      <el-table-column
              prop="deptName"
              header-align="center"
              align="center"
              width="200"
              label="饲料厂">
      </el-table-column>
      <el-table-column
              prop="creatorName"
              header-align="center"
              align="center"
              width="120"
              :show-overflow-tooltip="true"
              label="创建人">
      </el-table-column>
      <el-table-column
              prop="modifiederName"
              header-align="center"
              align="center"
              width="120"
              :show-overflow-tooltip="true"
              label="修改人">
      </el-table-column>
      <el-table-column
              fixed="right"
              header-align="center"
              align="center"
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
  import AddOrUpdate from './feed-edit'
  import deptSelect from '@/components/deptSelect'
  import {DeptType} from '@/utils'
export default {
    data () {
      return {
        dataForm: {},
        dataList: [],
        dataListLoading: false,
        addOrUpdateVisible: false,
        DeptType,
        query: {
          deptId: 0,
          name: '',
          identify: 'feed'
        }
      }
    },
    components: {
      AddOrUpdate,deptSelect
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/biz/base/list'),
          method: 'post',
          data: this.$http.adornData({
            ...this.query
          })
        }).then(({data}) => {
          console.log(data.list)
          this.dataList = data.list
          this.dataListLoading = false
        })
      },
      getFunction (functions) {
        const type = options.find(t => t.code === parseInt(functions))
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
            url: this.$http.adornUrl(`/biz/base/delete`),
            method: 'post',
            data: {
              id: dept.id,
              identify: 'dept'
            }
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
<style>
  .dept-select,.query-input{
    margin-right: 12px;
    width: 150px;
  }
  .query-input{
    width: 200px;
  }
</style>

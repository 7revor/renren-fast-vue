<template>
  <div class="mod-menu">
    <el-form :inline="true" :model="dataForm">
      <el-form-item>
        <el-input v-model="query.name" class="query-input" placeholder="请输入姓名"></el-input>
        <dept-select v-model="query.deptId"/>
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
              width="120"
              align="center"
              label="姓名" >
      </el-table-column>
      <el-table-column
              prop="sex"
              header-align="center"
              align="center"
              width="50"
              label="性别">
      </el-table-column>
      <el-table-column
              prop="birthday"
              header-align="center"
              align="center"
              width="120"
              label="出生日期">
      </el-table-column>
      <el-table-column
              prop="sfid"
              header-align="center"
              align="center"
              width="180"
              label="身份证号">
      </el-table-column>
      <el-table-column
              prop="deptName"
              header-align="center"
              align="center"
              width="180"
              label="隶属单位">
      </el-table-column>
      <el-table-column
              prop="code"
              header-align="center"
              align="center"
              width="120"
              label="编号">
      </el-table-column>
      <el-table-column
              prop="jobTime"
              header-align="center"
              align="center"
              width="120"
              label="参加工作时间">
      </el-table-column>
      <el-table-column
              prop="tel"
              header-align="center"
              align="center"
              width="120"
              label="联系电话">
      </el-table-column>
      <el-table-column
              header-align="center"
              align="center"
              width="200"
              label="用户名">
        <template slot-scope="scope">
          {{scope.row.username||'未绑定'}}
        </template>
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
    <staff-edit v-if="addOrUpdateVisible" ref="StaffEdit" @refreshDataList="getDataList"></staff-edit>
  </div>
</template>

<script>
  import StaffEdit from './staff-edit'
  import deptSelect from '@/components/deptSelect'
export default {
    data () {
      return {
        dataForm: {},
        dataList: [],
        dataListLoading: false,
        addOrUpdateVisible: false,
        query: {
          name: '',
          deptId: 0,
          identify: 'staff'
        }
      }
    },
    components: {
      StaffEdit, deptSelect
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
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.StaffEdit.init(id)
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
              identify: 'staff'
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

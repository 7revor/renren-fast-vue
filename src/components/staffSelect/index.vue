<template>
  <el-select class="dept-select" filterable :value="id" @change="changeHandler" placeholder="请选择员工信息">
    <el-option
            v-for="staff in staffList"
            :key="staff.id"
            :label="staff.name"
            :value="staff.id">
    </el-option>
  </el-select>
</template>

<script>
  export default {
    name: 'user-select',
    props: {
      id: {
        required: true
      }
    },
    model: {
      prop: 'id',
      event: 'change'
    },
    data () {
      return {
        staffList: []
      }
    },
    created () {
      this.$http({
        url: this.$http.adornUrl('/biz/base/list'),
        method: 'post',
        data: {
          identify: 'staff',
          deptId: 0
        }
      }).then(({data}) => {
        this.staffList = data.list
      })
    },
    methods: {
      changeHandler (ev) {
        this.$emit('change', ev)
      }
    }
  }
</script>

<style scoped>

</style>

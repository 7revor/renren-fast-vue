<template>
  <el-select class="dept-select" filterable :value="deptId" @change="changeHandler" placeholder="请选择单位">
    <el-option
            v-for="item in deptList"
            :key="item.id"
            :label="item.name"
            :value="item.id">
    </el-option>
  </el-select>
</template>

<script>
  export default {
    name: 'dept-select',
    props: {
      deptId: {
        required: true
      },
      filter: {
        type: Number
      },
      required: {
        type: Boolean
      }
    },
    model: {
      prop: 'deptId',
      event: 'change'
    },
    data () {
      return {
        deptList: []
      }
    },
    created () {
      this.$http({
        url: this.$http.adornUrl('/biz/base/list'),
        method: 'post',
        data: {
          functions: this.filter || 0,
          name: '',
          identify: 'dept'
        }
      }).then(({data}) => {
        !this.required && data.list.unshift({id: 0, name: '全部'})
        this.deptList = data.list
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

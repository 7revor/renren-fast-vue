<template>
  <el-dialog
          :title="!dataForm.id ? '新增' : '修改'"
          :close-on-click-modal="false"
          :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm"
             label-width="80px">
      <el-form-item  label="名称" prop="name">
       <el-input v-model="dataForm.name" placeholder="请输入饲料名称"></el-input>
      </el-form-item>
      <el-form-item  label="流水号" prop="num">
        <el-input v-model="dataForm.num" placeholder="请输入流水号"></el-input>
      </el-form-item>
      <el-form-item  label="营养成分" prop="component">
        <el-input v-model="dataForm.component" placeholder="请输入营养成分"></el-input>
      </el-form-item>
      <el-form-item  label="说明" prop="explain">
        <el-input v-model="dataForm.exp" placeholder="请输入说明"></el-input>
      </el-form-item>
      <el-form-item  label="饲料厂" prop="deptId">
        <dept-select :required="true" :filter="DeptType.FEED.code" v-model="dataForm.deptId"/>
      </el-form-item>
      <el-form-item v-if="dataForm.id" label="创建时间">
       {{dataForm.createTime}}
      </el-form-item>
      <el-form-item v-if="dataForm.modified" label="修改时间">
        {{dataForm.modified}}
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">保存</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import {DeptType} from '@/utils'
  import deptSelect from '@/components/deptSelect'
export default {
  components: {
    deptSelect
  },
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          name: '',
          num: '',
          component: '',
          exp: '',
          deptId: undefined
        },
        DeptType,
        dataRule: {
          name: [
            {required: true, message: '饲料名称不能为空', trigger: 'blur'}
          ],
          num: [
            {required: true, message: '流水号不能为空', trigger: 'blur'}
          ],
          component: [
            {required: true, message: '营养成分不能为空', trigger: 'blur'}
          ],
          deptId: [
            {required: true, message: '饲料厂不能为空', trigger: 'blur'}
          ],
        }
      }
    },
    methods: {
      init (id) {
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
        })
        this.dataForm.id = id
        if (this.dataForm.id === undefined) { // 新增
          this.dataForm = {
            id: 0,
            name: '',
            num: '',
            component: '',
            exp: '',
            deptId: undefined
          }
        } else { // 修改
          this.$http({
            url: this.$http.adornUrl(`/biz/base/select`),
            method: 'post',
            data: {
              id: this.dataForm.id,
              identify: 'feed'
            }
          }).then(res => {
            Object.assign(this.dataForm, res.data)
          })
        }
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/biz/feed/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                ...this.dataForm,
                'id': this.dataForm.id || undefined,
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>

<style lang="scss">
  .mod-menu {
    .menu-list__input,
    .icon-list__input {
      > .el-input__inner {
        cursor: pointer;
      }
    }

    &__icon-popover {
      width: 458px;
      overflow: hidden;
    }

    &__icon-inner {
      width: 478px;
      max-height: 258px;
      overflow-x: hidden;
      overflow-y: auto;
    }

    &__icon-list {
      width: 458px;
      padding: 0;
      margin: -8px 0 0 -8px;

      > .el-button {
        padding: 8px;
        margin: 8px 0 0 8px;

        > span {
          display: inline-block;
          vertical-align: middle;
          width: 18px;
          height: 18px;
          font-size: 18px;
        }
      }
    }

    .icon-list__tips {
      font-size: 18px;
      text-align: center;
      color: #e6a23c;
      cursor: pointer;
    }
  }
</style>

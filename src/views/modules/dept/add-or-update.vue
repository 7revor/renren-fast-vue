<template>
  <el-dialog
          :title="!dataForm.id ? '新增' : '修改'"
          :close-on-click-modal="false"
          :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm"
             label-width="80px">
      <el-form-item  label="名称" prop="name">
       <el-input v-model="dataForm.name" placeholder="请输入单位名称"></el-input>
      </el-form-item>
      <el-form-item  label="英文名" prop="nameEn">
        <el-input v-model="dataForm.nameEn" placeholder="请输入英文名"></el-input>
      </el-form-item>
      <el-form-item  label="编码" prop="code">
        <el-input v-model="dataForm.code" placeholder="请输入编码"></el-input>
      </el-form-item>
      <el-form-item  label="备注" prop="memo">
        <el-input v-model="dataForm.memo" placeholder="请输入备注"></el-input>
      </el-form-item>
      <el-form-item  label="负责人" prop="headMan">
        <el-input v-model="dataForm.headMan" placeholder="请输入负责人"></el-input>
      </el-form-item>
      <el-form-item  label="联系电话" prop="tel">
        <el-input v-model="dataForm.tel" placeholder="请输入联系电话"></el-input>
      </el-form-item>
      <el-form-item  label="部门职能" prop="functions">
        <!--<el-input v-model="dataForm.functions" placeholder="请输入部门职能"></el-input>-->
        <el-select v-model="dataForm.functions" placeholder="部门职能">
          <el-option
                  v-for="item in options"
                  :key="item.code"
                  :label="item.name"
                  :value="item.code">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item  label="联系地址" prop="address">
        <el-input v-model="dataForm.address" placeholder="请输入联系地址"></el-input>
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
const options = Object.values(DeptType)
export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          name: '',
          nameEn: '',
          code: '',
          parentId: 0,
          memo: '',
          headMan: '',
          tel: '',
          functions: 0,
          address: ''
        },
        options,
        dataRule: {
          name: [
            {required: true, message: '单位名称不能为空', trigger: 'blur'}
          ],
          nameEn: [
            {required: true, message: '英文名不能为空', trigger: 'blur'}
          ],
          code: [
            {required: true, message: '单位编码不能为空', trigger: 'blur'}
          ],
          headMan: [
            {required: true, message: '负责人不能为空', trigger: 'blur'}
          ],
          tel: [
            {required: true, message: '联系电话不能为空', trigger: 'blur'}
          ],
          address: [
            {required: true, message: '联系地址不能为空', trigger: 'blur'}
          ],
          functions: [
            {required: true, message: '部门职能不能为空', trigger: 'blur'}
          ]
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
            id: undefined,
            name: '',
            nameEn: '',
            code: '',
            parentId: 0,
            memo: '',
            headMan: '',
            tel: '',
            functions: '',
            address: ''
          }
        } else { // 修改
          this.$http({
            url: this.$http.adornUrl(`/biz/base/select`),
            method: 'post',
            data: {
              id: this.dataForm.id,
              identify: 'dept'
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
              url: this.$http.adornUrl(`/biz/base/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                ...this.dataForm,
                'id': this.dataForm.id || undefined,
                identify: 'dept'
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

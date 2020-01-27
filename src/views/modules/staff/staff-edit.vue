<template>
  <el-dialog
          :title="!dataForm.id ? '新增' : '修改'"
          :close-on-click-modal="false"
          :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="staffForm"
             label-width="150px">
      <el-form-item  label="姓名" prop="name">
       <el-input v-model="dataForm.name" placeholder="请输入姓名"></el-input>
      </el-form-item>
      <el-form-item  label="性别" prop="sex">
        <el-radio v-model="dataForm.sex" label="男">男</el-radio>
        <el-radio v-model="dataForm.sex" label="女">女</el-radio>
      </el-form-item>
      <el-form-item  label="英文名" prop="nameEn">
        <el-input v-model="dataForm.nameEn" placeholder="请输入英文名"></el-input>
      </el-form-item>
      <el-form-item  label="出生年月" prop="birthday">
        <el-date-picker
                v-model="dataForm.birthday"
                type="date"
                :editable="false"
                value-format="yyyy-MM-dd"
                placeholder="请选择出生年月">
        </el-date-picker>
      </el-form-item>
      <el-form-item  label="参加工作时间" prop="jobTime">
        <el-date-picker
                v-model="dataForm.jobTime"
                type="date"
                :editable="false"
                value-format="yyyy-MM-dd"
                placeholder="请选择参加工作时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item  label="民族" prop="nation">
        <el-input v-model="dataForm.nation" placeholder="请输入民族"></el-input>
      </el-form-item>
      <el-form-item  label="编号" prop="code">
        <el-input v-model="dataForm.code" placeholder="请输入编号"></el-input>
      </el-form-item>
      <el-form-item  label="身份证号" prop="sfid">
        <el-input v-model="dataForm.sfid" placeholder="请输入身份证号"></el-input>
      </el-form-item>
      <el-form-item  label="隶属单位" prop="deptId">
        <dept-select required v-model="dataForm.deptId"/>
      </el-form-item>
      <el-form-item  label="职责" prop="functions">
        <el-input v-model="dataForm.functions" placeholder="请输入职责"></el-input>
      </el-form-item>
      <el-form-item  label="联系电话" prop="tel">
        <el-input v-model="dataForm.tel" placeholder="请输入联系电话"></el-input>
      </el-form-item>
      <el-form-item  label="电子邮箱" prop="email">
        <el-input v-model="dataForm.email" placeholder="请输入电子邮箱"></el-input>
      </el-form-item>
      <el-form-item  label="学历" prop="education">
        <el-input v-model="dataForm.education" placeholder="请输入学历"></el-input>
      </el-form-item>
      <el-form-item  label="毕业院校" prop="school">
        <el-input v-model="dataForm.school" placeholder="请输入毕业院校"></el-input>
      </el-form-item>
      <el-form-item  label="专业" prop="major">
        <el-input v-model="dataForm.major" placeholder="请输入专业"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">保存</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import deptSelect from '@/components/deptSelect'

  const staff = {
    id: 0,
    name: '',
    sex: '',
    nameEn: '',
    code: '',
    deptId: undefined,
    sfid: '',
    birthday: '',
    jobTime: '',
    nation: '',
    tel: '',
    functions: '',
    email: '',
    education: '',
    school: '',
    major: '',
    username: undefined
  }
export default {
  components: {
    deptSelect
  },
  data () {
    return {
      visible: false,
      dataForm: {
        ...staff
      },
      dataRule: {
        name: [
            {required: true, message: '姓名不能为空', trigger: 'blur'}
        ],
        sex: [
            {required: true, message: '性别不能为空', trigger: 'blur'}
        ],
        code: [
            {required: true, message: '编码不能为空', trigger: 'blur'}
        ],
        sfid: [
            {required: true, message: '身份证号不能为空', trigger: 'blur'}
        ],
        tel: [
            {required: true, message: '联系电话不能为空', trigger: 'blur'}
        ],
        birthday: [
            {required: true, message: '出生年月不能为空', trigger: 'blur'}
        ],
        jobTime: [
            {required: true, message: '参加工作时间不能为空', trigger: 'blur'}
        ],
        deptId: [
            {required: true, message: '隶属单位不能为空', trigger: 'blur'}
        ]
      }
    }
  },
  methods: {
    init (id) {
      this.visible = true
      this.$nextTick(() => {
        this.$refs['staffForm'].resetFields()
      })
      this.dataForm.id = id
      if (this.dataForm.id === undefined) { // 新增
        this.dataForm = {
          ...staff
        }
      } else { // 修改
        this.$http({
          url: this.$http.adornUrl(`/biz/base/select`),
          method: 'post',
          data: {
            id: this.dataForm.id,
            identify: 'staff'
          }
        }).then(res => {
          Object.assign(this.dataForm, res.data)
        })
      }
    },
      // 表单提交
    dataFormSubmit () {
      this.$refs['staffForm'].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(`/biz/staff/save`),
            method: 'post',
            data: this.$http.adornData({
              ...this.dataForm,
              'id': this.dataForm.id || undefined
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

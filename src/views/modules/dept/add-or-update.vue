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
      <!--<el-form-item :label="dataForm.typeList[dataForm.type] + '名称'" prop="name">-->
      <!--<el-input v-model="dataForm.name" :placeholder="dataForm.typeList[dataForm.type] + '名称'"></el-input>-->
      <!--</el-form-item>-->
      <!--<el-form-item label="上级菜单" prop="parentName">-->
      <!--<el-popover-->
      <!--ref="menuListPopover"-->
      <!--placement="bottom-start"-->
      <!--trigger="click">-->
      <!--<el-tree-->
      <!--:data="menuList"-->
      <!--:props="menuListTreeProps"-->
      <!--node-key="menuId"-->
      <!--ref="menuListTree"-->
      <!--@current-change="menuListTreeCurrentChangeHandle"-->
      <!--:default-expand-all="true"-->
      <!--:highlight-current="true"-->
      <!--:expand-on-click-node="false">-->
      <!--</el-tree>-->
      <!--</el-popover>-->
      <!--<el-input v-model="dataForm.parentName" v-popover:menuListPopover :readonly="true" placeholder="点击选择上级菜单" class="menu-list__input"></el-input>-->
      <!--</el-form-item>-->
      <!--<el-form-item v-if="dataForm.type === 1" label="菜单路由" prop="url">-->
      <!--<el-input v-model="dataForm.url" placeholder="菜单路由"></el-input>-->
      <!--</el-form-item>-->
      <!--<el-form-item v-if="dataForm.type !== 0" label="授权标识" prop="perms">-->
      <!--<el-input v-model="dataForm.perms" placeholder="多个用逗号分隔, 如: user:list,user:create"></el-input>-->
      <!--</el-form-item>-->
      <!--<el-form-item v-if="dataForm.type !== 2" label="排序号" prop="orderNum">-->
      <!--<el-input-number v-model="dataForm.orderNum" controls-position="right" :min="0" label="排序号"></el-input-number>-->
      <!--</el-form-item>-->
      <!--<el-form-item v-if="dataForm.type !== 2" label="菜单图标" prop="icon">-->
      <!--<el-row>-->
      <!--<el-col :span="22">-->
      <!--<el-popover-->
      <!--ref="iconListPopover"-->
      <!--placement="bottom-start"-->
      <!--trigger="click"-->
      <!--popper-class="mod-menu__icon-popover">-->
      <!--<div class="mod-menu__icon-inner">-->
      <!--<div class="mod-menu__icon-list">-->
      <!--<el-button-->
      <!--v-for="(item, index) in iconList"-->
      <!--:key="index"-->
      <!--@click="iconActiveHandle(item)"-->
      <!--:class="{ 'is-active': item === dataForm.icon }">-->
      <!--<icon-svg :name="item"></icon-svg>-->
      <!--</el-button>-->
      <!--</div>-->
      <!--</div>-->
      <!--</el-popover>-->
      <!--<el-input v-model="dataForm.icon" v-popover:iconListPopover :readonly="true" placeholder="菜单图标名称" class="icon-list__input"></el-input>-->
      <!--</el-col>-->
      <!--<el-col :span="2" class="icon-list__tips">-->
      <!--<el-tooltip placement="top" effect="light">-->
      <!--<div slot="content">全站推荐使用SVG Sprite, 详细请参考:<a href="//github.com/daxiongYang/renren-fast-vue/blob/master/src/icons/index.js" target="_blank">icons/index.js</a>描述</div>-->
      <!--<i class="el-icon-warning"></i>-->
      <!--</el-tooltip>-->
      <!--</el-col>-->
      <!--</el-row>-->
      <!--</el-form-item>-->
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">保存</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import {DeptType} from '@/utils';
  const options = Object.values(DeptType);
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
          tel: 0,
          functions: '',
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
            url: this.$http.adornUrl(`/dept/select/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(res => {
            const {dept} = res.data
            Object.assign(this.dataForm, dept)
          })
        }
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/dept/${!this.dataForm.id ? 'save' : 'update'}`),
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

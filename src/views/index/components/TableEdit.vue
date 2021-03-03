<template>
  <el-dialog
    id="tableEdit"
    :title="title"
    :visible.sync="dialogFormVisible"
    width="70%"
    @close="close"
  >
    <el-form
      ref="form"
      :model="form"
      :rules="rules"
      label-width="80px"
      style="margin-bottom: -18px"
    >
      <el-row :gutter="24">
        <!-- 试验参数设置 -->
        <el-divider class="top-divider" content-position="left">
          试验参数设置
        </el-divider>
        <el-col :span="8">
          <el-form-item label="试验参数" prop="title">
            <el-select v-model="form.title" placeholder="请选择试验参数">
              <el-option
                v-for="(item, i) in options"
                :key="i"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="试验项目" prop="author">
            <el-select v-model="form.title" placeholder="请选择试验项目">
              <el-option
                v-for="(item, i) in options"
                :key="i"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="试验人员" prop="author">
            <el-select v-model="form.title" placeholder="请选择试验人员">
              <el-option
                v-for="(item, i) in options"
                :key="i"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-form-item label="样品设置" prop="author">
          <el-input v-model.trim="form.author" autocomplete="off"></el-input>
        </el-form-item>

        <!-- 环境参数 -->
        <el-divider content-position="left">环境参数</el-divider>
        <el-col :span="8">
          <el-form-item label="环境温度" prop="title">
            <el-input v-model.trim="form.author" autocomplete="off">
              <template slot="append">°C</template>
            </el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="环境湿度" prop="title">
            <el-input v-model.trim="form.author" autocomplete="off">
              <template slot="append">%</template>
            </el-input>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
    <!-- 通道校验 -->
    <el-divider content-position="left">通道校正</el-divider>
    <el-table :data="tableData" style="width: 100%">
      <el-table-column type="selection" width="45"></el-table-column>
      <el-table-column label="编号" type="index" width="45"></el-table-column>
      <el-table-column label="通道" prop="author"></el-table-column>
      <el-table-column
        label="最大量程（kPa）"
        prop="author"
        width="120"
      ></el-table-column>
      <el-table-column label="最大量程校准值" prop="author"></el-table-column>
      <el-table-column label="校正1">
        <template>
          <el-button type="text" size="small">校准</el-button>
        </template>
      </el-table-column>
      <el-table-column label="零位校准值" prop="author"></el-table-column>
      <el-table-column label="校正2">
        <template>
          <el-button type="text" size="small">校准</el-button>
        </template>
      </el-table-column>
    </el-table>
    <div slot="footer" class="dialog-footer">
      <el-button @click="close">取 消</el-button>
      <el-button type="primary" @click="save">确 定</el-button>
    </div>
  </el-dialog>
</template>

<script>
  import { doEdit } from '@/api/table'

  export default {
    name: 'TableEdit',
    data() {
      return {
        form: {
          title: '',
          author: '',
        },
        rules: {
          title: [{ required: true, trigger: 'blur', message: '请输入标题' }],
          author: [{ required: true, trigger: 'blur', message: '请输入作者' }],
        },
        title: '',
        dialogFormVisible: false,
        options: [
          { label: '试验一', value: '1' },
          { label: '试验二', value: '2' },
        ],
        tableData: [{ author: 'test' }], // 表格数据
      }
    },
    created() {},
    methods: {
      showEdit(row) {
        if (!row) {
          this.title = '添加'
        } else {
          this.title = '编辑'
          this.form = Object.assign({}, row)
        }
        this.dialogFormVisible = true
      },
      close() {
        this.$refs['form'].resetFields()
        this.form = this.$options.data().form
        this.dialogFormVisible = false
        this.$emit('fetch-data')
      },
      save() {
        this.$refs['form'].validate(async (valid) => {
          if (valid) {
            const { msg } = await doEdit(this.form)
            this.$baseMessage(msg, 'success')
            this.$refs['form'].resetFields()
            this.dialogFormVisible = false
            this.$emit('fetch-data')
            this.form = this.$options.data().form
          } else {
            return false
          }
        })
      },
    },
  }
</script>
<style lang="scss">
  #tableEdit {
    .el-divider--horizontal.top-divider {
      margin-top: 0;
    }

    .el-divider--horizontal {
      margin-top: 40px;
      margin-bottom: 20px;

      .el-divider__text {
        font-size: 12px;
        color: #888888;
      }
    }

    .el-form {
      padding-right: 0;
    }
  }
</style>

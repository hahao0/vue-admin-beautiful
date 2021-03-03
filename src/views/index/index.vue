<template>
  <div class="table-container">
    <el-row :gutter="24" style="padding-bottom: 10px">
      <el-col :span="17">
        <el-button
          size="mini"
          icon="el-icon-plus"
          type="primary"
          @click="handleAdd"
        >
          添加
        </el-button>
        <el-button
          size="mini"
          icon="el-icon-delete"
          type="danger"
          @click="handleDelete"
        >
          删除
        </el-button>
        <el-button size="mini" type="primary" @click="testMessage">
          开始试验
        </el-button>
        <el-button size="mini" type="primary" @click="testMessage">
          试验分析
        </el-button>
        <el-button size="mini" type="primary" @click="testMessage">
          导出试验
        </el-button>
        <el-button size="mini" type="primary" @click="testMessage">
          导入试验
        </el-button>
      </el-col>
      <el-col :span="7" style="line-height: 28px; text-align: right">
        <el-radio v-model="radio" label="1">当天</el-radio>
        <el-radio v-model="radio" label="2">近三天</el-radio>
        <el-radio v-model="radio" label="3">全部</el-radio>
      </el-col>
    </el-row>

    <el-table
      ref="tableSort"
      v-loading="listLoading"
      :data="list"
      :element-loading-text="elementLoadingText"
      :height="height"
      @selection-change="setSelectRows"
      @sort-change="tableSortChange"
    >
      <el-table-column type="selection" width="55"></el-table-column>
      <el-table-column
        label="试验编码"
        width="95"
        type="index"
      ></el-table-column>
      <el-table-column prop="title" label="创建时间"></el-table-column>
      <el-table-column label="实验名称" prop="author"></el-table-column>
      <el-table-column label="测试项目" prop="pageViews"></el-table-column>
      <el-table-column label="样品编号" prop="datetime"></el-table-column>
      <el-table-column label="样品名称" prop="datetime"></el-table-column>
      <el-table-column label="送检单位" prop="datetime"></el-table-column>
      <el-table-column label="试验人员" prop="datetime"></el-table-column>
      <el-table-column label="试验状态" prop="datetime"></el-table-column>
      <el-table-column label="操作">
        <template #default="{ row }">
          <el-button type="text" @click="handleEdit(row)">编辑</el-button>
          <el-button type="text" style="color: red" @click="handleDelete(row)">
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      :background="background"
      :current-page="queryForm.pageNo"
      :layout="layout"
      :page-size="queryForm.pageSize"
      :total="total"
      @current-change="handleCurrentChange"
      @size-change="handleSizeChange"
    ></el-pagination>
    <table-edit ref="edit"></table-edit>
  </div>
</template>

<script>
  import { getList, doDelete } from '@/api/table'
  import TableEdit from './components/TableEdit'
  export default {
    name: 'ComprehensiveTable',
    components: {
      TableEdit,
    },
    filters: {
      statusFilter(status) {
        const statusMap = {
          published: 'success',
          draft: 'gray',
          deleted: 'danger',
        }
        return statusMap[status]
      },
    },
    data() {
      return {
        radio: '1',
        imgShow: true,
        list: [],
        imageList: [],
        listLoading: false,
        layout: 'total, sizes, prev, pager, next, jumper',
        total: 0,
        background: true,
        selectRows: '',
        elementLoadingText: '正在加载...',
        queryForm: {
          pageNo: 1,
          pageSize: 20,
          title: '',
        },
      }
    },
    computed: {
      height() {
        return this.$baseTableHeight()
      },
    },
    created() {
      this.fetchData()
    },
    beforeDestroy() {},
    mounted() {},
    methods: {
      tableSortChange() {
        const imageList = []
        this.$refs.tableSort.tableData.forEach((item, index) => {
          imageList.push(item.img)
        })
        this.imageList = imageList
      },
      setSelectRows(val) {
        this.selectRows = val
      },
      handleAdd() {
        this.$refs['edit'].showEdit()
      },
      handleEdit(row) {
        this.$refs['edit'].showEdit(row)
      },
      handleDelete(row) {
        if (row.id) {
          this.$baseConfirm('你确定要删除当前项吗', null, async () => {
            const { msg } = await doDelete({ ids: row.id })
            this.$baseMessage(msg, 'success')
            this.fetchData()
          })
        } else {
          if (this.selectRows.length > 0) {
            const ids = this.selectRows.map((item) => item.id).join()
            this.$baseConfirm('你确定要删除选中项吗', null, async () => {
              const { msg } = await doDelete({ ids: ids })
              this.$baseMessage(msg, 'success')
              this.fetchData()
            })
          } else {
            this.$baseMessage('未选中任何行', 'error')
            return false
          }
        }
      },
      handleSizeChange(val) {
        this.queryForm.pageSize = val
        this.fetchData()
      },
      handleCurrentChange(val) {
        this.queryForm.pageNo = val
        this.fetchData()
      },
      handleQuery() {
        this.queryForm.pageNo = 1
        this.fetchData()
      },
      async fetchData() {
        this.listLoading = true
        const { data, totalCount } = await getList(this.queryForm)
        this.list = data
        const imageList = []
        data.forEach((item, index) => {
          imageList.push(item.img)
        })
        this.imageList = imageList
        this.total = totalCount
        setTimeout(() => {
          this.listLoading = false
        }, 500)
      },
      testMessage() {
        this.$baseMessage('test1', 'success')
      },
    },
  }
</script>
<style lang="scss">
  .el-radio {
    margin-right: 20px;
  }
</style>

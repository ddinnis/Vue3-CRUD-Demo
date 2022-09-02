<template>
  <div class="container">
    <div class="title">
      <h2>Crud Demo</h2>
    </div>
    <div class="queryInput">
      <el-input
        @input="handleQueryInput"
        v-model="queryInput"
        placeholder="请输入搜索姓名"
        clearable
      />
      <div class="btns">
        <el-button type="primary" plain @click="handleAddRow">添加</el-button>
        <el-button
          type="danger"
          plain
          @click="handleDeMultipleRow"
          v-if="multipleSelection.length > 0"
        >
          删除多行
        </el-button>
      </div>
    </div>
    <div class="table-info">
      <el-table
        border
        ref="multipleTableRef"
        :data="tableData"
        style="width: 100%"
        @selection-change="handleSelectionChange"
      >
        <el-table-column type="selection" width="50" />
        <el-table-column prop="name" label="Name" width="80" />
        <el-table-column prop="email" label="Email" width="150" />
        <el-table-column
          prop="address"
          label="Address"
          width="300"
          show-overflow-tooltip
        />
        <el-table-column
          prop="tag"
          label="Tag"
          width="120"
          :filters="[
            { text: 'Home', value: 'Home' },
            { text: 'Office', value: 'Office' }
          ]"
          :filter-method="filterTag"
          filter-placement="bottom-end"
        >
          <template #default="scope">
            <el-tag
              :type="scope.row.tag === 'Home' ? '' : 'success'"
              disable-transitions
            >
              {{ scope.row.tag }}
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column fixed="right" label="Operations" width="120">
          <template #default="scope">
            <el-button
              link
              type="danger"
              size="small"
              @click.prevent="handleDeleteRow(scope.row)"
            >
              删除
            </el-button>
            <el-button
              link
              type="primary"
              size="small"
              @click.prevent="handleEditRow(scope.row)"
            >
              编辑
            </el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <div class="dialog-form">
      <el-dialog
        v-model="dialogFormVisible"
        :title="dialogFormTitle === 'add' ? '新增' : '编辑'"
        close-on-press-escape
      >
        <el-form :model="form">
          <el-form-item label="请输入姓名" :label-width="formLabelWidth">
            <el-input v-model="form.name" autocomplete="off" />
          </el-form-item>
          <el-form-item label="请输入邮箱" :label-width="formLabelWidth">
            <el-input v-model="form.email" autocomplete="off" type="email" />
          </el-form-item>
          <el-form-item label="请输入详细地址" :label-width="formLabelWidth">
            <el-input v-model="form.address" autocomplete="off" />
          </el-form-item>
          <el-form-item label="请输入标签" :label-width="formLabelWidth">
            <el-select v-model="form.tag" placeholder="请选择标签">
              <el-option label="Home" value="Home" />
              <el-option label="Office" value="Office" />
            </el-select>
          </el-form-item>
        </el-form>
        <template #footer>
          <span class="dialog-footer">
            <el-button @click="dialogFormVisible = false">取消</el-button>
            <el-button type="primary" @click="handleConfirmClick">
              确认
            </el-button>
          </span>
        </template>
      </el-dialog>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
const formLabelWidth = '140px'
const dialogFormVisible = ref(false)
const dialogFormTitle = ref('')
const queryInput = ref('')
const multipleSelection = ref([])

let tableData = ref([
  {
    id: '0',
    name: 'Alice0',
    email: '123@163.com',
    address: 'No. 189, Grove St, Los Angeles',
    tag: 'Home'
  },
  {
    id: '1',
    name: 'Alice1',
    email: '123@163.com',
    address: 'No. 189, Grove St, Los Angeles',
    tag: 'Home'
  },
  {
    id: '2',
    name: 'Alice2',
    email: '123@163.com',
    address: 'No. 189, Grove St, Los Angeles',
    tag: 'Office'
  },
  {
    id: '3',
    name: 'Alice3',
    email: '123@163.com',
    address: 'No. 189, Grove St, Los Angeles',
    tag: 'Home'
  },
  {
    id: '4',
    name: 'Alice4',
    email: '123@163.com',
    address: 'No. 189, Grove St, Los Angeles',
    tag: 'Office'
  }
])

const form = ref({
  id: '',
  name: '',
  email: '',
  address: '',
  tag: ''
})

const tableDataCopy = Object.assign(tableData.value)

const handleQueryInput = val => {
  if (val.length > 0) {
    tableData.value = tableData.value.filter(item => {
      console.log('val----', val)
      return item.name.toLowerCase().match(val)
    })
  } else {
    tableData.value = tableDataCopy
  }
}

const handleSelectionChange = val => {
  multipleSelection.value = []
  val.forEach(item => {
    multipleSelection.value.push(item)
  })
}

const filterTag = (value, row) => {
  return row.tag === value
}

const handleAddRow = () => {
  dialogFormTitle.value = 'add'
  dialogFormVisible.value = true
  form.value = {}
}

const handleConfirmClick = () => {
  if (dialogFormTitle.value === 'add') {
    tableData.value.push({ id: tableData.value.length, ...form.value })
  }
  if (dialogFormTitle.value === 'edit') {
    const index = tableData.value.findIndex(item => {
      return form.value.id === item.id
    })
    tableData[index] = form.value
  }
  dialogFormVisible.value = false
}

const handleDeleteRow = ({ id }) => {
  let index = tableData.value.findIndex(item => {
    return item.id === id
  })
  tableData.value.splice(index, 1)
}

const handleDeMultipleRow = () => {
  multipleSelection.value.forEach(item => {
    handleDeleteRow(item)
    console.log(item)
  })
  multipleSelection.value = []
}

const handleEditRow = row => {
  dialogFormVisible.value = true
  dialogFormTitle.value = 'edit'
  form.value = { ...row }
}
</script>

<style scoped>
.container {
  width: 800px;
  position: absolute;
  left: 0;
  right: 0;
  margin: auto;
  text-align: center;
}

.el-input {
  width: 200px;
}

.queryInput {
  display: flex;
  justify-content: space-between;
  margin: 20px 0;
}

.btns {
  display: flex;
  justify-content: center;
  align-content: center;
}

.el-button--text {
  margin-right: 15px;
}
.el-select {
  width: 300px;
}
.el-input {
  width: 300px;
}
.dialog-footer button:first-child {
  margin-right: 10px;
}
</style>

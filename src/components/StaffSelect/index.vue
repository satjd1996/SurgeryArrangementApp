<template>
<el-dialog ref="dialog" title="选择护士" 
  @open="getStaffList()"
  :fullscreen="false" 
  :show-close="false" 
  :close-on-click-modal="false" 
  :close-on-press-escape="false"
  :visible.sync="isVisible" 
  append-to-body>
  <el-form ref="form" :model="formData" label-width="80px">
    <data-tables
      ref="staffList"
      :table-props="tableProps"
      :data="staffList"
      :checkbox-filter-def="checkboxFilterDef"
      tooltip-effect="dark"
      style="width: 100%"
      @selection-change="handleSelectionChange"
      v-loading.body="listLoading" 
      element-loading-text="Loading">
      <el-table-column
        type="selection"
        width="55"
        :selectable="selectableFilter">
      </el-table-column>
      <el-table-column
        prop="name"
        label="姓名"
        width="120">
      </el-table-column>
      <el-table-column
        prop="age"
        label="年龄"
        width="120"
        sortable="custom">
      </el-table-column>
      <el-table-column
        prop="pregnant"
        label="是否孕期"
        show-overflow-tooltip>
        <template slot-scope="scope">{{ scope.row.pregnant }}</template>
      </el-table-column>
      <el-table-column
        prop="vacation"
        label="是否休假">
        <template slot-scope="scope">{{ scope.row.vacation }}</template>
      </el-table-column>
      <el-table-column
        prop="exp"
        label="资历"
        sortable="custom"
        show-overflow-tooltip>
      </el-table-column>

    </data-tables>
  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="closeDialog">取 消</el-button>
    <el-button type="primary" @click="onSubmit">确 定</el-button>
  </div>
</el-dialog>
</template>

<script>
import { getStaff } from '@/api/staff'

export default {
  name: 'staff-select',
  components: {},
  props: {
    isVisible: false,
    staffFilter: {
      type: Function,
      default: function(staff) {
        return staff.sid
      }
    },
    selectableFilter: {
      type: Function,
      default: function(row, index) { return true }
    }
  },
  methods: {
    closeDialog() {
      this.$emit('dialogClose')
    },
    onSubmit() {
      this.$emit('submit')
    },
    getStaffList() {
      this.listLoading = true
      getStaff().then(response => {
        this.staffList = response.data.items.filter(this.staffFilter)
        this.listLoading = false
      })
    },
    handleSelectionChange(val) {
      console.log(this.formData.selectedStaffIndex)
      this.formData.selectedStaffIndex = val
    }
  },
  data() {
    return {
      listLoading: false,
      staffList: [],
      tableProps: {
        defaultSort: {
          prop: 'exp',
          order: 'descending'
        }
      },
      checkboxFilterDef: {
        def: [
          {
            'code': 'pregnant',
            'name': '过滤孕期'
          },
          {
            'code': 'vacation',
            'name': '过滤休假'
          }
        ],
        filterFunction(el, filter) {
          return !(el[filter.vals[0]] === true)
        }
      },
      formData: {
        selectedStaffIndex: []
      }
    }
  }
}
</script>
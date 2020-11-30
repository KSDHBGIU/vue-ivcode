<template>
  <div class="table-main">
    <el-table id="myTable"
              ref="table"
              stripe
              border
              :data="getData"
              :height="setting.tablePage?'calc(100% - 55px)':'100%'"
              :show-summary="setting.summary"
              :summary-method="getSummaries"
              @selection-change="handleSelectionChange"
              @row-dblclick="dbclick"
              highlight-current-row>
      <template v-for="(item,index) in setting.columnList">
        <tableScope v-if="item.isScope"
                    :item="item"></tableScope>
        <el-table-column v-else
                         :type="item.type"
                         :index="indexMethod"
                         :prop="item.prop"
                         :show-overflow-tooltip="item.overflow"
                         :fixed="item.fixed"
                         :label="item.label"
                         :width="item.width"
                         :sortable="item.sortable"
                         :selectable="checkSelectable"
                         :min-width="item.minWidth"
                         header-align="center"
                         :align="item.align">
          <template slot="header"
                    slot-scope="scope">
            <span v-html="renderheader(item.label)"></span>
          </template>
          <!-- 多级表头 -->
          <template v-if="item.columns">
            <template v-for="(row,num) in item.columns">
              <tableColumn :item="row"></tableColumn>
            </template>
          </template>
        </el-table-column>
      </template>
    </el-table>
    <el-pagination v-if="setting.tablePage"
                   layout="prev, pager, next, total, sizes, jumper"
                   :current-page="setting.tablePage.currentPage"
                   :total="setting.tablePage.total"
                   :page-size="pageSize"
                   @current-change="handleCurrentChange"
                   @size-change="handleSizeChange">
    </el-pagination>
  </div>
</template>

<script>
import { tableMixin } from '../../../mixins/index'
import tableColumn from "./TableColumn.vue";
import tableScope from "./TableScope.vue";

export default {
  components: { tableColumn, tableScope },
  props: {
    setting: {
      type: Object, //传入类型
      required: true //必须传值
    },
    data: {
      type: Array, //传入类型
      required: false //必须传值
    }
  },
  data() {
    return {
      tableData: [],
      currpage: 1,
      pageSize: 10
    }
  },
  mixins: [tableMixin],
  watch: {
    data: function (d) {
      this.tableData = d
    }
  },
  computed: {
    getData() {
      let start = (this.currpage - 1) * this.pageSize
      let end = this.currpage * this.pageSize
      if (!this.setting.autoPage) {
        return this.tableData
      }
      else if (this.setting.onExcel || !this.setting.tablePage) {
        return this.tableData
      } else {
        return this.tableData.slice(start, end)
      }
    }
  },
  methods: {
    initData() {
      if (this.setting.tablePage) {
        this.pageSize = this.setting.tablePage.size
      }
      setTimeout(() => {
        try {
          this.$refs.table.doLayout()
        }
        catch (err) { }
      }, 2000)
    },
    handleCurrentChange(cpage) {
      this.currpage = cpage
      this.setting.currpageFun(cpage, this.pageSize)
    },
    handleSizeChange(val) {
      this.pageSize = val
    },
    indexMethod(index) {
      if (this.setting.tablePage) {
        return (this.currpage - 1) * this.pageSize + index + 1;
      }
      else {
        return index + 1;
      }
    },
    handleSelectionChange(val) {
      if (this.setting.handleSelectionChange) {
        this.setting.handleSelectionChange(val)
      }
    },
    checkSelectable(row) {
      if (this.setting.checkSelectable) {
        return this.setting.checkSelectable(row)
      }
      return true
    },
    dbclick(row, event, column) {
      row['isOK'] = row['isOK'] == undefined ? true : !row.isOK
      this.$refs.table.setCurrentRow()
    },
    getSummaries(param) {
      const { columns, data } = param;
      const sums = [];
      columns.forEach((column, index) => {
        let unit = ''
        if (index === 0) {
          sums[index] = '合计';
          return;
        }
        if (column.property === 'economicLoss') {
          sums[index] = '';
          return;
        }
        else if (column.property === 'peopleDeath') unit = '人'
        else if (column.property === 'agriculture') unit = '亩'
        else if (column.property === 'houseDown') unit = '间'
        else if (column.property === 'treeDown') unit = '棵'
        else if (column.property === 'traffic') unit = '处'
        else if (column.property === 'pongdingStatus') unit = '处'
        else if (column.property === 'parkClosed') unit = '处'

        const values = data.map(item => Number(item[column.property]));
        if (!values.every(value => isNaN(value))) {
          sums[index] = values.reduce((prev, curr) => {
            const value = Number(curr);
            if (!isNaN(value)) {
              return prev + curr;
            } else {
              return prev;
            }
          }, 0);
          sums[index] += ' ' + unit;
        } else {
          sums[index] = '';
        }
      })
      return sums;
    }
  },
  mounted() {
    this.initData()
  }
}
</script>

<style>
.table-main {
  height: 100%;
}
.el-tooltip__popper.is-dark {
  max-width: 500px;
}
.el-table .el-table__fixed {
  bottom: 17px;
  height: auto !important;
}
.el-table .cell,
.el-table th div,
.el-table--border th:first-child .cell,
.el-table--border td:first-child .cell {
  padding-left: 4px;
  padding-right: 4px;
}
.el-table--border,
.el-table--group,
.el-table td,
.el-table th.is-leaf,
.el-table--border th,
.el-table__fixed-right-patch {
  border-color: #dedede;
}
.el-table .el-table__body .cell a {
  cursor: pointer;
}
.el-table--border::after,
.el-table--group::after,
.el-table::before {
  border-color: #dedede;
}
.el-table thead {
  color: #333;
}
.el-table .el-table__header-wrapper th,
.el-table .el-table__header-wrapper tr {
  background: #fafafa;
}
.el-table .cell .el-button {
  padding: 0 10px;
}
.el-pagination {
  margin-top: 10px;
}
</style>

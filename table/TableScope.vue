<template>
  <el-table-column v-if="item.isScope"
                   :fixed="item.fixed"
                   :prop="item.prop"
                   :show-overflow-tooltip="item.overflow"
                   :label="item.label"
                   :width="item.width"
                   :sortable="item.sortable"
                   :min-width="item.minWidth"
                   header-align="center"
                   :align="item.align">
    <!-- 操作 -->
    <template slot-scope="scope">
      <span :class="row.class?row.class:''"
            v-for="(row,num) in item.btnList"
            :key="num">
        <el-button v-if="!row.key || row.value==scope.row[row.key]"
                   type="text"
                   @click="row.clickFun(scope.row)">{{row.label}}</el-button>
      </span>
      <div v-if="item.htmlFun"
           v-html="item.htmlFun(scope.row[item.prop],scope.row)"></div>
      <div v-if="item.isEdit"
           :model="scope.row"
           :class="scope.row.isOK?'table-column-edit':''">
        <el-input v-if="scope.row.isOK"
                  v-model="scope.row[item.prop]"
                  style="width:100%;height:100%;"></el-input>
        <span v-else>{{scope.row[item.prop]}}</span>
      </div>
    </template>
  </el-table-column>
</template>

<script>
import { tableMixin } from '../../../mixins/index'

export default {
  name: "table-column",
  props: {
    item: {
      type: Object, //传入类型
      required: true //必须传值
    }
  },
  mixins: [tableMixin]
};
</script>

<style lang="scss">
#myTable {
  .table-column-edit {
    position: absolute;
    top: 4px;
    left: 0px;
    width: 100%;
    height: 100%;
  }
}
</style>

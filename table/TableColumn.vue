<template>
  <tableScope v-if="item.isScope"
              :item="item"></tableScope>
  <el-table-column v-else
                   :type="item.type"
                   :prop="item.prop"
                   :show-overflow-tooltip="item.overflow"
                   :fixed="item.fixed"
                   :label="item.label"
                   :width="item.width"
                   :sortable="item.sortable"
                   :min-width="item.minWidth"
                   header-align="center"
                   :align="item.align">
    <template slot="header"
              slot-scope="scope">
      <span v-html="renderheader(item.label)"></span>
    </template>
    <template v-if="item.columns">
      <template v-for="row in item.columns">
        <table-column :item="row"></table-column>
      </template>
    </template>
  </el-table-column>
</template>

<script>
import { tableMixin } from '../../../mixins/index'
import tableScope from "./TableScope.vue";

export default {
  name: "table-column",
  components: { tableScope },
  props: {
    item: {
      type: Object, //传入类型
      required: true //必须传值
    }
  },
  mixins: [tableMixin]
};
</script>

<style>
</style>

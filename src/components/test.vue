<template>
  <el-table ref="topicTable" :row-key="getRowKeys" :data="tDt">
    <el-table-column type="expand">expand area</el-table-column>
    <el-table-column label="column1">
      <template slot-scope="scope">
        <el-button @click="handleConnectionSearch(scope.row)">
        </el-button>
      </template>
    </el-table-column>
  </el-table>
</template>

<script>
export default {
  data() {
    return {
      tDt:[{id:1}, {id:2}]
    }
  },
  methods: {
    handleConnectionSearch(row) {
      this.$refs.topicTable.toggleRowExpansion(row, true) // 点击button展开
    },
    getRowKeys(row) {
      return row.id
    }
  }
}
</script>
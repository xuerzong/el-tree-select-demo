<script>
import SingleTreeSelect from './components/SingleTreeSelect.vue'
import { treeData } from './constants/test'

export default {
  components: {
    SingleTreeSelect
  },

  data() {
    return {
      value: 0,
      treeData
    }
  },

  watch: {
    value(v) {
      console.log(v, 'value') 
    }
  },

  methods: {
    handleSelect(value) {
      this.value = value
    },

    handleReset() {
      this.value = 0
    }
  }
}
</script>

<template>
  <div class="container">
    <div class="wrapper">
      <single-tree-select v-model="value" :tree-data="treeData" placeholder="请选择" @select="handleSelect"/>
      <el-button @click="handleReset">重置</el-button>
    </div>
  </div>
</template>

<style>
.container {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
}

.wrapper {
  width: 320px;
}
</style>

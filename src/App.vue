<script>
export default {
  data() {
    return {
      options: [{
        value: '选项1',
        label: '黄金糕'
      }, {
        value: '选项2',
        label: '双皮奶'
      }, {
        value: '选项3',
        label: '蚵仔煎'
      }, {
        value: '选项4',
        label: '龙须面'
      }, {
        value: '选项5',
        label: '北京烤鸭'
      }],
      value: '',
      popoverVisible: false
    }
  },

  watch: {
    popoverVisible(newVal) {
      if(newVal) {
        console.log(this.$refs['tree-select'])
      }
    }
  },

  computed: {
    treeSelectClassName () {
      return this.popoverVisible ? 'tree-select' : 'tree-select tree-select'
    }
  },

  methods: {
    handleSelectOnFocus() {
      this.popoverVisible = true
    },

    handleSelectOnBlur() {
      this.popoverVisible = false
    },

    handle2FocusSelect() {
      const treeSelect = this.$refs['tree-select']
      treeSelect.focus()
    }
  },
}
</script>

<template>
  <div class="tree-select">
    <el-input
      mutiple
      ref="tree-select"
      v-popover:tree-select-popover
      v-model="value"
      :class="{'is-focus': popoverVisible}"
      @focus="handleSelectOnFocus"
    />
    <el-popover
      ref="tree-select-popover"
      placement="bottom"
      title="标题"
      width="100%"
      trigger="manual"
      content="这是一段内容,这是一段内容,这是一段内容,这是一段内容。"
      transition="el-zoom-in-top"
      v-model="popoverVisible"
      @click="handle2FocusSelect"
    >
    </el-popover>
  </div>
</template>


<style>
.tree-select .select-popper {
  display: none;
}

.el-input.is-focus .el-input__inner {
  border-color: #409EFF;
  outline: 0;
}
</style>

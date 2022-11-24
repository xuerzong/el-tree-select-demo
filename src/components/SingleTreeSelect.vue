<script type="text/babel">
import { valueEquals } from 'element-ui/src/utils/util'

export default {
  name: "SingleTreeSelect",

  props: {
    value: {
      required: true
    },
    treeData: {
      type: Array,
      default: () => []
    },
    placeholder: {
      type: String,
      required: false
    },
  },  

  data() {
    return {
      selectedValue: {}, // { label: string, value: any }
      width: 0,
      popoverVisible: false,
    }
  }, 

  watch: {
    value(v) {
      if(!valueEquals(v, this.selectedValue.id)) {
        this.setSelected()
      }
    }
  },

  computed: {
    inputSuffixClass() {
      return this.popoverVisible ? 'arrow-up is-reverse' : 'arrow-up'
    }
  },

  mounted() {
    if(typeof this.value !== 'undefined') {
      this.setSelected()
    }

    // 获取容器宽度 使弹窗宽度100%
    this.$nextTick(() => { 
      this.width = this.$refs.wrapper.offsetWidth || 0
    })
  },

  methods: {
    /**
     * 展开下拉框
     */
    handlePopoverShow () {
      this.popoverVisible = true
    },

    /**
     * 收起下来框
     */
    handlePopoverClose() {
      this.popoverVisible =false
    },

    /**
     * 选择节点
     * @param {{ id: number, label: string }} value 
     */
    handleSelectNode (value) {
      if(!value) return
      this.selectedValue = value
      this.emitSelect(value.id)
      this.handlePopoverClose()
    },

    handleInputFocus () {
      this.$refs['input'].focus()
    },

    /**
     * 向父节点传递值
     * @param {number} value 
     */
    emitSelect (value) {
      if(!valueEquals(this.value, value)) {
        this.$emit('select', value)
      }
    },

    setSelected() {
      const _opt = this.getFullOptionsByValueFromArray(this.treeData)
      this.selectedValue = _opt || {}
    },


    getFullOptionsByValueFromArray (array = []) {
      for(let i = 0, len = array.length; i < len - 1; i ++) {
        const current = array[i]
        if(current.id === this.value) {
          return current
        }

        if(Array.isArray(current.children)) {
          const result = this.getFullOptionsByValueFromArray(current.children)
          if(result) return result
        }
      }

      return null
    }
  },
}
</script>

<template>
  <div ref="wrapper" class="tree-select">
    <el-input
      mutiple
      ref="input"
      v-popover:tree-select-popover
      v-model="selectedValue.label"
      readonly
      :class="{'is-focus': popoverVisible}"
      :placeholder="placeholder"
      @focus="handleInputFocus"
      @keydown.native.esc.stop.prevent="handlePopoverClose"
      @keydown.native.tab="handlePopoverClose"
    >
      <template slot="suffix">
        <i :class="['el-select__caret', 'el-input__icon', 'el-icon-' + inputSuffixClass]"/>
      </template>
    </el-input>
    <el-popover
      ref="tree-select-popover"
      class="tree-select-popover"
      placement="bottom"
      :width="width"
      :append-to-body="false"
      trigger="focus"
      transition="el-zoom-in-top"
      v-model="popoverVisible"
    >
      <el-tree
        ref="tree"
        :data="treeData"
        node-key="id"
        :expand-on-click-node="false"
        @node-click="handleSelectNode"
        @node-expand="handleInputFocus"
        @node-collapse="handleInputFocus"
      >
    </el-tree>
    </el-popover>
  </div>
</template>


<style>
.tree-select .el-input .el-select__caret {
  transform: rotateZ(180deg);
}

.tree-select .el-input .el-select__caret.is-reverse {
  transform: rotateZ(0);
}

.tree-select-popover .el-popover {
  transition-delay: .05s !important; /** 延迟动画效果，避免展开或收起树节点时`弹窗`有短暂的收缩动画 */
}
</style>



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

  computed: {
    treeSelectClassName () {
      return this.popoverVisible ? 'tree-select' : 'tree-select tree-select'
    },

    inputSuffixClass() {
      return this.popoverVisible ? 'arrow-up is-reverse' : 'arrow-up'
    }
  },

  mounted() {
    if(typeof this.value !== 'undefined') {
      const _opt = this.getFullOptionsByValueFromArray(this.treeData, this.value)
      if(_opt) this.selectedValue = _opt
    }

    this.$nextTick(() => { 
      this.width = this.$refs.wrapper.offsetWidth || 0
    })
  },

  methods: {
    handlePopoverShow () {
      this.popoverVisible = true
    },

    handlePopoverClose() {
      this.popoverVisible =false
    },

    handleSelectNode (value) {
      if(!value) return
      this.selectedValue = value
      this.emitSelect(value.value)
      this.handlePopoverClose()
    },

    handleInputFocus () {
      this.$refs['input'].focus()
    },

    emitSelect (value) {
      if(!valueEquals(this.value, value)) {
        this.$emit('select', value)
      }
    },

    getFullOptionsByValueFromArray (array = [], value) {
      for(let i = 0, len = array.length; i < len - 1; i ++) {
        if(array[i].value === value) {
          return array[i]
        }

        if(Array.isArray(array[i].children)) {
          const result = this.getFullOptionsByValueFromArray(array[i].children, value)
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
  transition-delay: .05s !important;
}
</style>



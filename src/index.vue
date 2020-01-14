<template>
  <div :class="'sc-new-tree'">
    <TreeNodeList ref="TreeNodeList" :data="data" v-if="data.some(child => child.$keep)"></TreeNodeList>
    <div v-else>无数据</div>
  </div>
</template>

<script>
import TreeNode from './TreeNode.vue'
import TreeNodeList from './TreeNodeList.vue'
import Vue from 'vue'
Vue.component('TreeNode', TreeNode)
Vue.component('TreeNodeList', TreeNodeList)

export default {
  name: 'Tree',
  props: {
    data: {
      default () {
        return []
      },
      type: Array
    },
    selected: {
      default () {
        return []
      },
      type: Array
    },
    keyword: {
      type: String,
      default: ''
    }
  },
  watch: {
    selected () {
      this.init()
      this.$refs.TreeNodeList && this.$refs.TreeNodeList.setSonNode()
    },
    keyword () {
      this.init()
      this.$forceUpdate()
      this.$refs.TreeNodeList && this.$refs.TreeNodeList.setSonNode()
    }
  },
  created () {
    this.$tree = this
    this.init()
  },
  methods: {
    init () {
      // selectedMap
      this.selectedMap = Object.create(null)
      this.selected.forEach(selectedId => { this.selectedMap[selectedId] = true })

      const collectSonNodeFlag = (dataList, parent) => {
        dataList.forEach(data => {
          data.$parent = parent

          if (data.subTreeNodes) {
            collectSonNodeFlag(data.subTreeNodes, data)
            data.$keep = data.name.includes(this.keyword) || !!(data.subTreeNodes.filter(child => child.$keep)).length
            if (data.$keep) {
              let $checked = 0
              let $total = 0
              for (let i = 0; i < data.subTreeNodes.length; i++) {
                $checked += data.subTreeNodes[i].$checked
                $total += data.subTreeNodes[i].$total
              }
              data.$checked = $checked
              data.$total = $total
            } else {
              data.$checked = 0
              data.$total = 0
            }
          } else {
            data.$keep = data.name.includes(this.keyword)

            // if(data.nodeType === 0) {

            // } else {
            //   data.$keep = false
            // }
            if (data.$keep) {
              data.$checked = this.selectedMap[data.id] ? 1 : 0
              data.$total = 1
            } else {
              data.$checked = 0
              data.$total = 0
            }
          }
        })
      }
      collectSonNodeFlag(this.data, null)
    }
  }
}
</script>

<template>
  <div class="p20">
    <template v-for="(node, index) in filterData">
      <TreeNode ref="TreeNode" v-if="index < (page + 1) * size && node.$keep" :key="index" :data="node"></TreeNode>
    </template>
    <div v-if="filterData.length > (page + 1) * size" @click="page++" style="paddingLeft: 20px">显示更多数据</div>
  </div>
</template>

<script>
export default {
  name: 'TreeNodeList',
  props: {'data': {}},
  computed: {
    filterData () {
      return this.data.filter(child => child.$keep)
    }
  },
  data () {
    return { page: 0 }
  },
  created () {
    this.$tree = this.$parent.$tree

    this.size = 1000
  },
  methods: {
    setSonNode () {
      this.$refs.TreeNode && this.$refs.TreeNode.forEach(vm => {
        vm.setSonNode()
      })
    },
    setParentNode () {
      this.$parent.setParentNode && this.$parent.setParentNode()
    }
  }
}
</script>

<style scoped>
.p20 {
  padding-left: 20px;
}
</style>

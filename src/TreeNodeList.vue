<template>
  <div class="p20">
    <template v-for="(node, index) in data">
      <TreeNode ref="TreeNode" v-if="index < (page + 1) * size && node.$keep" :key="index" :data="node"></TreeNode>
    </template>
    <div v-if="data.length > (page + 1) * size" @click="page++" style="paddingLeft: 20px">显示更多数据</div>
  </div>
</template>

<script>
export default {
  name: "TreeNodeList",
  props: ["data"],
  data() {
    return { page: 0 };
  },
  created() {
    this.$tree = this.$parent.$tree;

    this.size = 20;
  },
  methods: {
    setSonNode() {
      this.$refs.TreeNode.forEach(vm => {
        vm.setSonNode();
      });
    },
    setParentNode() {
      this.$parent.setParentNode && this.$parent.setParentNode();
    },
  },
};
</script>

<style scoped>
.p20 {
  padding-left: 20px;
}
</style>

<template>
  <div :class="'sc-new-tree'">
    <TreeNodeList ref="TreeNodeList" :data="data"></TreeNodeList>
  </div>
</template>

<script>
import TreeNode from "./TreeNode.vue";
import TreeNodeList from "./TreeNodeList.vue";
import Vue from "vue";
Vue.component("TreeNode", TreeNode);
Vue.component("TreeNodeList", TreeNodeList);

export default {
  name: "Tree",
  props: {
    data: Array,
    selected: {
      default() {
        return [];
      },
      type: Array,
    },
  },
  watch: {
    selected() {
      this.init();
      this.$refs.TreeNodeList.setSonNode();
    },
  },
  created() {
    this.$tree = this;
    this.init();
  },
  methods: {
    init() {
      this.selectedMap = Object.create(null);
      this.selected.reduce((map, selectedId) => {
        map[selectedId] = true;
        return map;
      }, this.selectedMap);

      const collectSonNodeFlag = (dataList, parent, flag) => {
        dataList.forEach(data => {
          data.$parent = parent;

          if (data.subTreeNodes) {
            collectSonNodeFlag(data.subTreeNodes, data, flag);
            let $checked = 0,
              $total = 0;
            for (let i = 0; i < data.subTreeNodes.length; i++) {
              $checked += data.subTreeNodes[i].$checked;
              $total += data.subTreeNodes[i].$total;
            }
            data.$checked = $checked;
            data.$total = $total;

            data.$keep = true;
          } else {
            data.$checked = this.selectedMap[data.id] ? 1 : 0;
            data.$total = 1;

            data.$keep = true;
          }
        });
      };
      collectSonNodeFlag(this.data, null);
    },
    filter(keyword = "") {
      // todo
      this.filterMap = Object.create(null);

      const collectSonNodeFlag = (dataList, parent) => {
        dataList.forEach(data => {
          data.$parent = parent;

          if (data.subTreeNodes) {
            collectSonNodeFlag(data.subTreeNodes, data);
            data.$keep = data.subTreeNodes.some(child => child.$keep);
          } else {
            data.$keep = data.name.includes(keyword);
          }
        });
      };
      collectSonNodeFlag(this.data, null);
      this.$refs.TreeNodeList.setSonNode();
    },
  },
};
</script>

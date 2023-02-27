<template>
  <table class="tbl-basic fixed">
    <colgroup>
      <DraggableTreeTableColumn
        v-for="column in columns"
        :key="column.prop"
        :width="column.width"
        :align="column.align"
      ></DraggableTreeTableColumn>
    </colgroup>
    <tbody v-if="(!Array.isArray(list) || list.length === 0)">
      <tr>
        <td :colspan="emptyTextColspan" class="emptyText">{{ emptyText }}</td>
      </tr>
    </tbody>
    <tbody v-else :class="dragAreaClass">
      <DraggableTreeTableRow
        v-for="(item, rowIndex) in list"
        :key="item[rowKey]"
        :tableComponent="tableComponent"
        :rowKey="rowKey"
        :data="item"
        :columns="columns"
        :index="rowIndex"
        :isShow="isShowList[rowIndex]"
        :isExpand="isExpandList[rowIndex]"
        :isDraggable="isDraggable"
        :getRowPath="getRowPath"
        :childrenInfo="childrenInfoList[rowIndex]"
        :toggleExpand="toggleExpand"
      />
    </tbody>
  </table>
</template>

<script>
import DraggableTreeTableColumn from './DraggableTreeTableColumn';
import DraggableTreeTableRow from './DraggableTreeTableRow';

export default {
  name: 'DraggableTreeTableBody',
  props: {
    tableComponent: Object,
  
    columns: Array,
    list: Array,
    childrenInfoList: Array,
    emptyTextColspan: Number,
    emptyText: String,
    rowKey: String,
    depth: Number,
    isDraggable: Function,
    getRowPath: Function,

    // eventHandler
    handleStart: Function,
    handleChoose: Function,
    handleEnd: Function,
  },
  components: {
    DraggableTreeTableColumn,
    DraggableTreeTableRow,
  },
  data() {
    return {
      isShowList: this.list.map(() => true),
      isExpandList: this.list.map(() => true),
    };
  },
  watch: {
    list(nextValue) {
      this.isShowList = nextValue.map(() => true);
      this.isExpandList = nextValue.map(() => true);
    },
  },
  computed: {
    dragAreaClass() {
      return {
        dragArea: true,
        isDragging: this.isDragging,
      };
    },
  },
  methods: {
    toggleExpand(index, value) {
      const nextExpand = (value !== undefined ? value : !this.isExpandList[index]) ;

      // this.isExpandList[index] = nextExpand;
      this.isExpandList = [
        ...this.isExpandList.slice(0, index),
        nextExpand,
        ...this.isExpandList.slice(index + 1),
      ];

      const arr = [index];
      while(arr.length > 0) {
        const curr = arr.splice(0, 1)[0];
        this.childrenInfoList[curr].forEach(item => arr.push(item.index));

        if (curr !== index) {
          this.isShowList[curr] = nextExpand;
        }
      }
    },
  },
};
</script>

<style scoped>
.emptyText {
  text-align: center;
  color: #909399;
}

.childrenRow:not(.sortable-ghost) .dragArea.isDragging:not(:has(tr)) {
  height: 25px;
}

.draggableItem {
}
/* 
.draggableRow:hover {
  background-color: #f5f7fa;
} */

.sortable-ghost {
  padding-left: 10px;
  /* height: 1px; */

}

.sortable-ghost > td {
  background-color: #409eff;
}
</style>

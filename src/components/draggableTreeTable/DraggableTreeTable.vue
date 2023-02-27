<template>
  <div class="draggableTable" :style="outerStyle">
    <table class="tbl-basic fixed draggableTableHead">
      <colgroup>
        <DraggableTreeTableColumn
          v-for="column in columns"
          :key="column.prop"
          :width="column.width"
          :align="column.align"
        ></DraggableTreeTableColumn>
      </colgroup>
      <thead>
        <tr>
          <th v-for="column in columns" :key="column.prop">
            {{ column.label }}
          </th>
        </tr>
      </thead>
    </table>
    <div class="draggableTableBody">
      <DraggableTreeTableBody
        :rowKey="rowKey"
        :columns="columns"
        :list="rows"
        :emptyTextColspan="emptyTextColspan"
        :emptyText="emptyText"
        :isDraggable="isDraggable"
        :getRowPath="getRowPath"
        :tableComponent="tableComponent"
      >
      </DraggableTreeTableBody>
    </div>
  </div>
</template>

<script>
import DraggableTreeTableColumn from './DraggableTreeTableColumn';
import DraggableTreeTableBody from './DraggableTreeTableBody';

export default {
  name: 'DraggableTreeTable',
  props: {
    // 행 데이터
    rows: {
      type: Array,
      default() {
        return [];
      },
    },
    columns: {
      types: Array,
      default() {
        return [];
      },
    },
    emptyText: String,
    rowKey: {
      type: String,
      default: 'id',
    },
    outerStyle: Object,
    isDraggable: {
      type: Function,
      default() {
        return () => true;
      },
    },
    getRowPath: {
      type: Function,
      default() {
        return () => [];
      },
    },
  },
  components: {
    DraggableTreeTableColumn,
    DraggableTreeTableBody,
  },
  data() {
    return {
      fromIndex: -1,
      toIndex: -1,
    };
  },
  computed: {
    tableComponent() {
      return this;
    },
    emptyTextColspan() {
      if (!this.$slots.colgroup || this.$slots.colgroup.length === 0) {
        return 1;
      }

      return this.$slots.colgroup[0].children.length;
    },
  },
  mounted() {
    this.$on('row:dragStart', this.handleStart);
    // this.$on('row:drag', this.handleStart);
    // this.$on('row:dragEnter', this.handleStart);
    this.$on('row:dragOver', this.handleOver);
    this.$on('row:drop', this.handleDrop);
    // this.$on('row:dragLeave', this.handleStart);
    this.$on('row:dragEnd', this.handleEnd);
  },
  methods: {
    handleStart({ fromIndex }) {
      console.log(`handleStart : from : ${fromIndex}`);
      this.fromIndex = fromIndex;
    },
    handleOver({ toIndex }) {
      console.log(`handleOver : from : ${this.fromIndex}, to : ${toIndex}`);
      this.toIndex = toIndex;
    },
    handleDrop() {
      this.$emit('rowDrop', { fromIndex: this.fromIndex, toIndex: this.toIndex});
    },
    handleEnd() {
      this.$emit('rowDragEnd', { fromIndex: this.fromIndex, toIndex: this.toIndex});
      this.fromIndex = -1;
      this.toIndex = -1;
    },
    
  },
};
</script>

<style scoped>
.draggableTableBody {
  overflow-y: auto;
}
</style>

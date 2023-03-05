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
        :childrenInfoList="childrenInfoList"
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
    emptyText: {
      type: String,
      default: '데이터가 없습니다',
    },
    rowKey: {
      type: String,
      default: 'id',
    },
    outerStyle: {
      type: Object,
      default() {
        return {};
      },
    },
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

    childrenInfoList() {
      const rowPathArr = this.rows.map((row) => this.getRowPath(row));

      const arr = rowPathArr.map((rowPath, rowPathIndex) => {
        const result = [];

        rowPathArr.forEach((targetRowPath, targetIndex) => {
          if (rowPathIndex !== targetIndex && this.validateParent(rowPath, targetRowPath)) {
            result.push({
              index: targetIndex,
              children: this.rows[targetIndex],
            });
          }
        });

        return result;
      })

      return arr;
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
    /**
     * 두 rowPath를 비교하여 부모-자식 관계인지 체크
     * @param {Array} parentRowPath 
     * @param {Array} rowPath 
     * @param {Boolean} isAdjcent 인접여부
     */
    validateParent(parentRowPath, rowPath, isAdjacent = true) {
      const lengthCheck = isAdjacent ? parentRowPath.length + 1 === rowPath.length : parentRowPath.length < rowPath.length;
      return lengthCheck && parentRowPath.every((node, nodeIndex) => node === rowPath[nodeIndex]);
    },
    handleStart({ fromIndex }) {
      console.log(`handleStart : from : ${fromIndex}`);
      this.fromIndex = fromIndex;
    },
    handleOver({ toIndex }) {
      console.log(`handleOver : from : ${this.fromIndex}, to : ${toIndex}`);
      this.toIndex = toIndex;
    },
    handleDrop({ asChildren }) {
      console.log(`handleDrop ${this.fromIndex} -> ${this.toIndex}, asChildren: ${asChildren}`);

      if (this.fromIndex === this.toIndex) {
        return;
      }

      const [parentRowPath, rowPath] = [
        this.getRowPath(this.rows[this.fromIndex]),
        this.getRowPath(this.rows[this.toIndex]),
      ];

      if (this.validateParent(parentRowPath, rowPath, false)) {
        return;
      }

      this.$emit('rowDrop', { fromIndex: this.fromIndex, toIndex: this.toIndex, asChildren});
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

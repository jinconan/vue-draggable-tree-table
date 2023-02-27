<template>
  <tr v-show="isShow" :class="rowClass" :draggable="draggable"
    @dragstart.capture="onDragstart"
    @drag="onDrag"
    @dragend="onDragend"
  >
    <td
        class="draggableRowCell"
        v-for="(column, colIndex) in columns"
        :key="column.prop"
        :style="{
          paddingLeft:
            colIndex === 0 ? depth * 16 + 10 + 'px' : undefined,
          textAlign: 'left',
        }"
        @dragenter="onDragenter"
        @dragover.prevent="onDragover"
        @drop.prevent="onDrop"
        @dragleave.prevent="onDragleave"
      >
        <i
          v-if="colIndex === 0 && draggable"
          class="el-icon-s-grid handle"
          style="cursor: pointer"
        ></i>
        <i
          v-if="colIndex === 0 && hasChildren"
          :class="iconClass"
          @click="toggleExpand(index)"
          style="cursor: pointer"
        ></i>
        <div class="cellContainer"

        >{{ data[column.prop] }}</div>
    </td>
  </tr>
</template>
<script>

export default {
  name: 'DraggableTreeTableRow',
  components: {
  },
  props: {
    tableComponent: Object,
    // 행 데이터
    data: {
      type: Object,
      default() {
        return {};
      },
    },
    columns: Array,
    childrenInfo: Array,
    isShow: Boolean,
    isExpand: Boolean,
    // draggable 여부 체크 콜백
    isDraggable: Function,
    getRowPath: Function,
    index: Number,
    rowKey: String,
    toggleExpand: Function,
  },
  data() {
    return {
      dragging: false,
      hoverRowUpper: false,
      hoverRowLower: false,
      hoverCell: false,
    };
  },
  computed: {
    draggable() {
      const { data, index } = this.$props;
      return this.isDraggable({ data, index });
    },
    hasChildren() {
      return this.childrenInfo.length > 0;
    },
    rowClass() {
      return {
        draggableRow: true,
        dragging: this.dragging,
        activeDropRowUpper: this.hoverRowUpper,
        activeDropRowLower: this.hoverRowLower,
        activeDropCell: this.hoverCell,
      }
    },
    depth() {
      return this.getRowPath(this.data).length;
    },
    iconClass() {
      return {
        'el-icon-arrow-right': !this.isExpand,
        'el-icon-arrow-down': this.isExpand,
      };
    },
  },
  methods: {
    onDragstart(event) {
      const { target, currentTarget } = event;
      this.dragging = true;
      console.log(event);
      this.tableComponent.$emit('row:dragStart', { fromIndex: this.index });
    },
    onDrag(event) {
      this.tableComponent.$emit('row:drag');
    },
    onDragend(event) {
      this.dragging = false;
      this.tableComponent.$emit('row:dragEnd');
    },

    getToIndex(event) {
      const { toElement, offsetY } = event;
      let toIndex;
      if (toElement.classList.contains('cellContainer') || toElement.classList.contains('handle')) {
        this.hoverCell = true;
        toIndex = this.index;
      } else {

        if (offsetY < toElement.clientHeight / 2) {
          this.hoverRowUpper = true;
          this.hoverRowLower = false;
          toIndex = this.index;
        } else {
          this.hoverRowLower = true;
          this.hoverRowUpper = false;
          
          if (this.tableComponent.fromIndex !== this.index) {
            toIndex = this.index + 1;
          } else {
            toIndex = this.index;
          }
        }

      }

      return toIndex;
    },

    onDragenter(event) {
      this.tableComponent.$emit('row:dragEnter', { toIndex: this.getToIndex(event) });

    },
    onDragover(event) {
      this.tableComponent.$emit('row:dragOver', { toIndex: this.getToIndex(event) });
    },
    onDrop(event) {
      console.log('onDrop', event);
      const asChildren = this.hoverCell;
      this.hoverCell = false;
      this.hoverRowUpper = false;
      this.hoverRowLower = false;
      

      this.tableComponent.$emit('row:drop', { asChildren });
    },
    onDragleave(event) {
      const { toElement } = event;

      if (toElement.classList.contains('cellContainer') || toElement.classList.contains('handle')) {
        this.hoverCell = false;
      } else {
        this.hoverRowUpper = false;
        this.hoverRowLower = false;
      }

      this.tableComponent.$emit('row:dragLeave');
    },

  }
};
</script>

<style lang="css" scoped>
.draggableRowCell {
  padding: 12px 10px;
}
.draggableRow {

}

.cellContainer {
  display: inline-block;
}


.draggableRow.dragging {
  /* background-color: #409eff; */
}

.draggableRow.activeDropRowUpper {
  border-top: 5px solid #409eff;
}

.draggableRow.activeDropRowLower {
  border-bottom: 5px solid #409eff;
}

.draggableRow.activeDropCell {
  background-color: #409eff;
}
</style>

<template>
  <tr v-show="isShow" :class="rowClass" :draggable="draggable"
    @dragstart="onDragstart"
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
        @dragenter.prevent="onDragenter"
        @dragover="onDragover"
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
      hoverRow: false,
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
        activeDropRow: this.hoverRow,
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
      console.log('onDragstart', event);
      this.dragging = true;
      this.tableComponent.$emit('rowDragStart', { fromIndex: this.index });
    },
    onDrag() {
      this.tableComponent.$emit('rowDrag');
    },
    onDragend(event) {
      console.log('onDragend', event);
      this.dragging = false;
      this.tableComponent.$emit('rowDragEnd');
    },


    onDragenter(event) {
      const { toElement } = event;
      if (toElement.classList.contains('cellContainer')) {
        this.hoverCell = true;
      } else {
        this.hoverRow = true;
      }


      console.log('onDragenter', event);
      
      this.tableComponent.$emit('rowDragEnter', { toIndex: this.index });
    },
    onDragover(event) {
      const { srcElement, toElement } = event;

      if (srcElement.closest('tr') === toElement.closest('tr')) {
        event.preventDefault();
        return;
      }

      if (toElement.classList.contains('cellContainer')) {
        this.hoverCell = true;
      } else {
        this.hoverRow = true;
      }

      this.tableComponent.$emit('rowDragOver');
    },
    onDrop(event) {
      console.log(event);

      this.tableComponent.$emit('rowDrop');
    },
    onDragleave(event) {
      const { toElement } = event;

      if (toElement.classList.contains('cellContainer')) {
        this.hoverCell = false;
      } else {
        this.hoverRow = false;
      }

      console.log('onDragleave', event);
      this.tableComponent.$emit('rowDragLeave');
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

.draggableRow.activeDropRow {
  border-bottom: 5px solid #409eff;
}

.draggableRow.activeDropCell {
  background-color: #409eff;
}
</style>

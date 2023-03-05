<template>
  <div id="app">
    <DraggableTreeTableVue
      :columns="tableColumns"
      :rows="flattenData"
      rowKey="groupName"
      :getRowPath="getRowPath"
      :isDraggable="isDraggable"
      @rowDrop="handleRowDrop"
    >
    </DraggableTreeTableVue>
  </div>
</template>

<script>
import DraggableTreeTableVue from './components/draggableTreeTable/DraggableTreeTable.vue';

export default {
  name: 'App',
  data() {
    return {
      tableColumns: [
        { prop: 'groupName', label: '지역', width: 200 },
      ],
      flattenData: [
        { 
          groupName: '서울',
          groupKey: '0',
        },
        { 
          groupName: '구로구',
          groupKey: '0-0',
        },
        { 
          groupName: '개봉1동',
          groupKey: '0-0-0',
        },
        { 
          groupName: '개봉2동',
          groupKey: '0-0-1',
        },
        { 
          groupName: '개봉3동',
          groupKey: '0-0-2',
        },
        { 
          groupName: '양천구',
          groupKey: '0-1',
        },
      ],
    };

  },
  components: {
    DraggableTreeTableVue,
  },
  methods: {
    getRowPath(row) {
      return row.groupKey.split('-');
    },
    isDraggable({ data }) {
      return this.getRowPath(data).length > 1;
    },
    handleRowDrop({ fromIndex, toIndex }) {
      this.$alert((`${fromIndex} -> ${toIndex}`));
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

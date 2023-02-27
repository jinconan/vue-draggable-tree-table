<template>
  <div id="app">
    <DraggableTreeTableVue
      :columns="tableColumns"
      :rows="flattenData"
      emptyText="데이터가 없습니다"
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
        { prop: 'groupName', label: '그룹명', width: 200 },
        { prop: 'groupLevel', label: '그룹레벨', width: 100 },
      ],
      flattenData: [
        { 
          groupName: 'PARKINGCLOUD',
          groupLevel: '001',
          groupKey: '0',
        },
        { 
          groupName: '대표이사',
              groupLevel: '002',
          groupKey: '0-0',
        },
        { 
          groupName: '영업본부', groupLevel: '003',
          groupKey: '0-0-0',
        },
        { 
          groupName: '지원본부', groupLevel: '003',
          groupKey: '0-0-1',
        },
        { 
          groupName: '부사장', groupLevel: '002',
          groupKey: '0-1',
        },
        { groupName: '기타', groupLevel: '002', groupKey: '0-2' },
      ],

      tableData: [
        {
          groupName: 'PARKINGCLOUD',
          groupLevel: '001',
          children: [
            {
              groupName: '대표이사',
              groupLevel: '002',
              children: [
                { groupName: '영업본부', groupLevel: '003', children: [] },
                { groupName: '지원본부', groupLevel: '003', children: [] },
                {
                  groupName: 'iParking Factory',
                  groupLevel: '003',
                  children: [],
                },
                { groupName: 'CORE TECH', groupLevel: '003', children: [] },
                { groupName: '기술본부', groupLevel: '003', children: [] },
                { groupName: '운영사업그룹', groupLevel: '003', children: [] },
                {
                  groupName: '컴플라이언스그룹',
                  groupLevel: '003',
                  children: [],
                },
                { groupName: 'HR본부', groupLevel: '003', children: [] },
              ],
            },
            { groupName: '부사장', groupLevel: '002', children: [] },
            { groupName: '기타', groupLevel: '002', children: [] },
          ],
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

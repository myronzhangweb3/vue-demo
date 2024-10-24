<template>
  <div id="app">
    <el-container>
      <!-- 上部分：查询参数 -->
      <el-header>
        <el-input v-model="query" placeholder="请输入查询参数" style="width: 200px;"></el-input>
        <el-button v-if="role === 'role1'">查询</el-button>
      </el-header>

      <!-- 中间部分：按钮 -->
      <el-main>
        <el-button v-if="role === 'role1'" @click="handleButtonClick">角色1-多选</el-button>
        <el-button v-if="role === 'role2'" @click="handleButtonClick">角色2-单选</el-button>
      </el-main>

      <!-- 下部分：列表展示 -->
      <el-footer>
        <el-table :data="paginatedTableData" @selection-change="handleSelectionChange">
          <el-table-column
              v-if="role === 'role1'"
              type="selection"
              :selectable="role1IsSelectable">
          </el-table-column>

          <el-table-column
              v-if="role === 'role2'"
              type="selection"
              :selectable="role2IsSelectable">
          </el-table-column>

          <el-table-column prop="id" label="编号"></el-table-column>
          <el-table-column prop="type" label="类型"></el-table-column>
          <el-table-column prop="name" label="名称"></el-table-column>
          <el-table-column prop="value" label="值"></el-table-column>
          <el-table-column prop="description" label="描述">
            <template slot-scope="scope">
              <el-input v-model="scope.row.description" disabled></el-input>
            </template>
          </el-table-column>
        </el-table>
        <el-pagination
            background
            layout="prev, pager, next"
            :total="totalItems"
            v-model="currentPage"
            :page-size="pageSize"
            @current-change="handlePageChange">
        </el-pagination>
      </el-footer>
    </el-container>

    <!-- 弹窗 -->
    <dialog-component
        :visible.sync="dialogVisible"
        :selectList="selectList"
        :options1="options1"
        :options2="options2"
        @save="handleSave">
    </dialog-component>
  </div>
</template>

<script>
import DialogComponent from './components/DialogComponent.vue';

export default {
  components: {
    DialogComponent
  },
  created() {
    this.initOption1();
    this.queryData();
  },
  data() {
    return {
      query: '',
      role: 'role1',
      // role: 'role2',
      tableData: [],
      options1: [],
      options2: [
        {label: '选项C', value: ['C', 1]},
        {label: '选项D', value: ['D', 5]}
      ],
      selectList: [],
      dialogVisible: false,

      // 分页相关
      currentPage: 1,
      pageSize: 5
    };
  },
  computed: {
    paginatedTableData() {
      const start = (this.currentPage - 1) * this.pageSize;
      const end = start + this.pageSize;
      return this.tableData.slice(start, end);
    },
    totalItems() {
      return this.tableData.length;
    }
  },
  methods: {
    role1IsSelectable(row) {
      return !row.canModify;
    },
    role2IsSelectable(row) {
      if (!row.canModify) {
        return false;
      }
      if (this.selectList.length === 0) {
        return true;
      }
      if (this.selectList.length === 1) {
        return this.selectList[0] === row;
      }
    },
    handleSelectionChange(selection) {
      if (selection.length === 0) {
        return
      }
      this.selectList = selection;
    },
    handleButtonClick() {
      this.dialogVisible = true;
    },
    handleSave() {
      this.dialogVisible = false;
      this.$message.success("保存成功. Data: " + JSON.stringify(this.selectList));
    },
    queryData() {
      this.tableData = [
        {id: 1, name: 'Item 1', type: 'type1', value: 'value1', description: 'Description 1', canModify: false},
        {id: 2, name: 'Item 2', type: 'type2', value: 'value2', description: 'Description 2', canModify: true},
        {id: 3, name: 'Item 3', type: 'type2', value: 'value3', description: 'Description 3', canModify: false},
        {id: 4, name: 'Item 4', type: 'type1', value: 'value4', description: 'Description 4', canModify: true},
        {id: 5, name: 'Item 5', type: 'type1', value: 'value5', description: 'Description 5', canModify: false},
        {id: 6, name: 'Item 6', type: 'type2', value: 'value6', description: 'Description 6', canModify: true},
        {id: 7, name: 'Item 7', type: 'type1', value: 'value7', description: 'Description 7', canModify: true},
        {id: 8, name: 'Item 8', type: 'type1', value: 'value8', description: 'Description 8', canModify: true},
        {id: 9, name: 'Item 9', type: 'type2', value: 'value9', description: 'Description 9', canModify: true},
        {id: 10, name: 'Item 10', type: 'type1', value: 'value10', description: 'Description 10', canModify: true},
        {id: 11, name: 'Item 11', type: 'type1', value: 'value11', description: 'Description 11', canModify: false},
        {id: 12, name: 'Item 12', type: 'type1', value: 'value12', description: 'Description 12', canModify: true},
      ];
    },
    initOption1() {
      this.options1 = [{label: '选项A', value: 'A'}, {label: '选项B', value: 'B'}];
    },
    handlePageChange(val) {
      this.currentPage = val;
    }
  }
};
</script>

<style>
#app {
  width: 80%;
  margin: 0 auto;
  padding: 20px;
}
</style>

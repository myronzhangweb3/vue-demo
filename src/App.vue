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
        <el-button v-if="role === 'role1'" @click="handleButtonClick('button1')">角色1-多选</el-button>
        <el-button v-if="role === 'role2'" @click="handleButtonClick('button2')">角色2-单选</el-button>
      </el-main>

      <!-- 下部分：列表展示 -->
      <el-footer>
        <el-table :data="tableData" @selection-change="handleSelectionChange">
          <el-table-column
              type="selection"
              v-if="role === 'role1'"
              :selectable="isSelectable">
          </el-table-column>
          <el-table-column v-else-if="role === 'role2'">
            <template slot-scope="scope">
              <el-radio :disabled="scope.row.canModify" v-model="selected" :label="scope.row.id"></el-radio>
            </template>
            </el-table-column>
          <el-table-column prop="name" label="名称"></el-table-column>
          <el-table-column prop="description" label="描述">
            <template slot-scope="scope">
              <el-input v-model="scope.row.description" disabled></el-input>
            </template>
          </el-table-column>
        </el-table>
      </el-footer>
    </el-container>

    <!-- 弹窗 -->
    <el-dialog title="选中内容" :visible.sync="dialogVisible">
      <el-table :data="selectList">
        <el-table-column prop="name" label="名称"></el-table-column>
        <el-table-column prop="description" label="描述" :editable="true">
          <template slot-scope="scope">
            <el-input v-model="scope.row.description"></el-input>
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button @click="removeItem(scope.$index)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="save">保存</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      query: '',
      role: 'role2',
      tableData: [
        {id: 1, name: 'Item 1', description: 'Description 1', canModify: false},
        {id: 2, name: 'Item 2', description: 'Description 2', canModify: true},
        {id: 3, name: 'Item 3', description: 'Description 3',  canModify: false},
        {id: 4, name: 'Item 4', description: 'Description 4', canModify: true},
        {id: 5, name: 'Item 5', description: 'Description 5',  canModify: false},
        {id: 6, name: 'Item 6', description: 'Description 6', canModify: false}
      ],
      selected: null,
      selectList: [],
      dialogVisible: false,
    };
  },
  watch: {
    selected(newVal) {
      // 当 selected 改变时，禁用其他没有被选中的选项
      this.tableData = this.tableData.map(item => ({
        ...item,
        canModify: item.id !== newVal
      }));
    }
  },
  methods: {
    // 判断行是否可选
    isSelectable(row) {
      return !row.canModify;
    },
    // 多选模式使用
    handleSelectionChange(selection) {
      this.selectList = selection;
    },
    handleButtonClick(buttonType) {
      if (buttonType === 'button2') {
        const selectedItem = this.tableData.find(item => item.id === this.selected);
        this.selectList = selectedItem ? [selectedItem] : [];
      }
      this.dialogVisible = true;
    },
    save() {
      this.dialogVisible = false;
      console.log("save:", this.selectList);
      this.$message.success("保存成功");
    },
    removeItem(index) {
      this.selectList.splice(index, 1);
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

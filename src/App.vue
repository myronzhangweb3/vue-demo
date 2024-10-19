<template>
  <div id="app">
    <el-container>
      <!-- 上部分：查询参数 -->
      <el-header>
        <!-- 查询输入框 -->
        <el-input v-model="query" placeholder="请输入查询参数" style="width: 200px;"></el-input>
        <!-- 角色1的查询按钮，仅在角色1时显示 -->
        <el-button v-if="role === 'role1'">查询</el-button>
      </el-header>

      <!-- 中间部分：按钮 -->
      <el-main>
        <!-- 多选按钮，仅在角色1时显示 -->
        <el-button v-if="role === 'role1'" @click="handleButtonClick">角色1-多选</el-button>
        <!-- 单选按钮，仅在角色2时显示 -->
        <el-button v-if="role === 'role2'" @click="handleButtonClick">角色2-单选</el-button>
      </el-main>

      <!-- 下部分：列表展示 -->
      <el-footer>
        <!-- 表格展示 -->
        <el-table :data="tableData" @selection-change="handleSelectionChange">
          <!-- 多选列，基于可选条件 -->
          <el-table-column
              v-if="role === 'role1'"
              type="selection"
              :selectable="isSelectable">
          </el-table-column>
          <!-- 单选列 -->
          <el-table-column v-else>
            <template slot-scope="scope">
              <!-- 单选按钮，依据 role 和 canModify 属性控制 -->
              <el-radio :disabled="scope.row.canModify" v-model="selected" :label="scope.row.id"></el-radio>
            </template>
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
      </el-footer>
    </el-container>

    <!-- 弹窗 -->
    <el-dialog title="选中内容" :visible.sync="dialogVisible">
      <!-- 弹窗中显示选中项 -->
      <el-table :data="selectList">
        <el-table-column prop="id" label="编号"></el-table-column>
        <el-table-column prop="type" label="类型"></el-table-column>
        <el-table-column prop="name" label="名称"></el-table-column>
        <el-table-column prop="value" label="值"></el-table-column>
        <el-table-column prop="description" label="描述">
          <template slot-scope="scope">
            <el-input v-model="scope.row.description"></el-input>
          </template>
        </el-table-column>
        <!-- 新增下拉列1 -->
        <el-table-column label="选项1" fixed="right">
          <template slot-scope="scope">
            <el-select v-model="scope.row.option1" placeholder="请选择">
              <el-option
                  v-for="option in options1"
                  :key="option.value"
                  :label="option.label"
                  :value="option.value"
              ></el-option>
            </el-select>
          </template>
        </el-table-column>

        <!-- 新增下拉列2 -->
        <el-table-column label="选项2" fixed="right">
          <template slot-scope="scope">
            <el-select v-model="scope.row.option2" placeholder="请选择">
              <el-option :value="null" disabled>
                <div>
                  <span style="display: inline-block; width: 50%;">姓名</span>
                  <span style="display: inline-block; width: 50%;">数量</span>
                </div>
              </el-option>
              <el-option
                  v-for="option in options2"
                  :key="option.value"
                  :value="option.value"
              >
                <div>
                  <div>
                    <span style="display: inline-block; width: 50%;">{{ option.value[0] }}</span>
                    <span style="display: inline-block; width: 50%;">{{ option.value[1] }}</span>
                  </div>
                </div>
              </el-option>
            </el-select>
          </template>
        </el-table-column>

        <!-- 操作列 -->
        <el-table-column label="操作" fixed="right">
          <template slot-scope="scope">
            <!-- 删除按钮，删除该行 -->
            <el-button @click="removeItem(scope.$index)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>

      <!-- 弹窗底部操作 -->
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
      query: '', // 输入的查询字符串
      role: 'role1', // 当前角色：控制不同的选项和按钮
      // 表格数据，每项有唯一的id、名称、描述和是否可修改状态
      tableData: [
        {id: 1, name: 'Item 1', type: 'type1', value: 'value1', description: 'Description 1', canModify: false},
        {id: 2, name: 'Item 2', type: 'type1', value: 'value2', description: 'Description 2', canModify: true},
        {id: 3, name: 'Item 3', type: 'type1', value: 'value3', description: 'Description 3', canModify: false},
        {id: 4, name: 'Item 4', type: 'type1', value: 'value4', description: 'Description 4', canModify: true},
        {id: 5, name: 'Item 5', type: 'type1', value: 'value5', description: 'Description 5', canModify: false},
        {id: 6, name: 'Item 6', type: 'type1', value: 'value6', description: 'Description 6', canModify: false}
      ],
      options1: [
        {label: '选项A', value: 'A'},
        {label: '选项B', value: 'B'}
      ],
      options2: [
        {label: '选项C', value: ['C', 1]},
        {label: '选项D', value: ['D', 5]}
      ],
      selected: null, // 当前选中的单选列id
      selectList: [], // 当前选中的多选项列表
      dialogVisible: false, // 控制弹窗显示状态
    };
  },
  watch: {
    selected(newVal) {
      // 监控单选选项的变化，更新每一项的 canModify 状态
      this.tableData.forEach(item => item.canModify = item.id !== newVal);
    }
  },
  methods: {
    // 判断行是否可选：返回 true 则不可选
    isSelectable(row) {
      return !row.canModify;
    },
    // 多选选项变化时更新 selectList
    handleSelectionChange(selection) {
      this.selectList = selection;
    },
    // 点击按钮事件，根据角色处理不同逻辑
    handleButtonClick() {
      if (this.role === 'role2') {
        // 单选时，获取选中项
        const selectedItem = this.tableData.find(item => item.id === this.selected);
        this.selectList = selectedItem ? [selectedItem] : [];
      }
      this.dialogVisible = true;
    },
    // 保存更改
    save() {
      this.dialogVisible = false;
      this.$message.success("保存成功. Data: " + JSON.stringify(this.selectList));
    },
    // 从 selectList 中移除某项
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

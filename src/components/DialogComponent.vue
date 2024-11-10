<template>
  <el-dialog title="选中内容" :visible="visible" @close="closeDialog">
    <el-table :data="dialogPageData">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="type" label="类型"></el-table-column>
      <el-table-column prop="name" label="名称"></el-table-column>
      <el-table-column prop="value" label="值"></el-table-column>
      <el-table-column prop="description" label="描述">
        <template slot-scope="scope">
          <el-input v-model="scope.row.description"></el-input>
        </template>
      </el-table-column>
      <el-table-column label="选项1" fixed="right">
        <template slot-scope="scope">
          <el-select v-model="scope.row.optionValue1" placeholder="请选择">
            <el-option
                v-for="option in options1"
                :key="option.value"
                :label="option.label"
                :value="option.value">
            </el-option>
          </el-select>
        </template>
      </el-table-column>
      <el-table-column label="选项2" fixed="right">
        <template slot-scope="scope">
          <el-select v-model="scope.row.optionValue2" placeholder="请选择">
            <el-option :value="null" disabled>
              <div>
                <span style="display: inline-block; width: 50%;">姓名</span>
                <span style="display: inline-block; width: 50%;">数量</span>
              </div>
            </el-option>
            <el-option
                v-for="option in getOptionData(scope.row.optionValue1)"
                :key="option.label"
                :value="option.value">
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
      <el-table-column label="操作" fixed="right">
        <template slot-scope="scope">
          <el-button @click="removeItem(scope.$index)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-pagination
        background
        layout="prev, pager, next"
        :total="allData.length"
        v-model="dialogCurrentPage"
        :page-size="dialogPageSize"
        @current-change="handlePageChange">
    </el-pagination>

    <span slot="footer" class="dialog-footer">
      <el-button @click="closeDialog">取消</el-button>
      <el-button type="primary" @click="save">保存</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  props: {
    visible: {
      type: Boolean,
      required: true
    },
    options1: Array,
    options2: Array,
    selectList: Array
  },
  data() {
    return {
      allData: this.selectList,
      dialogPageData: [],
      dialogCurrentPage: 1,
      dialogPageSize: 3,
    };
  },
  watch: {
    selectList: {
      immediate: true,
      handler(newVal) {
        this.allData = newVal;
        this.paginatedAllData();
      }
    }
  },
  methods: {
    paginatedAllData() {
      const start = (this.dialogCurrentPage - 1) * this.dialogPageSize;
      const end = start + this.dialogPageSize;
      this.dialogPageData = this.allData.slice(start, end);
    },
    removeItem(index) {
      const globalIndex = (this.dialogCurrentPage - 1) * this.dialogPageSize + index;
      this.allData.splice(globalIndex, 1);
      this.paginatedAllData();
    },
    save() {
      this.$emit('save');
    },
    handlePageChange(val) {
      this.dialogCurrentPage = val;
      this.paginatedAllData();
    },
    closeDialog() {
      this.$emit('update:visible', false);
    },
    getOptionData(optionValue1) {
      console.log("getOptionData", optionValue1)
      if (!optionValue1) {
        return []
      }
      if (optionValue1 === "A") {
        return [{label: '选项A-1', value: ['A-1', 1]},
          {label: '选项A-2', value: ['A-2', 5]}]
      } else {
        return [{label: '选项B-1', value: ['B-1', 1]},
          {label: '选项B-2', value: ['B-2', 5]}]
      }
    }
  }
};
</script>

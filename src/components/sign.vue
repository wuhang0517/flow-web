<template>
  <div id="info">
    <h1>渠道维护</h1>
    <div style="width: 300px">
      渠道类型:
      <el-input v-model="input" size="medium" style="width: 200px" placeholder="请输入渠道类型"/>
    </div>
    <el-container style="margin-top:20px;height: 400px; width: 100%; border: 1px solid #eee; color: #2c3e50">
      <el-header style=" font-size: 12px">
        <div style="height:100%;width:20%;margin-top:0px;display: inline-block">
          <h1 style="margin:0px">渠道维护</h1>
        </div>
        <div
            style="height:100%;width:20%;position: absolute;right: 0px;display: inline-block">
          <div>
            <el-button style="width: 150px;position: absolute;right: 0px;margin-top:10px;background: #ffffff"
                       type="text"
                       @click="dialogFormVisible=true">
              新增
            </el-button>
            <el-button style="width: 150px;position: absolute;right: 200px;margin-top:10px;background: #ffffff"
                       type="text"
                       @click="selectAll()">
              查询
            </el-button>
          </div>
        </div>
      </el-header>
      <el-main>
        <el-table  :data="tableData">
          <el-table-column type="expand" width="50">
            <el-table ref="multipleTable" tooltip-effect="dark" :data="attrData"  @selection-change="handleSelectionChange">
              <el-table-column type="selection" width="50"/>
              <el-table-column prop="attrId" width="100" label="属性id"/>
              <el-table-column prop="attrName" width="100" label="属性名称"/>
              <el-table-column prop="value" label="属性值">
                <template v-slot="">
                  <el-input v-model="modelvalue" size="mini"></el-input>
                </template>
              </el-table-column>
            </el-table>
            <el-button >确认</el-button>
          </el-table-column>
          <el-table-column prop="tradeTypename" label="渠道名称" width="150"/>
          <el-table-column prop="modelTypeName" label="模式名称" width="150"/>
          <el-table-column prop="modelTypeCHNName" label="模式中文名称" width="150"/>
          <el-table-column prop="modelFuntionId" label="模式对应的模组id" width="150"/>
          <el-table-column prop="modelFuntionName" label="模式对应的模组名称"/>

        </el-table>
      </el-main>
    </el-container>
    <el-container style="margin-top:20px;height: 400px; width: 100%; border: 1px solid #eee; color: #2c3e50">
      <h1>基础功能</h1>
      <div style="margin: 20px 0;">
        <el-checkbox :indeterminate="isIndeterminate" v-model="checkAll" @change="handleCheckAllChange">全选</el-checkbox>
        <div style="margin: 15px 0;"></div>
        <el-checkbox-group v-model="checks" @change="handleCheckedCitiesChange">
          <el-checkbox v-for="check in checkModel" :label="check" :key="check">{{check}}</el-checkbox>
        </el-checkbox-group>
      </div>
    </el-container>
  </div>
</template>

<script>
const allchecks = ['黑名单筛查', '高风险国别筛查', '汇路校验','限额查询'];
export default {
  data() {
    return {
      checkAll: false,
      checks: [],
      checkModel: allchecks,
      isIndeterminate: false,
      input: '',
      modelvalue:'',
      multipleSelection: [],
      tableData: [{modelTypeId: '1',
        modelTypeName: 'oure',
        modelTypeCHNName: '汇出汇款',
        modelFuntionId: '1',
        modelFuntionName: 'oure'},
          {modelTypeId: '2',
        modelTypeName: '',
        modelTypeCHNName: '汇入汇款',
        modelFuntionId: '2',
        modelFuntionName: ''}
      ],
      attrData: [{
        attrId: '1',
        attrName: '机构号'
      }, {
        attrId: '2',
        attrName: '清算类型'
      }, {
        attrId: '3',
        attrName: '国别'
      }, {
        attrId: '4',
        attrName: '币种'
      }, {
        attrId: '5',
        attrName: '汇款方式'
      }]
    }
  },
  methods: {
    handleSelectionChange(val) {
      this.multipleSelection = val
    },
    handleCheckAllChange(val) {
      this.checks = val ? allchecks : [];
      this.isIndeterminate = false;
    },
    handleCheckedCitiesChange(value) {
      let checkedCount = value.length;
      this.checkAll = checkedCount === this.checkModel.length;
      this.isIndeterminate = checkedCount > 0 && checkedCount < this.checkModel.length;
    }
  }
};
</script>

<style scoped>

</style>

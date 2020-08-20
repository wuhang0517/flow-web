<template>
  <div>
    <h1>模式维护</h1>
    <el-container>
      <el-header style=" font-size: 12px;position: relative">
        <div style="height:100%;width:20%;margin-top:0px;display: inline-block">
          <h1 style="margin:0px">模式</h1>
        </div>
        <div
            style="height:100%;width:20%;position: absolute;right: 0px;display: inline-block">
          <div>
            <el-button style="width: 150px;position: absolute;right: 0px;margin-top:10px;background: #ffffff"
                       type="text"
                       @click="dialogFormVisible() ">
              新增
            </el-button>
            <el-button style="width: 150px;position: absolute;right: 200px;margin-top:10px;background: #ffffff"
                       type="text"
                       @click="selectModelAll()">
              查询
            </el-button>
          </div>
        </div>
      </el-header>
      <el-main>
        <el-table :data="tableData">
          <el-table-column prop="modelTypeId" label="模式id" width="150"/>
          <el-table-column prop="modelTypeName" label="模式名称" width="150"/>
          <el-table-column prop="modelTypeCHNName" label="模式中文名称" width="150"/>
          <el-table-column prop="modelFuntionId" label="模式对应的模组id" width="150"/>
          <el-table-column prop="modelFuntionName" label="模式对应的模组名称"/>
        </el-table>
      </el-main>

    </el-container>
    <div>
      <el-dialog title="新增模式" :visible.sync="insertModuleDialog">
        <el-form :model="form">
          <el-form-item label="模式名称" :label-width="formLabelWidth">
            <el-input v-model="form.modelTypeId" auto-complete="off"></el-input>
          </el-form-item>
          <el-form-item label="模式中文名称" :label-width="formLabelWidth">
            <el-input v-model="form.modelTypeName" auto-complete="off"></el-input>
          </el-form-item>
          <el-form-item label="模式对应的模组id" :label-width="formLabelWidth">
            <el-input v-model="form.modelFuntionId" auto-complete="off"></el-input>
          </el-form-item>
          <el-form-item label="模式对应的模组名称" :label-width="formLabelWidth">
            <el-input v-model="form.modelFuntionName" auto-complete="off"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="insertModuleDialog = false">取 消</el-button>
          <el-button type="primary" @click="insertModle(form)">确 定</el-button>
        </div>
        <el-main>
          <el-checkbox :indeterminate="isIndeterminate" v-model="checkAll" @change="handleCheckAllChange">全选
          </el-checkbox>
          <div style="margin: 15px 0;"></div>
          <el-checkbox-group v-model="checkedAttr" @change="handleCheckedCitiesChange">
            <el-checkbox v-for="att in attrs" :label="att" :key="att.attributeId">{{
                att.attributeChnName
              }}
            </el-checkbox>
          </el-checkbox-group>
        </el-main>
      </el-dialog>
    </div>
  </div>
</template>

<script>

export default {
  name: "module",
  data() {
    return {
      input: '',
      checkedAttr: [],
      multipleSelection: [],
      attrs: [],
      checkAll: false,
      isIndeterminate: false,
      tableData: [{
        modelTypeId: '1',
        modelTypeName: 'oure',
        modelTypeCHNName: '汇出汇款',
        modelFuntionId: '1',
        modelFuntionName: 'oure'
      },
        {
          modelTypeId: '2',
          modelTypeName: '',
          modelTypeCHNName: '汇入汇款',
          modelFuntionId: '2',
          modelFuntionName: ''
        }
      ],
      insertModuleDialog: false,
      form: {
        modelTypeName: '',
        modelTypeCHNName: '',
        modelFuntionId: '',
        modelFuntionName: ''
      },
      formLabelWidth: '150px'
    }
  },
  methods: {

    handleCheckAllChange(val) {
      this.checkedAttr = val ? this.attrs : [];
      this.isIndeterminate = false;
    },
    handleCheckedCitiesChange(value) {
      let checkedCount = value.length;
      this.checkAll = checkedCount === this.attrs.length;
      this.isIndeterminate = checkedCount > 0 && checkedCount < this.attrs.length;
    },
    dialogFormVisible(){
      this.insertModuleDialog =true
      this.$axios.get("/api/baseAttribute/selectAll")
          .then(response => {
            const res = response.data
            if (res.ret === true) {
              this.attrs = res.data
            }
          })
    },
    selectModelAll() {
      this.$axios.get("/api/baseAttribute/selectAll")
          .then(response => {
            const res = response.data
            if (res.ret === true) {
              this.attrs = res.data
            }
          })
    },
    insertModle(form) {
      let checked=[]
      for (var i = 0; i < this.checkedAttr.length; i++) {
        checked.push(this.checkedAttr[i].attributeId)
      }
      const formparam ={
        modelTypeName: form.modelTypeName,
        modelTypeChnName: form.modelTypeCHNName,
        modelFunctionId: form.modelFuntionId,
        modelFunctionName: form.modelFuntionName
      }
      const ids = {
        ids: checked.toString()
      }
      this.$axios.post("/api/baseModel/insert", formparam,ids)
          .then(response => {
            const res = response.data
            if (res.ret == true) {
              this.$message({showClose: true, message: "添加成功", type: 'success'});
              this.selectAll();
              this.dialogFormVisible = false
            }
          }).catch(reason => {
        console.log(reason)
      })
      console.log(formparam)
      console.log(checked)
      console.log(form)
    }

  }

}
</script>
<style scoped>
</style>

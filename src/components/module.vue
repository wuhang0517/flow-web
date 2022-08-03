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
                       @click="insertmodel() ">
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
        <el-table :data="models">
          <el-table-column prop="modelTypeId" label="模式id" width="150"/>
          <el-table-column prop="modelTypeName" label="模式名称" width="150"/>
          <el-table-column prop="modelTypeChnName" label="模式中文名称" width="150"/>
          <el-table-column prop="modelFunctionId" label="模式对应的模组id" width="150"/>
          <el-table-column prop="modelFunctionName" label="模式对应的模组名称"/>
          <el-table-column
              fixed="right"
              label="操作"
              width="100">
            <template slot-scope="scope">
              <el-button @click="deletemodel(scope.row)" type="text" size="small">删除</el-button>
              <el-button @click="updatemodel(scope.row)" type="text" size="small">修改</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-main>

    </el-container>
    <div>
      <el-dialog title="新增模式" :visible.sync="insertModuleDialog">
        <el-form :model="form">
          <el-form-item label="模式名称" :label-width="formLabelWidth">
            <el-input v-model="form.modelTypeName" auto-complete="off"></el-input>
          </el-form-item>
          <el-form-item label="模式中文名称" :label-width="formLabelWidth">
            <el-input v-model="form.modelTypeChnName" auto-complete="off"></el-input>
          </el-form-item>
          <el-form-item label="模式对应的模组id" :label-width="formLabelWidth">
            <el-input v-model="form.modelFunctionId" auto-complete="off"></el-input>
          </el-form-item>
          <el-form-item label="模式对应的模组名称" :label-width="formLabelWidth">
            <el-input v-model="form.modelFunctionName" auto-complete="off"></el-input>
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
            <el-checkbox v-for="att in attrs" :label="att" :key="att.attributeName">{{
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
      models: [],
      insertModuleDialog: false,
      form: {
        modelTypeName: '',
        modelTypeChnName: '',
        modelFunctionId: '',
        modelFunctionName: ''
      },
      formLabelWidth: '150px',
      check: []
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
    insertmodel() {
      this.checkAll = false
      this.form = []
      this.checkedAttr = []
      this.insertModuleDialog = true
      if (this.attrs.length <= 0) {
        this.$axios.get("/api/baseAttribute/selectAll")
            .then(response => {
              const res = response.data
              if (res.ret === true) {
                this.attrs = res.data
              }
            });
      }
    },
    selectModelAll() {
      this.models = []
      this.$axios.get("/api/baseModel/selectAll")
          .then(response => {
            const res = response.data
            if (res.ret === true) {
              for (var i = 0; i < res.data.length; i++) {
                this.models.push(res.data[i])
              }
            }
          })
    },
    insertModle(form) {

      let checked = []
      for (var i = 0; i < this.checkedAttr.length; i++) {
        checked.push(this.checkedAttr[i].attributeName)
      }
      const para = {
        modelTypeName: form.modelTypeName,
        modelTypeChnName: form.modelTypeChnName,
        modelFunctionId: form.modelFunctionId,
        modelFunctionName: form.modelFunctionName,
        attributeNames: checked.toString()
      }
      this.$axios.post("/api/baseModel/insert", para)
          .then(response => {
            const res = response.data
            if (res.ret == true) {
              this.$message({showClose: true, message: "添加成功", type: 'success'});
              this.selectModelAll();
              this.form = {}
              this.handleCheckAllChange(false)
            }
          }).catch(reason => {
        console.log(reason)
      })
    },
    deletemodel(row) {
      this.$confirm('此操作将删除该模式, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$axios.get("/api/baseModel/delete?modelTypeName=" + row.modelTypeName)
            .then(response => {
              const res = response.data
              if (res.ret === true) {
                this.$message({showClose: true, message: "删除成功", type: 'success'});
              } else {
                this.$message({showClose: true, message: "删除失败", type: 'fail'});
              }
              this.selectModelAll();
            })
      })
    },
    async updatemodel(row) {
      this.checkedAttr = []
      this.form = {}
      this.insertModuleDialog = true
      this.form.modelTypeName = row.modelTypeName
      this.form.modelTypeChnName = row.modelTypeChnName
      this.form.modelFunctionId = row.modelFunctionId
      this.form.modelFunctionName = row.modelFunctionName
      if (this.attrs.length <= 0) {
        await this.$axios.get("/api/baseAttribute/selectAll")
            .then(response => {
              const res = response.data
              if (res.ret === true) {
                this.attrs = res.data
              }
            });
      }
      this.$axios.get("/api/attrModelRel/select?modelTypeName=" + row.modelTypeName)
          .then(response => {
            const res = response.data
            if (res.ret === true) {
              const attrname = res.data.attributeName.split(",")
              for (let checkattr of attrname) {
                for (let att of this.attrs) {
                  if ((att.attributeName === checkattr)) {
                    this.checkedAttr.push(att)
                    continue
                  }
                }
              }
              let checkedCount = this.checkedAttr.length;
              this.checkAll = this.checkedAttr.length === this.attrs.length;
              this.isIndeterminate = checkedCount > 0 && checkedCount < this.attrs.length;
            }
          })
    }
  }

}
</script>
<style scoped>
</style>

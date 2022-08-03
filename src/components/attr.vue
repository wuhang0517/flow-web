<template>
  <div>
    <h1>属性维护</h1>
    <el-container style="margin-top:20px;height: 800px; width: 100%; border: 1px solid #eee; color: #2c3e50">
      <el-header style=" font-size: 12px;position: relative">
        <div style="height:100%;width:20%;margin-top:0px;display: inline-block">
          <h1 style="margin:0px">属性</h1>
        </div>
        <div
            style="height:100%;width:20%;position: absolute;right: 0px;display: inline-block">
          <div>
            <el-button style="width: 150px;position: absolute;right: 0px;margin-top:10px;background: #ffffff"
                       type="text"
                       @click="insertattr()">
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
        <el-table :data="tableData">
          <el-table-column prop="attributeId" label="属性id" width="150"/>
          <el-table-column prop="attributeName" label="属性英文名称" width="150"/>
          <el-table-column prop="attributeChnName" label="属性中文名称"/>
          <el-table-column
              fixed="right"
              label="操作"
              width="100">
            <template slot-scope="scope">
              <el-button @click="handleClick(scope.row)" type="text" size="small">删除</el-button>
              <el-button @click="updateattr(scope.row)" type="text" size="small">修改</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-main>
    </el-container>
    <div>
      <el-dialog title="新增属性" :visible.sync="dialogFormVisible">
        <el-form :model="form">
          <el-form-item label="属性英文名称" :label-width="formLabelWidth">
            <el-input v-model="form.attributeName" auto-complete="off"></el-input>
          </el-form-item>
          <el-form-item label="属性中文名称" :label-width="formLabelWidth">
            <el-input v-model="form.attributeChnName" auto-complete="off"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="confimattr(form,type)">确 定</el-button>
        </div>
      </el-dialog>
    </div>
  </div>
</template>

<script>
export default {
  name: "attr",
  data() {
    return {
      type: 'insert',
      tableData: [],
      dialogFormVisible: false,
      form: {
        attrName: '',
        attrChnName: ''
      },
      formLabelWidth: '120px'
    }
  },
  methods: {
    confimattr(form, type) {
      const attr = {
        attributeId: form.attributeId,
        attributeName: form.attributeName,
        attributeChnName: form.attributeChnName
      }
      let url = "";
      let msg = "";
      if (type == 'insert') {
        url = "/api/baseAttribute/insert";
        msg = "添加成功"
      } else if (type == 'update') {
        url = "/api/baseAttribute/update";
        msg = "修改成功"
      }
      this.$axios.post(url, attr)
          .then(response => {
            const res = response.data
            if (res.ret == true) {
              this.$message({showClose: true, message: msg, type: 'success'});
            } else {
              this.$message({showClose: true, message: res.msg, type: 'fail'});
            }
            this.form = {}
            this.selectAll();
          }).catch(reason => {
        console.log(reason)
      });
    },
    handleClick(row) {
      this.$confirm('此操作将删除该属性, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$axios.get("/api/baseAttribute/delete?id=" + row.attributeId)
            .then(response => {
              const res = response.data
              if (res.ret === true) {
                this.$message({showClose: true, message: "删除成功", type: 'success'});
                this.selectAll();
              } else {
                this.$message({showClose: true, message: "删除失败", type: 'fail'});
              }
            })
      })
    },
    selectAll() {
      this.tableData = []
      this.$axios.get("/api/baseAttribute/selectAll")
          .then(response => {
            const res = response.data
            if (res.ret === true) {
              for (var i = 0; i < res.data.length; i++) {
                this.tableData.push(res.data[i])
              }
            }
          })
    },
    updateattr(row) {
      this.dialogFormVisible = true
      this.form.attrName = row.attributeName
      this.form.attrChnName = row.attributeChnName
      this.type = 'update'
      this.form = row
    },
    insertattr() {
      this.dialogFormVisible = true
      this.form = {}
    }
  }

}
</script>

<style scoped>

</style>

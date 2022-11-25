<template>
    <div class="home" style="margin: 10px">
      <div style="margin: 10px 0">
        <el-button type="primary" @click="add">新增</el-button>
        <el-button type="primary">导入</el-button>
        <el-button type="primary">导出</el-button>
      </div>
      <div style="margin: 10px 0">
        <el-input v-model="search" placeholder="请输入昵称关键字" style="width: 200px;margin: 10px" clearable/>
        <el-button type="primary" style="margin-left: 5px;" @click="load">查询</el-button>
      </div>
      <el-table :data="tableData" border style="width: 100%">
        <el-table-column fixed prop="id" label="ID" width="120" />
        <el-table-column prop="username" label="用户名" width="120" />
        <el-table-column prop="nickName" label="昵称" width="120" />
        <el-table-column prop="age" label="年龄" width="120" />
        <el-table-column prop="sex" label="性别" width="120" />
        <el-table-column prop="address" label="地址" width="120" />
        <el-table-column fixed="right" style="" label="操作" width="200">
          <template #default="scope">
            <el-button size="small" type="primary" @click="handleEdit(scope.row)">编辑</el-button>
            <el-popconfirm title="是否要删除?" @confirm="handleDelete(scope.row.id)">
              <template #reference>
                <el-button size="small" type="danger" >删除</el-button>
              </template>
            </el-popconfirm>
          </template>
        </el-table-column>
      </el-table>
  
      <div style="margin: 10px 0">
        <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            :page-size="pageSize"
            :page-sizes="[5, 10, 20]"
            layout="total, sizes, prev, pager, next, jumper"
            :total="total">
        </el-pagination>
  
        <el-dialog v-model="dialogVisible" title="新增" width="30%">
          <el-form :model="form" label-width="120px" style="width: 90%">
            <el-form-item label="用户名">
              <el-input v-model="form.username" />
            </el-form-item>
            <el-form-item label="昵称">
              <el-input v-model="form.nickName" />
            </el-form-item>
            <el-form-item label="年龄">
              <el-input v-model="form.age" />
            </el-form-item>
            <el-form-item label="性别">
                <el-radio v-model="form.sex" label="男">男</el-radio>
                <el-radio v-model="form.sex" label="女">女</el-radio>
                <el-radio v-model="form.sex" label="未知">未知</el-radio>
            </el-form-item>
            <el-form-item label="地址">
              <el-input typeo="textarea" v-model="form.address" />
            </el-form-item>
  
          </el-form>
          <template #footer>
          <span class="dialog-footer">
            <el-button @click="dialogVisible = false">取消</el-button>
            <el-button type="primary" @click="save">确定</el-button>
          </span>
          </template>
        </el-dialog>
      </div>
    </div>
</template>

<script>
import request from '@/utils/request'



export default {
  name: 'User',
  components: {

  },
  data(){
    return{
      form: {},
      dialogVisible: false,
      search: '',
      currentPage: 1,
      pageSize: 10,
      total: 0,
      tableData: [

      ]
    }
  },
  created() {
    this.load()
  },
  methods:{
    load(){
      request.get("/user",{
        params: {
            pageNum: this.currentPage,
            pageSize: this.pageSize,
            search: this.search
        }
      }).then(res =>{
        console.log(res)
        this.tableData = res.data.records
        this.total = res.data.total
      })
    },
    add(){
      this.dialogVisible = true
      this.form = {}
    },
    save(){
        if(this.form.id){
            request.put("/user",this.form).then(res =>{
            console.log(res)
            if(res.code === '0' ){
                this.$message({
                    type: "success",
                    message: "更新成功"
                })
            }else{
                this.$message({
                    type: "error",
                    message: res.msg
                })
            }
            this.load()
            this.dialogVisible = false
            
          })
        }else{
            request.post("/user",this.form).then(res =>{
            console.log(res)
            
            if(res.code === '0' ){
                this.$message({
                    type: "success",
                    message: "添加成功"
                })
            }else{
                this.$message({
                    type: "error",
                    message: res.msg
                })
            }
            this.load()
            this.dialogVisible = false
          })
        }
        
      
    },
    handleEdit(row){
        this.form = JSON.parse(JSON.stringify(row))
        this.dialogVisible = true
    },
    handleDelete(id){
        console.log(id)
        request.delete("/user/"+id).then(res =>{
            if(res.code === '0' ){
                this.$message({
                    type: "success",
                    message: "删除成功"
                })
            }else{
                this.$message({
                    type: "error",
                    message: res.msg
                })
            }
            this.load()
        })
    },
    handleSizeChange(val){
        this.pageSize=val;
        this.load()
    },
    handleCurrentChange(val){
        this.currentPage = val;
        this.load()
    },


  }
}
</script>

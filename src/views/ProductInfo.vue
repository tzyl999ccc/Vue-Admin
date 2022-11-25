<template>
    <div class="home" style="margin: 10px">
      <div style="margin: 10px 0">
        <el-button type="primary" @click="add">新增</el-button>
        <el-button type="primary">导入</el-button>
        <el-button type="primary">导出</el-button>
      </div>
      <div style="margin: 10px 0">
        <el-input v-model="search" placeholder="请输入商品名关键字" style="width: 200px;margin: 10px" clearable/>
        <el-button type="primary" style="margin-left: 5px;" @click="load">查询</el-button>
      </div>
      <el-table :data="tableData" border style="width: 100%">
        <el-table-column fixed prop="id" label="商品id" width="120" />
        <el-table-column prop="name" label="商品名" width="120" />
        <el-table-column prop="content" label="商品描述" width="120" />
        <el-table-column prop="price" label="价格" width="120" />
        <el-table-column prop="number" label="总数量" width="120" />
        <el-table-column prop="date" label="创建日期" width="120" />
        <el-table-column label="照片" width="120">
            <template #default="scope">
                <el-image
                    style="width: 100px; height: 100px"
                    :src="scope.row.cover"
                    :preview-src-list="[scope.row.cover]"/>
            </template>
        </el-table-column>
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
            <el-form-item label="商品名">
              <el-input v-model="form.name" />
            </el-form-item>
            <el-form-item label="商品描述">
              <el-input v-model="form.content" />
            </el-form-item>
            <el-form-item label="商品价格">
              <el-input v-model="form.price" />
            </el-form-item>
            <el-form-item label="数量">
              <el-input v-model="form.number" />
            </el-form-item>
            <el-form-item label="创建时间">
                <el-date-picker value-format="YYYY-MM-DD" v-model="form.date" type="date" placeholder="请输入当前时间" style="width: 80%" clearable/>
            </el-form-item>
            <el-form-item label="图片">
                <el-upload ref="upload" action="http://localhost:9090/files/upload" :on-success="filesUploadSuccess">
                    <el-button type="primary">点击上传</el-button>
                </el-upload>
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
  name: 'ProductInfo',
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
    filesUploadSuccess(res){
        console.log(res)
        this.form.cover = res.data
    },
    load(){
      request.get("/productInfo",{
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
      this.$refs['upload'].clearFiles()
    },
    save(){
        if(this.form.id){
            request.put("/productInfo",this.form).then(res =>{
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
            request.post("/productInfo",this.form).then(res =>{
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
        this.$nextTick(() => {
            this.$refs['upload'].clearFiles()
        })
        
    },
    handleDelete(id){
        console.log(id)
        request.delete("/productInfo/"+id).then(res =>{
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

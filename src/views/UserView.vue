<template>
    <div class="demo-image">
      <div style="margin: 10px 0">
        <el-input v-model="search" placeholder="请输入商品名关键字" style="width: 200px;margin: 10px" clearable/>
        <el-button type="primary" style="margin-left: 5px;" @click="find">查询</el-button>
      </div>
      <div v-for="fit in imgs" :key="fit" class="block">
        <span class="demonstration">{{ fit.name }}</span>
        <el-image style="width: 100px; height: 100px" :src="fit.cover"/>
        <span class="demonstration">{{ fit.price }}</span>
        <span class="demonstration">{{ fit.content }}</span>
      </div>
      <div class="foot">
        <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            :page-size="pageSize"
            :page-sizes="[5, 10, 20]"
            layout="total, sizes, prev, pager, next, jumper"
            :total="total">
        </el-pagination>
      </div>
    </div>
</template>

<script>
import request from '@/utils/request'
   export default {
    name: 'UserView',
    components: {

    },
    data(){
        return{
            imgs: [],
            search: '',
            currentPage: 1,
            qpageSize: 10,
            total: 0,
        }
    },
    created() {
        this.find()
    },
    methods: {
        find(){
            request.get("/productInfo",{
              params: {
              pageNum: this.currentPage,
              pageSize: this.pageSize,
              search: this.search
             }
            }).then(res =>{
              console.log(res)
              this.imgs = res.data.records
              this.total = res.data.total
            })
        },
        handleSizeChange(val){
          this.pageSize=val;
          this.find()
        },
        handleCurrentChange(val){
          this.currentPage = val;
          this.find()
        },
    },
   }
</script>

<style>
.demo-image .block {
  padding: 30px 0;
  margin: 5px;
  text-align: center;
  border-style: solid;
  display: inline-block;
  width: 18%;
  box-sizing: border-box;
  vertical-align: top;
  height: 250px;
}
.demo-image .block:last-child {
  border-right: solid;
}
.demo-image .demonstration {
  display: block;
  color: var(--el-text-color-secondary);
  font-size: 14px;
  margin-bottom: 20px;
}
</style>
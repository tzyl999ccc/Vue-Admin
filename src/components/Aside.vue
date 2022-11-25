<template>
  <div>
    <el-menu
        style="width: 200px;min-height:calc(100vh)"
        default-active="user"
        router
        :default-openeds="['1']"
        class="el-menu-vertical-demo">
      <el-sub-menu index="1">
        <template #title>系统管理</template>
        <el-menu-item index="user">用户管理</el-menu-item>
      </el-sub-menu>
      <el-menu-item index="productInfo">商品管理</el-menu-item>
      <el-menu-item index="userView">用户主页面</el-menu-item>
    </el-menu>
  </div>
</template>

<script>

export default {
  name: "Aside",
  data() {
    return {
      user: {}
    }
  },
  created() {
    let userStr = sessionStorage.getItem("user") || "{}"
    this.user = JSON.parse(userStr)
  }
}
</script>

<style scoped>

</style>

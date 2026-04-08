<template>
  <div class="backend-layout">
    <el-container class="container">
      <SideBar class="side-bar" />
      <el-container>
        <el-header>
            <NaviBar />
        </el-header>
        <el-main>
            <router-view class="content-container"></router-view>
        </el-main>
      </el-container>
    </el-container>
    
  </div>
</template>

<script setup>
import SideBar from './SideBar.vue'
import NaviBar from './NaviBar.vue'

</script>

<style lang="scss" scoped>
.backend-layout {
  height: 100vh;
  .container {
    height: 100%;
    .el-header {
      height: 74px !important;
    }
    .side-bar {
      height: 100%;
    }
    .content-container {
      background-color: #fff;
      height: calc(100% - 74px);
      padding: 20px;
    }
  }
  
}
</style>

<template>
  <div class="navi-bar">
    <div class="flex-box">
      <el-button @click="handleCollapse">
        <el-icon><Expand /></el-icon>
      </el-button>
      <p class="navi-title">导航栏</p>
    </div>
    <div class="flex-box">
      <el-dropdown @command="handleCommand">
        <div class="flex-box">
          <el-avatar src="https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png"></el-avatar>
          <p class="user-name">admin</p>
          <el-icon><ArrowDown /></el-icon>
        </div>
        <template #dropdown>
          <el-dropdown-menu>
            <el-dropdown-item command="logout">退出登录</el-dropdown-item>
          </el-dropdown-menu>
        </template>
      </el-dropdown>

    </div>
  </div>
</template>


<script setup>
import { useAdminStore } from '@/stores/admin'


const handleCollapse = () =>{
  useAdminStore().toggleCollapse()
}

const handleCommand = (command) =>{
  if(command === 'logout'){
    // 退出登录逻辑
  }
}


</script>


<style lang="scss" scoped>
.navi-bar {
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 15px;
  background-color: white;
  border-bottom: 1px solid #e5e7eb;
  .flex-box {
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .navi-title {
    margin-left: 20px;
    font-size: 26px;
    font-weight: bold;
    color: #1f2937;
  }
}

.user-name {
        margin: 0 5px;
        font-weight: bold;
    }


</style>

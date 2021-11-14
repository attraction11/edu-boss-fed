<template>
  <div class="header">
    <div class="navbar">
      <el-button type="text" class="hamburger" icon="el-icon-s-fold"></el-button>
      <el-breadcrumb separator-class="el-icon-arrow-right" separator="/">
        <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item
          v-for="record in records"
          :key="record.path"
        >{{ record.meta.title }}</el-breadcrumb-item>
      </el-breadcrumb>
    </div>
    <el-dropdown>
      <span class="el-dropdown-link">
        <el-avatar shape="square" :size="40" :src="userInfo.portrait || require('@/assets/default-avatar.png')"></el-avatar>
        <i class="el-icon-arrow-down el-icon--right"></i>
      </span>
      <el-dropdown-menu slot="dropdown">
        <el-dropdown-item>{{ userInfo.userName }}</el-dropdown-item>
        <el-dropdown-item divided @click.native="handleLogout">退出</el-dropdown-item>
      </el-dropdown-menu>
    </el-dropdown>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { getUserInfo } from '@/services/user'
import { RouteRecord } from 'vue-router'

export default Vue.extend({
  name: 'AppHeader',
  data () {
    return {
      userInfo: {}, // 当前登录用户信息
      records: [] as RouteRecord[]
    }
  },
  created () {
    this.loadUserInfo()
  },
  watch: {
    $route: {
      handler () {
        this.getRecords()
      },
      immediate: true
    }
  },
  methods: {
    async loadUserInfo () {
      const { data } = await getUserInfo()
      this.userInfo = data.content
    },
    getRecords () {
      const matched = this.$route.matched.filter(item => item.meta && item.meta.title && item.meta.breadcrumb !== false)
      this.records = matched
    },
    handleLogout () {
      this.$confirm('确认退出吗？', '退出提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          // 确认执行这里
          // 清除登录状态
          this.$store.commit('setUser', null)

          // 跳转到登录页面
          this.$router.push({
            name: 'login'
          })

          this.$message({
            type: 'success',
            message: '退出成功!'
          })
        })
        .catch(() => {
          // 取消执行这里
          this.$message({
            type: 'info',
            message: '已取消退出'
          })
        })
    }
  }
})
</script>

<style lang="scss" scoped>
.header {
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  .navbar{
    display: flex;
    align-items: center;
    .hamburger {
      margin-right: 10px;
      padding: 15px;
      font-size: 20px;
      border: 0;
      border-radius: 0;
      &:hover{
        background-color: rgba(0,0,0,.1);
      }
    }
  }
  .el-dropdown-link {
    display: flex;
    align-items: center;
  }
}
</style>

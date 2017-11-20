<template>
  <el-row class="container">
    <!-- 头部 -->
    <el-col :span="24" class="topbar-wrap">
      <div class="topbar-logo topbar-btn">
        <a href="/">
          <img src="../assets/logo.png" alt="">
        </a>
      </div>
      <div class="topbar-logos" v-show="!collapsed">
        <a href="/">
          <img src="../assets/logotxt.png" alt="">
        </a>
      </div>
      <div class="topbar-title">
        <span>后台管理系统</span>
      </div>
      <div class="topbar-account topbar-btn">
        <el-dropdown trigger="click">
          <span class="el-dropdown-link userinfo-inner">
            <i class="iconfont icon-user"></i>{{nickname}}
            <i class="iconfont icon-down"></i>
          </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item>
              <router-link to="/user/profile">
                <span>个人信息</span>
              </router-link>
            </el-dropdown-item>
            <el-dropdown-item>
              <router-link to="/user/changepwd">
                <span>修改密码</span>
              </router-link>
            </el-dropdown-item>
            <el-dropdown-item :loading="loading" divided @click.native.prevent="logout">
              退出登录
            </el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </div>
    </el-col>
    <!-- 中间 -->
    <el-col :span="24" class="main">
      <!-- 左侧导航 -->
      <aside :class="{showSidebar:!collapsed}">
        <!-- 展开折叠开关 -->
        <div class="menu-toggle" @click.prevent="collapse">
          <i class="iconfont icon-menufold" v-show="!collapsed"></i>
          <i class="iconfont icon-menuunfold" v-show="collapsed"></i>
        </div>
        <!-- 导航菜单 -->
        <el-menu :collapse="collapsed" router default-active="0">
          <template v-for="(item,index) in $router.options.routes" v-if="item.menuShow">
            <el-submenu v-if="!item.leaf" :index="index+''" :key="item.path">
              <template slot="title">
                <i style="color:#fff" :class="item.iconCls"></i>
                <span style="color:#fff" slot="title">{{item.name}}</span>
              </template>
              <el-menu-item v-for="term in item.children" :key="term.path" :index="term.path" v-if="term.menuShow" :class="$route.path == term.path ? 'is-active' : ''">
                <i :class="term.iconCls"></i>
                <span slot="title">{{term.name}}</span>
              </el-menu-item>
            </el-submenu>
            <el-menu-item v-else-if="item.leaf&&item.children&&item.children.length" :index="item.children[0].path" :class="$route.path == item.children[0].path ? 'is-active' : ''" :key="item.path">
              <i style="color:#fff" :class="item.iconCls"></i>
              <span style="color:#fff" slot="title">{{item.children[0].name}}</span>
            </el-menu-item>
          </template>
        </el-menu>
      </aside>
      <!-- 右侧内容区域 -->
      <section class="content-container">
        <div class="grid-content bg-purple-light">
          <el-col :span="24" class="content-wrapper">
            <transition name="fade" mode="out-in">
              <router-view></router-view>
            </transition>
          </el-col>
        </div>
      </section>
    </el-col>
  </el-row>
</template>

<script>
import { bus } from '../bus.js'
import API from '../api/api_user'

export default {
  name: 'home',
  data() {
    return {
      nickname: '',
      collapsed: false,
      loading: false
    }
  },
  created() {
    bus.$on('setNickName', (text) => {
      this.nickname = text
    })

    bus.$on('goto', (url) => {
      if (url === "/login") {
        localStorage.removeItem('access-user')
      }
      this.$router.push(url)
    })
  },
  methods: {
    collapse() {
      this.collapsed = !this.collapsed
    },
    logout() {
      let _this = this
      this.$confirm('确认退出吗?', '提示', {
        confirmButtonClass: 'el-button--warning'
      }).then(() => {
        //确认
        _this.loading = true;
        API.logout().then(function(result) {
          _this.loading = false;
          localStorage.removeItem('access-user');
          _this.$router.go('/login');
        }, function(err) {
          _this.loading = false;
          _this.$message.error({ showClose: true, message: err.toString(), duration: 2000 });
        }).catch(function(error) {
          _this.loading = false;
          console.log(error);
          _this.$message.error({ showClose: true, message: '请求出现异常', duration: 2000 });
        })
      }).catch(() => {

      })
    }
  },
  mounted() {
    let user = localStorage.getItem('access-user');
    if (user) {
      user = JSON.parse(user);
      this.nickname = user.nickname || '';
    }
  }
}
</script>


<style lang="scss" scoped>
.container {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 100%;
  .topbar-wrap {
    height: 50px;
    line-height: 50px;
    background: #373d41;
    padding: 0;
    .topbar-btn {
      color: #fff;
    }
    &:hover {
      background-color: #4A5064;
    }
    .topbar-logo {
      float: left;
      width: 59px;
      line-height: 26px;
      img {
        padding-left: 8px;
      }
    }
    .topbar-logos {
      float: left;
      width: 120px;
      line-height: 26px;
    }
    .topbar-logo img,
    .topbar-logos img {
      height: 40px;
      margin-top: 5px;
      margin-left: 2px;
    }
    .topbar-title {
      float: left;
      width: 200px;
      border-left: 1px solid #000;
      padding-left: 10px;
      span {
        color: #fff;
        font-size: 18px;
      }
    }
    .topbar-account {
      float: right;
      padding-right: 12px;
      .userinfo-inner {
        color: #fff;
        font-size: 14px;
        padding-left: 10px;
        cursor: pointer;
      }
    }
  }
  .main {
    display: flex;
    position: absolute;
    top: 50px;
    bottom: 0;
    overflow: hidden;
    aside {
      min-width: 50px;
      background-color: #333744;
      &::-webkit-scrollbar {
        display: none;
      }
      &.showSidebar {
        overflow-x: hidden;
        overflow-y: auto;
      }
      .el-menu {
        height: 100%;
        height: calc(100% - 80px);
        border-radius: 0px;
        background-color: #333744;
      }
      .el-menu {
        width: 180px;
      }
      .el-submenu .el-menu-item {
        min-width: 60px;
      }
      .el-menu--collapse {
        width: 60px;
      }
      .el-menu .el-menu-item {
        height: 46px;
        line-height: 46px;
        &.is-active {
          background-color: #00c1de;
          color: #fff;
        }
      }
      .el-menu-item:hover,
      .el-submenu .el-menu-item:hover {
        background-color: #7ed2df;
      }
      .menu-toggle {
        background-color: #4a5064;
        text-align: center;
        color: #fff;
        height: 26px;
        line-height: 30px;
      }
    }
    .content-container {
      background-color: #fff;
      flex: 1;
      overflow-y: auto;
      padding: 10px;
      padding-bottom: 1px;
      .content-wrapper {
        background-color: #fff;
        box-sizing: border-box;
      }
    }
  }
}
</style>



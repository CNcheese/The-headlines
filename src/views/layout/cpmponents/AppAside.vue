<template>
  <el-menu
    default-active="2"
    @open="handleOpen"
    @close="handleClose"
    router
    background-color="#545c64"
    text-color="#fff"
    active-text-color="#ffd04b">
    <el-menu-item>
      <img src="./logo.png" alt="" class="logo">
    </el-menu-item>
    <el-menu-item index="/">
      <i class="iconfont icon-shouye"></i>
      <span slot="title">首页</span>
    </el-menu-item>
    <el-submenu index="1">
      <template slot="title">
        <i class="iconfont icon-gengduoneirong"></i>
        <span>内容</span>
      </template>
      <el-menu-item index="/publish">
        <i class="iconfont icon-fabu3"></i>
        <span slot="title">发布</span>
      </el-menu-item>
      <el-menu-item index="article">
        <i class="iconfont icon-liebiao"></i>
        <span slot="title">内容列表</span>
      </el-menu-item>
    </el-submenu>
    <el-menu-item index="/comment">
      <i class="iconfont icon-icon_comment"></i>
      <span slot="title">评论</span>
    </el-menu-item>
    <el-menu-item index="/image">
      <i class="iconfont icon-kujialeqiyezhan_sucaishoucang"></i>
      <span slot="title">素材</span>
    </el-menu-item>
    <el-menu-item index="/fans">
      <i class="iconfont icon-fensi"></i>
      <span slot="title">粉丝</span>
    </el-menu-item>
    <el-menu-item index="/account">
      <i class="iconfont icon-lianxiren-yuanshijituantubiao"></i>
      <span slot="title">账户</span>
    </el-menu-item>
  </el-menu>
</template>

<script>
export default {
  name: 'AppAside',
  data () {
    return {}
  },
  methods: {
    handleOpen (key, keyPath) {
      console.log(key, keyPath)
    },
    handleClose (key, keyPath) {
      console.log(key, keyPath)
    }
  }
}
</script>

<style lang="less" scoped>
.el-menu {
  height: 100%;
}

.iconfont {
  margin-right: 10px;
  color: #F56C6C;
  font-size: 20px;
}
.logo {
  width: 80%;
}
</style>

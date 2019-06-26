<template>
  <el-row class="app-header">
    <el-col :span="14"> <i class="iconfont icon-heima"></i> <b>江苏传智播客教育科技股份有限公司</b> </el-col>
    <el-col :span="8" :offset="6">
      <el-dropdown trigger="click">
        <span class="el-dropdown-link">
          <el-input placeholder="随便看看" clear="see"></el-input>
          <img width="30" :src="userInfo.photo">
          {{userInfo.name}}<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item>账户设置</el-dropdown-item>
          <el-dropdown-item>退出</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </el-col>
  </el-row>
</template>

<script>
export default {
  name: 'Apphead',
  data () {
    return {
      userInfo: {}
    }
  },
  created () {
    this.userInfo = JSON.parse(window.localStorage.getItem('user_info'))
  }
}
</script>

<style  scoped lang='less'>
.app-header {
  height: 60px;
  display: flex;
  align-items: center;
}

.el-dropdown-link {
  display: flex;
  align-items: center;
  cursor: pointer;
  color: #409EFF;
  img {
    border-radius: 50%;
  }
}

.el-icon-arrow-down {
  font-size: 12px;
}
</style>

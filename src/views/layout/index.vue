<template>
  <el-container>
    <el-aside width="200px">
      <AppAside/>
    </el-aside>
    <el-container>
      <el-header>
          <AppHead/>
      </el-header>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
import AppAside from './cpmponents/AppAside'
import AppHead from './cpmponents/AppHead'
export default {
  name: 'Applayout',
  components: {
    AppAside,
    AppHead
  },
  data () {
    return {}
  }
}
</script>

<style  scoped lang="less">
.el-container {
  height: 100%;
}
.el-aside {
  background-color: #d0dce7;
}
.el-main {
  background-color: #ddd;
}
</style>

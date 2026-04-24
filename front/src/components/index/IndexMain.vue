<template>
  <el-main class="app-main">
    <bread-crumbs :title="title" class="bread-crumbs"></bread-crumbs>
    <router-view class="router-view" style="height:auto;background: transparent;"></router-view>
  </el-main>
</template>

<script>
import menu from '@/utils/menu'

export default {
  data() {
    return {
      menuList: [],
      role: '',
      currentIndex: -2,
      itemMenu: [],
      title: ''
    }
  },
  mounted() {
    this.menuList = menu.list()
    this.role = this.$storage.get('role')
  },
  created() {
    this.init()
  },
  methods: {
    init() {
      this.$nextTick(() => {})
    },
    menuHandler(menuItem) {
      this.$router.push({
        name: menuItem.tableName
      })
      this.title = menuItem.menu
    },
    titleChange(index, menus) {
      this.currentIndex = index
      this.itemMenu = menus
    },
    homeChange(index) {
      this.itemMenu = []
      this.title = ''
      this.currentIndex = index
      this.$router.push({
        name: 'home'
      })
    },
    centerChange(index) {
      this.itemMenu = [{
        buttons: ['新增', '查看', '修改', '删除'],
        menu: '修改密码',
        tableName: 'updatePassword'
      }, {
        buttons: ['新增', '查看', '修改', '删除'],
        menu: '个人信息',
        tableName: 'center'
      }]
      this.title = ''
      this.currentIndex = index
      this.$router.push({
        name: 'home'
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.el-main {
  padding: 24px;
  min-height: 100vh;
  background: #f3f7fb;
}

.router-view {
  padding: 0;
  margin-top: 16px;
  background: transparent;
  box-sizing: border-box;
}

.bread-crumbs {
  width: 100%;
  margin-top: 0;
  box-sizing: border-box;
  padding: 14px 18px;
  background: #fff;
  border: 1px solid #dce7f2;
  border-radius: 18px;
  box-shadow: 0 12px 32px rgba(31,79,120,.06);
}

.detail-form-content {
  background: transparent;
}
</style>

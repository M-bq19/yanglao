<template>
  <el-aside class="index-aside" width="240px">
    <div class="index-aside-inner menulist">
      <div v-for="item in menuList" :key="item.roleName" v-if="role === item.roleName" class="menulist-item">
        <el-menu :mode="2 == 1 ? 'horizontal' : 'vertical'" :unique-opened="true" class="el-menu-demo" default-active="0">
          <el-menu-item index="0" @click="menuHandler('')">棣栭〉</el-menu-item>
          <el-menu-item index="1" @click="menuHandler('center')">涓汉涓績</el-menu-item>
          <template v-for="(menuItem,index) in item.backMenu">
            <el-menu-item
              v-if="getVisibleChildMenus(menuItem).length === 1"
              :key="`${menuItem.menu}-single`"
              :index="String(index + 2)"
              @click="menuHandler(getVisibleChildMenus(menuItem)[0].tableName)"
            >
              {{ menuItem.menu }}
            </el-menu-item>
            <el-submenu
              v-else-if="getVisibleChildMenus(menuItem).length > 1"
              :key="`${menuItem.menu}-group`"
              :index="String(index + 2)"
            >
              <template slot="title">
                <span>{{ menuItem.menu }}</span>
              </template>
              <el-menu-item
                v-for="(child,sort) in getVisibleChildMenus(menuItem)"
                :key="`${menuItem.menu}-${sort}`"
                :index="`${index + 2}-${sort}`"
                @click="menuHandler(child.tableName)"
              >
                {{ child.menu }}
              </el-menu-item>
            </el-submenu>
          </template>
        </el-menu>
      </div>
    </div>
  </el-aside>
</template>

<script>
import menu from '@/utils/menu'

export default {
  data() {
    return {
      menuList: [],
      role: ''
    }
  },
  mounted() {
    const menus = menu.list()
    if (menus) {
      this.menuList = this.sanitizeMenus(menus)
    } else {
      this.$http({
        url: 'menu/list',
        method: 'get',
        params: {
          page: 1,
          limit: 1,
          sort: 'id'
        }
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.menuList = this.sanitizeMenus(JSON.parse(data.data.list[0].menujson))
          this.$storage.set('menus', this.menuList)
        }
      })
    }
    this.role = this.$storage.get('role')
  },
  methods: {
    isHiddenTable(tableName) {
      return ['yiqingjiance', 'wuyeshoufei'].includes(tableName)
    },
    getVisibleChildMenus(menuItem) {
      if (!menuItem || !Array.isArray(menuItem.child)) {
        return []
      }
      return menuItem.child.filter(child => !this.isHiddenTable(child.tableName))
    },
    sanitizeMenus(menus) {
      return JSON.parse(JSON.stringify(menus)).map(item => {
        if (Array.isArray(item.backMenu)) {
          item.backMenu = item.backMenu
            .map(group => {
              group.child = this.getVisibleChildMenus(group)
              return group
            })
            .filter(group => Array.isArray(group.child) && group.child.length > 0)
        }
        return item
      })
    },
    menuHandler(name) {
      this.$router.push('/' + name)
    }
  }
}
</script>

<style lang="scss" scoped>
.index-aside {
  position: relative;
  overflow: hidden;
  display: flex;
  flex-wrap: wrap;
  width: 240px !important;
  background: linear-gradient(180deg, #f8fbff 0%, #f2f7fd 100%);
  border-right: 1px solid #d9e6f3;

  .index-aside-inner {
    height: 100%;
    width: 100%;
    margin-right: -17px;
    margin-bottom: -17px;
    overflow: scroll;
    overflow-x: hidden !important;
    padding-top: 80px;
    box-sizing: border-box;
    background: transparent;

    &:focus {
      outline: none;
    }

    & ::v-deep .el-menu {
      border: 0;
      background-color: transparent;
    }
  }

  .menulist-item {
    width: 240px;
    padding: 18px 16px 28px;
    margin: 0;
    background: transparent;
    box-sizing: border-box;
  }

  .el-menu-demo {
    box-sizing: border-box;
    min-height: calc(100vh - 80px);

    & > .el-menu-item,
    & ::v-deep .el-submenu__title {
      width: 100%;
      height: 42px !important;
      line-height: 42px !important;
      padding: 0 14px !important;
      margin: 0 0 10px;
      color: #47627e;
      font-size: 14px;
      font-weight: 600;
      border-radius: 12px;
      border: 1px solid transparent !important;
      background: transparent !important;
      box-shadow: none !important;
      display: flex;
      align-items: center;
      justify-content: flex-start;
      box-sizing: border-box;
      transition: background-color .2s ease, color .2s ease, transform .2s ease;
    }

    & > .el-menu-item:hover,
    & > .el-menu-item.is-active,
    & ::v-deep .el-submenu__title:hover {
      color: #1f4f78 !important;
      background: #e7f1fb !important;
      transform: translateX(2px);
    }

    .el-submenu {
      margin: 0 0 10px;
    }

    & ::v-deep .el-submenu.is-opened > .el-submenu__title {
      color: #1f4f78 !important;
      background: #e7f1fb !important;
    }

    & ::v-deep .el-submenu__icon-arrow {
      color: #7c95ad !important;
      right: 14px;
    }

    & ::v-deep .el-menu.el-menu--inline {
      width: calc(100% - 12px);
      padding: 6px;
      margin: 6px 6px 0;
      border: 1px solid #d9e6f3;
      border-radius: 14px;
      background: rgba(255, 255, 255, .92);
      box-shadow: none;

      .el-menu-item {
        width: 100%;
        height: 38px;
        line-height: 38px;
        padding: 0 12px !important;
        margin: 0 0 4px;
        color: #5d7388 !important;
        font-size: 13px;
        font-weight: 500;
        border: 0;
        border-radius: 10px;
        background: transparent !important;
        box-shadow: none;
        text-align: left;
        min-width: auto;
        transition: background-color .2s ease, color .2s ease;
      }

      .el-menu-item:last-child {
        margin-bottom: 0;
      }

      .el-menu-item:hover,
      .el-menu-item.is-active {
        color: #1f4f78 !important;
        background: #e7f1fb !important;
      }
    }
  }
}
</style>

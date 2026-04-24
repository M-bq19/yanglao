<template>
  <div class="navbar" :style="{background:heads.headBgColor,height:heads.headHeight,boxShadow:heads.headBoxShadow,lineHeight:heads.headHeight}">
    <div class="title-menu" :style="{justifyContent:heads.headTitleStyle === '1' ? 'flex-start' : 'center'}">
      <el-image
        v-if="heads.headTitleImg"
        class="title-img"
        :style="{width:heads.headTitleImgWidth,height:heads.headTitleImgHeight,boxShadow:heads.headTitleImgBoxShadow,borderRadius:heads.headTitleImgBorderRadius}"
        :src="heads.headTitleImgUrl"
        fit="cover"
      />
      <div class="title-name" :style="{color:heads.headFontColor,fontSize:heads.headFontSize}">{{ this.$project.projectName }}</div>
    </div>
    <div class="right-menu">
      <div class="user-info" :style="{color:heads.headUserInfoFontColor,fontSize:heads.headUserInfoFontSize}">
        {{ this.$storage.get('role') }} {{ this.$storage.get('adminName') }}
      </div>
      <div
        v-if="this.$storage.get('sessionTable') !== 'users'"
        class="logout"
        :style="{color:heads.headLogoutFontColor,fontSize:heads.headLogoutFontSize}"
        @click="onIndexTap"
      >
        返回前台
      </div>
      <div class="logout" :style="{color:heads.headLogoutFontColor,fontSize:heads.headLogoutFontSize}" @click="onLogout">
        退出登录
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      user: {},
      heads: {
        headLogoutFontHoverColor: '#1f4f78',
        headFontSize: '24px',
        headUserInfoFontColor: '#52708d',
        headBoxShadow: '0 10px 30px rgba(31,79,120,.08)',
        headTitleImgHeight: '44px',
        headLogoutFontHoverBgColor: '#e8f2fb',
        headFontColor: '#1f4f78',
        headTitleImg: false,
        headHeight: '80px',
        headTitleImgBorderRadius: '22px',
        headTitleImgUrl: 'http://codegen.caihongy.cn/20201021/cc7d45d9c8164b58b18351764eba9be1.jpg',
        headBgColor: 'linear-gradient(180deg,#ffffff 0%,#f7fbff 100%)',
        headTitleImgBoxShadow: '0 8px 20px rgba(31,79,120,.15)',
        headLogoutFontColor: '#52708d',
        headUserInfoFontSize: '15px',
        headTitleImgWidth: '44px',
        headTitleStyle: '1',
        headLogoutFontSize: '14px'
      }
    }
  },
  created() {
    this.setHeaderStyle()
  },
  mounted() {
    const sessionTable = this.$storage.get('sessionTable')
    this.$http({
      url: sessionTable + '/session',
      method: 'get'
    }).then(({ data }) => {
      if (data && data.code === 0) {
        this.user = data.data
        this.$storage.set('userid', data.data.id)
      } else {
        this.$message.error(data.msg)
      }
    })
  },
  methods: {
    onLogout() {
      this.$storage.clear()
      this.$router.replace({ name: 'login' })
    },
    onIndexTap() {
      window.location.href = `${this.$base.indexUrl}`
    },
    setHeaderStyle() {
      this.$nextTick(() => {
        document.querySelectorAll('.navbar .right-menu .logout').forEach(el => {
          el.addEventListener('mouseenter', e => {
            e.stopPropagation()
            el.style.backgroundColor = this.heads.headLogoutFontHoverBgColor
            el.style.color = this.heads.headLogoutFontHoverColor
          })
          el.addEventListener('mouseleave', e => {
            e.stopPropagation()
            el.style.backgroundColor = 'transparent'
            el.style.color = this.heads.headLogoutFontColor
          })
        })
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.navbar {
  height: 60px;
  line-height: 60px;
  width: 100%;
  padding: 0 28px 0 24px;
  box-sizing: border-box;
  background-color: #fff;
  position: relative;
  z-index: 111;
  border-bottom: 1px solid #dce7f2;

  .right-menu {
    position: absolute;
    right: 28px;
    top: 0;
    height: 100%;
    display: flex;
    justify-content: flex-end;
    align-items: center;
    z-index: 111;

    .user-info {
      font-size: 15px;
      color: #52708d;
      padding: 0 12px;
    }

    .logout {
      font-size: 14px;
      color: #52708d;
      padding: 8px 12px;
      border-radius: 999px;
      cursor: pointer;
      transition: background-color .2s ease, color .2s ease;
    }
  }

  .title-menu {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    width: 100%;
    height: 100%;

    .title-img {
      width: 44px;
      height: 44px;
      border-radius: 22px;
      box-shadow: 0 8px 20px rgba(31,79,120,.15);
      margin-right: 16px;
    }

    .title-name {
      font-size: 24px;
      color: #1f4f78;
      font-weight: 700;
      letter-spacing: 1px;
    }
  }
}
</style>

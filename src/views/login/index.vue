<template>
    <div class="login">
      <van-nav-bar title="登录"/>
      <van-cell-group>
        <van-field v-model="user.mobile" clearable left-icon="phone-o" label="手机号" placeholder="请输入手机号"></van-field>
        <van-field v-model="user.code" left-icon="other-pay" label="验证码" placeholder="请输入验证码">
          <van-button round slot="button" size="small" >发送验证码</van-button>
        </van-field>
      </van-cell-group>
      <div class="login-btn-wrap">
        <van-button type="info" @click="onLogin">登录</van-button>
      </div>

    </div>
</template>

<script>
import request from '@/utils/request'
export default {
  name: 'LoginPage',
  components: {},
  props: {},
  data () {
    return {
      user: {
        mobile: '',
        code: ''
      }

    }
  },
  computed: {},
  watch: {},
  created () {},
  methods: {
    async onLogin () {
      const user = this.user

      const res = await request({
        method: 'POST',
        url: ' /app/v1_0/authorizations',
        data: user
      })
      console.log(res)
    }
  }
}
</script>

<style scoped lang="less">
.login {
  .login-btn-wrap {
    padding: 27px 16px;
    .van-button {
      width: 100%;
      background: #6db4fb;
    }
  }
  .van-cell {
    height: 45px;
    align-items: center;
  }
}
</style>

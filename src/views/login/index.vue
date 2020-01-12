<template>
    <div class="login">
      <van-nav-bar title="登录"/>

      <ValidationObserver ref="form">

        <ValidationProvider name="手机号" rules="required|mobile" immediate>
          <van-field v-model="user.mobile" clearable left-icon="phone-o" label="手机号" placeholder="请输入手机号"></van-field>
        </ValidationProvider>

        <ValidationProvider name="验证码" rules="required|code" immediate>
          <van-field v-model="user.code" left-icon="other-pay" label="验证码" placeholder="请输入验证码">
            <van-count-down v-if="toFrontOnShow" slot="button" :time="1000 * 60" format="ss s" @finish="toFrontOnShow = false" />
            <van-button v-else round slot="button" size="small" @click="onSendSmsCode">发送验证码</van-button>
          </van-field>
        </ValidationProvider>

      </ValidationObserver>

      <div class="login-btn-wrap">
        <van-button type="default" @click="onLogin">登录</van-button>
      </div>

    </div>
</template>

<script>
import { login } from '@/api/user'
import { validate } from 'vee-validate'
export default {
  name: 'LoginPage',
  components: {},
  props: {},
  data () {
    return {
      user: {
        mobile: '13911111111',
        code: '246810'

      },
      toFrontOnShow: false
    }
  },
  computed: {},
  watch: {},
  created () {},
  methods: {
    async onLogin () {
      const user = this.user

      const success = await this.$refs.form.validate()
      if (!success) {
        const errors = this.$refs.form.errors
        for (let key in errors) {
          const item = errors[key]
          if (item[0]) {
            this.$toast(item[0])
            return
          }
        }
      }

      this.$toast.loading({
        duration: 0, // 持续展示 toast
        message: '登陆中...',
        forbidClick: true // 是否禁止背景点击
      })

      try {
        const { data } = await login(user)

        this.$store.commit('setUser', data.data)

        this.$toast.success('登陆成功')
        this.$router.push('/')
      } catch (err) {
        console.log('登陆失败', err)
        this.$toast.fail('登陆失败,手机号或验证码不正确')
      }
    },
    async onSendSmsCode () {
      try {
        const { mobile } = this.user
        const validateResult = await validate(mobile, 'required|mobile', {
          name: '手机号'
        })
        if (!validateResult.valid) {
          this.$toast(validateResult.errors[0])
          return
        }
        this.toFrontOnShow = true
      } catch (err) {
        console.log(err)
        this.$toast('请勿频繁操作')
      }
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
      border: 1px solid  #f25d8e;
      color:  #f25d8e;
    }
  }
  .van-cell {
    height: 45px;
    align-items: center;
  }
}
</style>

<!--短信验证-->
<template>
  <div id="messageAuth" class="container">
    <p class="titleTip">已发送短信验证码到</p>
    <p class="phoneNumber">{{ data.phone | formatPhone }}</p>
    <div class="form-row">
      <!--短信验证码-->
      <div class="smsDiv">
        <sms-code-input
          ref="smsCodeInput"
          label="验证码："
          :value="''"
          @input="smsCodeInput"
          :totalTime="60"
          @btnClick="smsCodeBtnClick"
          :maxlength="6"
          placeholder="请输入验证码"
          type="text">
        </sms-code-input>
      </div>
    </div>
    <!--提交按钮-->
    <div class="dfButtonContainer">
      <mt-button class="button" :class="{ disabled: !smsCode }" size="large" :disabled="!smsCode" @click.native="submit">提交</mt-button>
    </div>
  </div>
</template>

<script>
  import { Indicator, Toast, MessageBox } from 'mint-ui';
  import SmsCodeInput from '@/components/input/SmsCodeInput';

  // console.log(global);

  export default {
    name: 'messageAuth',
    components: {
      SmsCodeInput,
    },
    props: {
      data: {
        type: Object,
        default: {},
      },
    },
    data() {
      return {
        smsCode: '', // 短信验证码
      };
    },
    methods: {
      smsCodeBtnClick() {
        this.startCountDown();
        this.$parent.nextClick(true);
      },
      startCountDown() {
        this.$refs.smsCodeInput.startCountDown();
      },
      smsCodeInput(smsCode) {
        this.smsCode = smsCode;
      },
      submit() { // 提交操作
        const params = {
          phone: this.data.phone,
          serviceCode: this.data.serviceCode,
          captcha: this.smsCode,
          method: 'jxlAuth',
        };
        this.$parent.waitMomentVisible = true;
        this.$refs.smsCodeInput.stopCount();
        this.$store.dispatch('dfOnlyStatus', { url: 'promotion.do', params }).then((res) => {
          // console.log(res);
          this.$parent.waitMomentVisible = false;
          if (!res) return;
          if (res.status === 200) {
            Toast('认证成功');
            window.setTimeout(() => {
              this.$router.back();
            }, 1000);
          } else if (res.status === 203) {
            Toast(res.desc || '短信验证码错误');
          }
        });
      },
    },
  };
</script>

<style lang="scss">
  #messageAuth {
    display: flex;
    display: -webkit-flex;
    flex-direction: column;
    flex: 1;
    overflow: scroll;
    -webkit-overflow-scrolling: touch;
    padding-bottom: 0.6rem;
    .titleTip{
      text-align: center;
      font-size: 0.13rem;
      padding-top: 0.3rem;
    }
    .phoneNumber{
      text-align: center;
      font-size: 0.25rem;
      padding: 0.08rem 0 0;
    }
    .form-row {
      margin-top: 0.2rem;
      background-color: #fff;
    }
    .hairline {
      border-top: 1px solid rgb(231,231,231);
      -webkit-transform: scaleY(0.5);
      transform: scaleY(0.5);
      margin-left: 0.15rem;
    }
    /deep/ .inviteman-input .input {
      text-align: left;
      margin-right: 0.15rem !important;
    }
  }
</style>
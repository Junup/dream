<template>
  <div class="page">
    <div class="page__hd">
      <div class="page__title">智能AI解梦</div>
      <div class="page__desc">智能AI解梦 是一套依赖于CHAT GPT对人类梦境解释的工具，数据来源自周公解梦。</div>
    </div>
    <div class="page__bd">
      <div class="weui-cells__title">请输入您的梦境</div>
      <div class="weui-cells weui-cells_after-title">
        <div class="weui-cell">
          <div class="weui-cell__bd">
            <textarea class placeholder="请输入您的梦境" v-model="dream" style="height: 3.3em" />
            <div class="weui-textarea-counter">{{dream.length}}/200</div>
          </div>
        </div>
      </div>
      <div class="weui-btn-area">
        <button class="weui-btn" type="primary" @click="showTopTipsFun">开始解梦</button>
      </div>
    </div>
  </div>  
</template>

<script>
export default {
  data() {
    return {
      dream: ""
    }
  },
  created() {
    console.log(1)
    // 点击按钮触发登录事件
    wx.login({
      success: res => {
        // 获取临时登录凭证
        const code = res.code;
        // 获取用户信息
        wx.getUserInfo({
          success: res => {
            const userInfo = res.userInfo;
            console.log(userInfo);
            // 将 code 和 userInfo 发送给后台服务器
            wx.request({
              url: 'https://ai-api.aitools666.com/login',
              method: 'get',
              data: {
                code: code
              },
              success: res => {
                // 将服务器返回的自定义登录态 token 存储到本地
                wx.setStorageSync('token', res.data.access_token);
              }
            });
          }
        });
      }
    });
  },
  methods: {
    showTopTipsFun() {
      if (!this.dream) {
        wx.showToast({
          title: '请填写您的梦境',
          icon: 'error',
          duration: 3000,
          mask: true
        });
        return false
      }
      const self = this
      wx.navigateTo({
        url: '/pages/test/main'
      })
      console.log(self.dream)
      wx.sendSocketMessage({
        data: JSON.stringify({
          "msg": self.dream,
          "act": "start_generate",
          "payload": {
            "template_name": "jiemeng"
          }
        })
      })
    }
  }
}
</script>

<style>
</style>

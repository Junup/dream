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
            <div class="weui-textarea-counter">0/200</div>
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
  methods: {
    showTopTipsFun() {
      wx.login({
        success: (Result) => {
          console.log(Result.code)
        }
      })
      wx.getUserProfile({
        desc: '获取用户信息',
        lang: 'zh_CN',
        success: function(res) {
          console.log(res);
        },
        fail: function(e) {
          console.log(e);
        }
      })
      wx.navigateTo({
        url: '/pages/test/main'
      })
    }
  }
}
</script>

<style>
</style>

<template>
  <div class="page">
    <div class="htmlText">
      {{ AImessage }}
      <span class="end" v-if="!isOver"></span>
    </div>
    <div class="weui-btn-area ">
        <button class="weui-btn" type="primary" @click="showTopTipsFun">分享给好友</button>
      </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      AImessage: "",
      isOver: false
    }
  },
  computed: {

  },
  created() {
    this.initWs()
  },
  mounted() {
    this.AImessage = ""
    this.isOver = false
  },
  methods: {
    initWs() {
      const self = this
      setTimeout(() => {
        const token = wx.getStorageSync('token');
        if (token) {
          wx.connectSocket({
            url: 'wss://ai-api.aitools666.com/chat',
            header: {
              'authorization': token
            }
          })
          wx.onSocketOpen(function(res) {
            console.log('WebSocket连接已打开！')
          })
          wx.onSocketMessage(function(res) {
            self.AImessage = self.AImessage + JSON.parse(res.data).message
            self.isOver = JSON.parse(res.data).act === 'answer_finish'
          })
        } else {
          this.initWs()
        }
      }, 500)
    }
  }
}
</script>

<style scoped>
.page{
  padding:2em
}
.htmlText{
  height:70vh;
  overflow:auto
}
.end::after{
  content: "|";
  animation: blink .3s linear infinite alternate;
}
@keyframes blink {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}
</style>

<template>
	<div class="page">
		<div class="htmlText" ref="content">
			{{ AImessage }}
			<span class="end" v-if="!isOver"></span>
		</div>
		<div class="weui-btn-area ">
			<button class="weui-btn" :loading="shareBtnLoading" type="primary" @click="showTopTipsFun">分享给好友</button>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				AImessage: "",
				isOver: false,
				socketMsgQueue: [],
				shareBtnLoading : false
			}
		},
		computed: {

		},
		created() {
			this.initWs()
			uni.$on('start-drame', (data) => {
				this.socketMsgQueue.push({
					"act": "start_generate",
					"payload": {
						"message": data.dream,
						"template_name": "jiemeng"
					}
				})
				this.sendMsg()
			})
		},
		mounted() {
			this.AImessage = ""
			this.isOver = false
			this.recordId = null

		},
		onUnload() {
			this.socketMsgQueue = [];
			wx.closeSocket()
		},

		methods: {
			initWs() {
				const self = this
				const token = wx.getStorageSync('token');
				wx.connectSocket({
					url: 'wss://ai-api.aitools666.com/chat',
					header: {
						'authorization': token
					}
				})
				wx.onSocketOpen((res) => {
					this.sendMsg()
				})
				wx.onSocketMessage((res) => {
					const response = JSON.parse(res.data)
					if (response.act == 'answer') {
						this.AImessage = this.AImessage + response.message
						// this.$nextTick(() => {
						// 	const content = this.$refs.content
						// 	content.style.animation = 'scroll-bottom 0.5s ease-out forwards'
						// 	content.scrollTop = content.scrollHeight
						// })

					} else if (response.act == 'answer_finish') {
						this.isOver = true
						this.recordId = response.payload.record_id
					} else {
						wx.showToast({
							title: response.message,
							icon: 'error',
							duration: 3000,
							mask: true
						});
					}


				})
			},
			sendMsg() {
				for (let i = 0; i < this.socketMsgQueue.length; i++) {
					wx.sendSocketMessage({
						data: JSON.stringify(this.socketMsgQueue[i])
					})
				}
			},
			showTopTipsFun() {
				if (!this.isOver) {
					wx.showToast({
						title: "请等待生成结束",
						icon: 'error',
						duration: 3000,
						mask: true
					});
					return
				}
				this.shareBtnLoading = true
				wx.downloadFile({
					url: 'https://ai-api.aitools666.com/generate-post?record_id=' + this.recordId,
					success: (res) => {
						this.shareBtnLoading = false
						wx.showShareImageMenu({
							path: res.tempFilePath,
							fail: (e)=>{
								console.log(e)
							}
						})
					}
				})
			}
		}
	}
</script>

<style>
	.page {
		padding: 2em
	}

	.htmlText {
		height: 70vh;
		overflow: auto;
		white-space: pre-wrap;
		line-height: 1.6;
		margin-bottom: 1em;
		padding-bottom: 1em;
	}

	.end::after {
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
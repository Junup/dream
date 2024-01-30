<template>
	<div class="page">
		<scroll-view scroll-y="true"  class="htmlText"
		    :scroll-into-view="lastDiv" :scroll-top="scrollTop" scroll-with-animation="true">
			<view ref="content" class="scrolls">
				<text :user-select="true">
					{{ AImessage }}<span class="end" v-if="!isOver"></span>
				</text>
				
			 </view>
			 <view id="last-div">
				 
			 </view>
		</scroll-view>
		
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
				scrollTop:1,
				lastDiv:'last-div',
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
			downScrollTop(isLog){
				let height = 0
				const query = uni.createSelectorQuery().in(this);
				
				query.select('.htmlText').boundingClientRect(data => {
				  height = data.height
				}).exec();
				query.select('.scrolls').boundingClientRect(data => {
					// if(!isLog){
					// 	console.log( data.height , height , 0 , data.height + height , 0 )
					// }
				  this.scrollTop = data.height - height > 0 ? data.height +  height : 0 
				}).exec();
				
			},
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
						this.$nextTick(() => {
							this.downScrollTop()
						})

					} else if (response.act == 'answer_finish') {
						this.isOver = true
						this.recordId = response.payload.record_id
						setTimeout(() => {
							this.downScrollTop(false)
						},500)
						 
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
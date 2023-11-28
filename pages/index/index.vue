<template>
	<view class="page">
		<h5 class="title-h5">周公解梦</h5>
		<p class="text-content">请详细描述你的梦境，本系统将科学的分析、解析您的梦境。</p>
		<view class="uni-title uni-common-pl" style="margin-bottom: 10px;">我梦到了:</view>
		<view class="uni-textarea">
			<textarea class="textarea-style" v-model="dream" placeholder="如:从高处跌落,带着降落伞滑落至草原" />
		</view>
		<view class="scroll_box">
			<!-- <swiper class="swiper"  display-multiple-items="1" :autoplay="autoplay" :interval="interval" :duration="duration">
				<swiper-item v-for="(item,index) in list" :key="index">
					<view class="swiper-item uni-bg-green">{{item}}</view>
				</swiper-item>
			</swiper>
			<swiper class="swiper"  display-multiple-items="1" :autoplay="autoplay" :interval="interval" :duration="duration">
				<swiper-item v-for="(item,index) in list" :key="index">
					<view class="swiper-item uni-bg-green">{{item}}</view>
				</swiper-item>
			</swiper>
			<swiper class="swiper"  display-multiple-items="1" :autoplay="autoplay" :interval="interval" :duration="duration">
				<swiper-item v-for="(item,index) in list" :key="index">
					<view class="swiper-item uni-bg-green">{{item}}</view>
				</swiper-item>
			</swiper>
			<swiper class="swiper"  display-multiple-items="1" :autoplay="autoplay" :interval="interval" :duration="duration">
				<swiper-item v-for="(item,index) in list" :key="index">
					<view class="swiper-item uni-bg-green">{{item}}</view>
				</swiper-item>
			</swiper>
			<swiper class="swiper"  display-multiple-items="1" :autoplay="autoplay" :interval="interval" :duration="duration">
				<swiper-item v-for="(item,index) in list" :key="index">
					<view class="swiper-item uni-bg-green">{{item}}</view>
				</swiper-item>
			</swiper> -->
		</view>
		<button @click="submitForm" class="submit">开始解梦</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',
				swiperDotIndex: 0,
				dream: '',
				autoplay: true,
				interval: 2000,
				duration: 2000,
				list: [
					'请点击发行菜单进行发布',
					'体积较大；若要正式发布',
					'运行模式下不压缩代码且含有sourcemap',
					'检查是否启动多个微信开发者工具，如果是则关闭所有打开的微信开发者工具，',
					'然后再重新运行',
					'如果出现微信开发者工具启动后白屏的问题',
					'或者关闭微信开发者工具，然后再从HBuilderX中启动指定页面',
					'可以通过微信开发者工具切换pages.json中condition配置的页面',
					'中修改文件并保存，会自动刷新微信模拟器',
					'微信开发者工具已启动，在HBuilderX'
				],
			}
		},
		created() {
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
		onLoad() {},
		methods: {
			submitForm() {
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
					url: '/pages/index/decode',
					success() {
						uni.$emit('start-drame',{dream:self.dream})
					}
				})
				
			},
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.input {
		position: relative;
	}

	.label {
		position: absolute;
		top: 1vh;
		left: 3vw;
		z-index: 9;
		font-size: 14px;
		background-color: #fff;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.page {
		padding: 20px;
		background-color: #efefef;
		height: 100%;
		position: absolute;
	}

	.text-style {
		font-size: 14px;
		text-indent: 2em;
		padding-top: 20px;
		border: none;
		background-color: red;
	}

	.textarea-style {
		background-color: #fff;
		padding: 10px;
		width: 100%;
		box-sizing: border-box;
		border-radius: 5px;
	}

	.text-content {
		margin: 3vh 0;
	}

	.swiper {
		height: 30px;
	}

	.title-h5 {
		font-size: 10vw;
		font-weight: bold;
		margin: 5vh 0;
		color: #000;
	}

	.submit {
		background-color: #409eff;
		color: #fff;
		position: fixed;
		left: 0;
		right: 0;
		width: 90%;
		bottom: 10vh;
	}
</style>
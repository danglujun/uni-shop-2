<template>
	<view>
		<view class="login-container">
			<uni-icons
				type="contact"
				size="100"
				color="#afafaf"
			></uni-icons>
			<button
				type="primary"
				class="btn-login"
				@click="getUserProfile"
			>一键登录</button>
			<view class="tip-text">
				登录后尽享更多权益
			</view>
		</view>
	</view>
</template>

<script>
	import { mapMutations, mapState } from 'vuex'

	export default {
		name: "my-login",
		data () {
			return {

			};
		},
		computed: {
			...mapState('m_user', ['redirectInfo'])
		},
		methods: {
			// 调用 mapMutations 辅助方法，把 m_user 模块中的 updateUserInfo 映射到当前组件中使用
			...mapMutations('m_user', ['updateUserInfo', 'updateToken', 'updateRedirectInfo']),

			// 获取微信用户的基本信息
			getUserProfile () {
				uni.getUserProfile({
					desc: '您的授权信息！',
					success: (res) => {
						// 将用户的基本信息存储到 vuex 中
						this.updateUserInfo(res.userInfo)
						// 获取登录成功后的 Token 字符串
						this.getToken(res)
					},
					fail: () => {
						return uni.$showMsg('您取消了登录授权！')
					}
				})
			},

			// 调用登录接口，换取永久的 token
			async getToken (info) {
				// 调用微信登录接口
				const [err, res] = await uni.login().catch(err => err)
				// 判断是否 uni.login() 调用失败
				if (err || res.errMsg !== 'login:ok') return uni.$showMsg('登录失败！')
				// console.log(info, res)
				// 准备参数对象
				const query = {
					code: res.code,
					encryptedData: info.encryptedData,
					iv: info.iv,
					rawData: info.rawData,
					signature: info.signature
				}
				// 换取 token
				// const { data: loginResult } = await uni.$http.post('/api/public/v1/users/wxlogin', query)
				// if (loginResult.meta.status !== 200) return uni.$showMsg('登录失败！')
				// uni.$showMsg('登录成功！')
				// 更新 vuex 中的 token
				// this.updateToken(loginResult.message.token)
				this.updateToken(
					"Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1aWQiOjEyLCJpYXQiOjE1MjU0MDIyMjMsImV4cCI6MTUyNTQ4ODYyM30.g-4GtEQNPwT_Xs0Pq7Lrco_9DfHQQsBiOKZerkO-O-o"
				)
				// console.log(loginResult)
				// 判断 vuex 中的 redirectInfo 是否为 null
				// 如果不为 null，则登录成功之后，需要重新导航到对应的页面
				this.navigateBack()
			},

			// 返回登录之前的页面
			navigateBack () {
				// redirectInfo 不为 null，并且导航方式为 switchTab
				if (this.redirectInfo && this.redirectInfo.openType === 'switchTab') {
					// 调用小程序提供的 uni.switchTab() API 进行页面的导航
					uni.switchTab({
						// 要导航到的页面地址
						url: this.redirectInfo.from,
						// 导航成功之后，把 vuex 中的 redirectInfo 对象重置为 null
						complete: () => {
							this.updateRedirectInfo(null)
						}
					})
				}
			}
		}
	}
</script>

<style lang="scss">
	.login-container {
		height: 750rpx;
		background-color: #F8F8F8;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		position: relative;
		overflow: hidden;

		// 绘制登录盒子底部的半椭圆造型
		&::after {
			content: ' ';
			display: block;
			width: 100%;
			height: 40px;
			position: absolute;
			bottom: 0;
			left: 0;
			background-color: white;
			border-radius: 100%;
			transform: translateY(50%);
		}

		.btn-login {
			width: 90%;
			border-radius: 100px;
			margin: 15px 0;
			background-color: #c00000;
		}

		.tip-text {
			font-size: 12px;
			color: gray;
		}
	}
</style>

<template>
	<view>
		<button class="blue-btn btn-margin" @click="verifyBalance"> 扫枪</button>
		<zwy-popup :ishide='isshow' width="320rpx" height="180rpx" radius="16rpx">
			<!-- 自定义展示内容 -->
			<view class="content">
				<view>
					<text style="font-size: 96rpx;">{{countdown}}</text>
				</view>
			</view>

		</zwy-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				isshow: false,
				userId: "",
				pileId:"",
				gunId: "",
				countdown: 60 // 初始倒计时时间设置为60秒
			}
		},
		onShow() {
			this.userId = uni.getStorageSync("userId")


		},
		methods: {
			verifyBalance() {

				//debugger
				let userId = uni.getStorageSync("userId")
				console.log(userId)
				if (userId) {
					let requestData = {
						userId: userId
					}
					uni.request({
						url: this.$baseUrl + "/userServiceApi/charge/user/getBalance",
						method: "POST",
						data: requestData,
						success: (res) => {
							//debugger
							let balance = res.data.data
							if (balance < 10) {
								uni.showModal({
									content: "余额不足,请先充值",
									success: (res) => {
										//debugger
										if (res.confirm) {
											uni.navigateTo({
												url: "/pages/myWallet/myWallet"
											})
										}
									}

								})
							} else {
								this.scan()
							}
						}
					})
				} else {
					uni.showToast({
						title: "请先登录"
					})
				}

			},

			scan() {

				let that = this
				that.countdown = 60;
				// 调起条码扫描
				uni.scanCode({
					scanType: ['qrCode'],
					success: function(res) {
						//debugger
						console.log('条码类型：' + res.scanType);
						console.log('条码内容：' + res.result);
						let result = res.result
						let deviceInfo = JSON.parse(result)
						that.pileId=deviceInfo.pileId
						that.gunId = deviceInfo.gunId
						//请求服务器创建订单
						that.createBill();

						//显示倒计时
						that.isshow = true;
						const timer = setInterval(() => {
							//debugger
							// console.log(that)
							// console.log(that.countdown)
							if (that.countdown > 0) {
								that.countdown--; // 每秒减1
							} else {
								clearInterval(timer); // 倒计时结束，清除定时器
								that.isshow = false;
								uni.navigateTo({
									url: "/pages/chargingProgress/chargingProgress?billId="+that.billId+"&gunId=" +
										that.gunId
								})
							}
						}, 100);


					},
					fail(err) {
						console.log(err)
					},
					complete() {
						console.log("complete")
					}
				});
			},
			createBill: function() {
				//debugger
				let billParam = {
					userId: this.userId,
					pileId: this.pileId,
					gunId: this.gunId
				}
				//debugger
				uni.request({
					url: this.$baseUrl + "/billServiceApi/charge/ChargingBill/createBill",
					method: "GET",
					data: billParam,
					success: (res) => {
						//debugger
						console.log(res)
						this.billId = res.data.data
						uni.setStorageSync("billId", this.billId)
					}
				})
			}
		}
	}
</script>

<style>
	.yellow-btn {
		background-color: #F5BA62;
		height: 96rpx;
		width: 600rpx;
		border-radius: 42rpx;
		color: #FFFFFFFF;
		font-size: 36rpx;
	}

	.btn-margin {
		margin-top: 40vh;
	}

	.content {
		width: 100%;
		height: 100%;
		display: flex;
		align-items: center;
		flex-direction: row;
		justify-content: center;
		padding-left: 24rpx;
		padding-right: 24rpx;
		padding-bottom: 24rpx;

	}
</style>

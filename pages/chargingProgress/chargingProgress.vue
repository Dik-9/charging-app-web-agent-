<template>
	<view>
		<uni-card>
			<view class="center">
				<view class="progress_background">
					<text class="progress_info"> {{((chargingProgress.chargingCapacity/chargingProgress.totalCapacity)*100).toString().substr(0,4)}}%</text>
				</view>
			</view>

			<view class="detail_info">
				<view>
					<view class="center">电价(元/度)</view>
					<view class="center">{{chargingProgress.oneElectricityCost}}</view>
				</view>
				<view>
					<image src="../../static/line.png" class="line"></image>
				</view>
				<view>
					<view class="center">充电时长(min)</view>
					<view class="center">{{chargingProgress.hours+":"+chargingProgress.minutes+":"+chargingProgress.seconds}}</view>
				</view>

				<view>
					<image src="../../static/line.png" class="line"></image>
				</view>
				<view>
					<view class="center">充电金额(元)</view>
					<view class="center">{{(chargingProgress.totalCost).toString().substr(0,4)}}</view>
				</view>
			</view>


		</uni-card>

		<button hover-class="btn-hover" class="blue-btn" @tap="stopCharging">
			结束充电</button>

		<view>
			<uni-popup ref="alertDialog" type="dialog">
				<uni-popup-dialog :type="msgType" cancelText="关闭" confirmText="同意" title="通知" :content="messageText"
					@confirm="dialogConfirm" @close="dialogClose"></uni-popup-dialog>
			</uni-popup>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				chargingProgress: {
					chargingCapacity: 0,
					totalCapacity: 0.01,
					oneElectricityCost: 0,
					hours: 0,
					minutes: 0,
					seconds: 0,
					totalCost: 0
				},
				msgType: "",
				messageText: "",
				billId: "",
				isShow: false				
			}
		},
		computed: {
			
		},
		onLoad(options) {
			console.log("onLoad")
			//debugger			
			this.billId = options.billId

		},
		onShow() {
			this.userId = uni.getStorageSync("userId")

			//调用摄像头会导致onShow执行二次，这里加个判断保证只执行一次

			if (this.isShow == false) {
				this.createWebSocketConnection()
				//debugger
				console.log("onShow")
				

				let dateTime = this.getDateTime()
				let msg = {
					type: 0,
					title: "充电开始通知",
					content: "你于" + dateTime + "开始充电"
				}
				this.saveMessage(msg)

			}
			this.isShow = true
		},
		onHide() {
			clearInterval(this.interval)
		},
		beforeDestroy() {
			this.socketTask1.close()
		},
		methods: {
			createWebSocketConnection: function() {

				let url = this.$wsUrl + "/billServiceApi/imserver/" + this.userId

				console.log("websockerUrl=" + url)
				this.socketTask1 = uni.connectSocket({
					url: url,
					success(data) {
						console.log(url + " websocket创建成功", data);
					},
				});


				//消息的发送和接收必须在正常连接打开中,才能发送或接收【否则会失败】
				this.socketTask1.onOpen((res) => {
					console.log("socketTask1连接正常打开中...！", res);
					this.is_open_socket = true;
					// 注：只有连接正常打开中 ，才能正常成功发送消息
					this.socketTask1.send({
						data: 'socketTask1我再给你发送消息啦!',
						success() {
							console.log("socketTask1消息发送成功");
						},
					});
					// 注：只有连接正常打开中 ，才能正常收到消息
					this.socketTask1.onMessage((res) => {
						//debugger
						console.log(res)
						let data = res.data
						let websocketMessage = JSON.parse(data)
						if (websocketMessage.state == 2) {
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
						}
						//充电进度
						if (websocketMessage.state == 3) {
							this.chargingProgress.chargingCapacity = websocketMessage.data
								.chargingCapacity
							this.chargingProgress.totalCapacity = websocketMessage.data.totalCapacity
							this.chargingProgress.oneElectricityCost=websocketMessage.data.oneElectricityCost
							this.chargingProgress.hours=websocketMessage.data.hours
							this.chargingProgress.minutes=websocketMessage.data.minutes
							this.chargingProgress.seconds=websocketMessage.data.seconds
							this.chargingProgress.totalCost=websocketMessage.data.totalCost


						}
						if (websocketMessage.state == 1) {
							let data = websocketMessage.data

							if (data.substr(0, 1) == "A") {
								this.messageToggle('error', data.substr(1, data.length - 1))
							}

							if (data.substr(0, 1) == "B") {
								this.messageToggle('warn', data.substr(1, data.length - 1))
							}

							let dateTime = this.getDateTime()
							let msg = {
								type: 1,
								title: "充电异常",
								content: "你于" + dateTime + "充电,发生异常"
							}
							this.saveMessage(msg)
						}


					});
				})


				// 这里仅是事件监听【如果socket关闭了会执行】
				this.socketTask1.onClose((err) => {
					console.log("socketTask1已经被关闭了", err) //在此进行重连			

				})
				//先确保清除了之前的心跳定时器
				clearInterval(this.pingpangTimes1)
				//每过一段时间发送一次心跳，发送Ping,服务器会反馈pong，这样操作以保持socket一直是连接状态，防止断开连接，心跳停止
				this.pingpangTimes1 = setInterval(() => {
					this.socketTask1.send({
						data: "ping",
						success: () => {},
						fail: () => {}
					});
				}, 5000)
			},
			
			saveMessage: function(msg) {
				//debugger
				let str = uni.getStorageSync('messageList')

				let messageList = []
				if (str != "") {
					messageList = JSON.parse(str);
				}

				messageList.push(msg)

				console.log(messageList)

				let str2 = JSON.stringify(messageList)
				uni.setStorageSync('messageList', str2)
			},
			dialogConfirm: function() {
				if (this.msgType == "error") {
					var time = (this.count / 60).toString().substr(0, 4)
					let url = "/pages/order-detail/order-detail?time=" + time + "&billId=" + this.billId

					uni.navigateTo({
						url: url
					})
				}
			},
			dialogClose: function() {},
			messageToggle(type, text) {
				this.msgType = type
				this.messageText = text
				this.$refs.alertDialog.open()
			},
			stopCharging() {
				//debugger
				clearInterval(this.interval)

				let dateTime = this.getDateTime()
				let msg = {
					type: 0,
					title: "充电结束通知",
					content: "你于" + dateTime + "结束充电"
				}
				this.saveMessage(msg)

				
				let url = "/pages/order-detail/order-detail?billId=" + this.billId
				console.log("url=" + url)
				uni.navigateTo({
					url: url
				})
			},
			getDateTime: function() {
				let currentDate = new Date();

				let year = currentDate.getFullYear(); // 获取当前年份
				let month = ("0" + (currentDate.getMonth() + 1)).slice(-2); // 获取当前月份，注意月份从0开始，所以需要加1，并且补0
				let day = ("0" + currentDate.getDate()).slice(-2); // 获取当前日期，补0
				let hours = ("0" + currentDate.getHours()).slice(-2); // 获取当前小时，补0
				let minutes = ("0" + currentDate.getMinutes()).slice(-2); // 获取当前分钟，补0
				let seconds = ("0" + currentDate.getSeconds()).slice(-2); // 获取当前秒钟，补0

				let formattedDate = year + "-" + month + "-" + day + " " + hours + ":" + minutes + ":" +
					seconds; // 拼接成年月日时分秒格式的字符串
				return formattedDate
			}
		}
	}
</script>

<style>
	.center {
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.progress_background {
		background-image: url(../../static/charging_progress@2x.png);
		background-size: cover;
		width: 396rpx;
		height: 396rpx;
		margin-top: 56rpx;
	}

	.progress_info {
		position: relative;
		top: 248rpx;
		left: 160rpx;
		font-size: 32rpx;
	}

	.detail_info {
		font-size: 28rpx;
		color: #0091FF;
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
		margin-top: 24rpx;
	}

	.line {
		width: 2rpx;
		height: 52rpx;
		margin-left: 24rpx;
		margin-right: 24rpx;
	}
</style>

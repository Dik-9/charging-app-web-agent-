<template>
	<view class="container">
		<view class="form-item">
			<text class="form-label">车牌号</text>
			<input class="form-input" v-model="license" placeholder="请输入车牌号" />
		</view>
		<view class="form-item">
			<text class="form-label">品牌</text>
			<input class="form-input" v-model="brand" placeholder="请输入车辆品牌" />
		</view>
		<view class="form-item">
			<text class="form-label">型号</text>
			<input class="form-input" v-model="model" placeholder="请输入车辆型号" />
		</view>
		<view class="form-item">
			<text class="form-label">车架号</text>
			<input class="form-input" v-model="vin" placeholder="请输入车架号" />
		</view>
		<view class="button-group">
			<button class="btn primary" @click="bind">绑定车辆</button>
			<button class="btn secondary" @click="unbind">解绑车辆</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				license: "",
				brand: "",
				model: "",
				vin: ""
			}
		},
		//页面一加载 直接调用的js代码
		onShow() {
			//从存储中取userId
			let userId = uni.getStorageSync("userId")
			//联网取用户的车辆信息
			uni.request({
				url: this.$baseUrl + "/user_api/user/vehicle/binded/" + userId,
				method: "GET",
				success: (res) => {
					let code = res.data.code
					let vehicle=res.data.data
					//res.data=JsonResult code message data
					if (code == 0) {
						//从JsonResult中 拿到data的4个属性
						if(vehicle){
							this.license = res.data.data.license
							this.brand = res.data.data.brand
							this.model = res.data.data.model
							this.vin = res.data.data.vin
						}else{
							uni.showToast({
								title: "请绑定车辆",
								icon: "error"
							});
						}
						//TODO 如果解绑 这些不够 需要 车辆id
					}else{
						//提示 车辆该绑定
					}
				},
				//添加失败fail的逻辑 弹个窗口
			})
		},
		methods: {
			unbind: function() { //解绑车辆
				//debugger
				let userId = uni.getStorageSync("userId")
				let vehicleId = uni.getStorageSync("vehicleId")
				let data = {
					userId: userId,
					vehicleId: vehicleId
				}
				uni.request({
					url: this.$baseUrl + "/user_api/user/vehicle/unbind",
					method: "GET",
					data: data,
					success: (res) => {
						//debugger
						let code = res.data.code
						if (code == 0) {
							uni.showToast({
								title: "解绑成功"
							})
						}
					}
				})
			},
			bind: function() { //绑定车辆
				//debugger
				let userId = uni.getStorageSync("userId")
				let data = {
					userId: userId,
					license: this.license,
					brand: this.brand,
					model: this.model,
					vin: this.vin
				}
				uni.request({
					url: this.$baseUrl + "/user_api/user/vehicle/bind",
					method: "POST",
					data: data,
					success: (res) => {
						//debugger
						let code = res.data.code
						if (code == 0) {
							uni.showToast({
								title: "绑定成功"
							})
							let vehicleId = res.data.data
							uni.setStorageSync("vehicleId", vehicleId)
						}
					}
				})
			}
		}
	}
</script>

<style>
	.container {
		padding: 32rpx;
		background-color: #f8f8f8;
		min-height: 100vh;
	}
	
	.form-item {
		margin-bottom: 40rpx;
		background-color: #fff;
		border-radius: 12rpx;
		padding: 24rpx 32rpx;
		box-shadow: 0 2rpx 12rpx rgba(0, 0, 0, 0.05);
	}
	
	.form-label {
		font-size: 30rpx;
		color: #333;
		display: block;
		margin-bottom: 16rpx;
		font-weight: 500;
	}
	
	.form-input {
		height: 80rpx;
		font-size: 28rpx;
		border: 1rpx solid #e5e5e5;
		border-radius: 8rpx;
		padding: 0 20rpx;
		background-color: #f9f9f9;
	}
	
	.button-group {
		margin-top: 60rpx;
		display: flex;
		flex-direction: column;
		gap: 24rpx;
	}
	
	.btn {
		height: 88rpx;
		border-radius: 44rpx;
		font-size: 32rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		border: none;
	}
	
	.primary {
		background-color: #0091FF;
		color: white;
	}
	
	.secondary {
		background-color: #f0f0f0;
		color: #0091FF;
		border: 1rpx solid #0091FF;
	}
	
	/* 添加点击效果 */
	.btn:active {
		opacity: 0.8;
		transform: scale(0.98);
	}
</style>
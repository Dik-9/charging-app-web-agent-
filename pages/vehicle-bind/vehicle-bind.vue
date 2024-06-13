<template>
	<view>
		<view class="row">
			<text class="title" style="margin-right: 48rpx;">车牌号</text>
			<input class="input-border" v-model="license" />
		</view>
		<view class="row">
			<text class="title" style="margin-right: 48rpx;">品牌</text>
			<input class="input-border" v-model="brand" />
		</view>
		<view class="row">
			<text class="title" style="margin-right: 48rpx;">型号</text>
			<input class="input-border" v-model="model" />
		</view>
		<view class="row">
			<text class="title" style="margin-right: 48rpx;">车架号</text>
			<input class="input-border" v-model="vin" />
		</view>
		<button class="blue-btn" @click="bind"> 绑定</button>
		<button class="blue-btn" @click="unbind"> 解绑</button>
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
		onShow() {
			//从存储中取userId
			let userId = uni.getStorageSync("userId")
			//联网取用户的车辆信息
			uni.request({
				url: this.$baseUrl + "/userServiceApi/getVehicleByUserId/" + userId,
				method: "GET",
				success: (res) => {
					//debugger
					let code = res.data.code
					if (code == 0) {

						this.license = res.data.data.license
						this.brand = res.data.data.brand
						this.model = res.data.data.model
						this.vin = res.data.data.vin
					}
				}
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
					url: this.$baseUrl + "/userServiceApi/unBind",
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
					url: this.$baseUrl + "/userServiceApi/insertVehicleBind",
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
	.title {
		font-size: 32rpx;
		margin-top: 40rpx;
		margin-bottom: 40rpx;
		width: 96rpx;
	}

	.row {
		display: flex;
		flex-direction: row;
		align-items: center;
	}

	.input-border {
		border: 1px solid #999999;
		padding: 24rpx;
	}

	.blue-btn {
		background-color: #0091FF;
		height: 72rpx;
		border-radius: 4rpx;
		color: #FFFFFFFF;
		font-size: 32rpx;
		margin: 24rpx;
	}
</style>

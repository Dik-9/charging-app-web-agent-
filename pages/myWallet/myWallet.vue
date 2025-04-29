<template>
	<view class="page">
		<view class="balance center">
			<view class="balance-label text-center">
				账户可用余额
			</view>
			<view class="balance-amount text-center">
				¥{{balance.toFixed(2)}}
			</view>
		</view>

		<view class="title text-center">充值金额</view>
		<view v-for="(list,rowIndex) in amountList" class="amountContainer">
			<view v-for="(amount,colIndex) in list" :key="colIndex" class="text-center">
				<view class="amountItem center" 
					:class="{active: amount.isActive && !otherAmount}"
					@click="amountClick(rowIndex,colIndex,amount.value)">
					充{{amount.value}}元
				</view>
			</view>
		</view>
		
		<view class="row center">
			<text class="title text-center" style="margin-right: 48rpx;">其它金额</text>
			<input 
				placeholder="请输入50-10000元的整数" 
				class="input-border text-center" 
				v-model="otherAmount" 
				type="number"
				@input="clearSelectedAmount"
			/>
		</view>
		
		<view class="btn-container center">
			<button class="blue-btn" @click="recharge" :disabled="isRecharging">
				{{ isRecharging ? '处理中...' : '充值' }}
			</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				balance: 0,
				selectAmount: null,
				otherAmount: "",
				isRecharging: false,
				amountList: [
					[
						{value: 500, isActive: false},
						{value: 300, isActive: false},
						{value: 200, isActive: false}
					],
					[
						{value: 100, isActive: false},
						{value: 50, isActive: false},
						{value: 20, isActive: false}
					]
				]
			}
		},
		onLoad(options) {
			if (options.pay === "success") {
				uni.showModal({
					title: "充值成功",
					content: "你已充值成功",
					cancelText: "关闭",
					confirmText: "去充电",
					success: (res) => {
						if (res.confirm) {
							uni.switchTab({
								url: "/pages/scanCharging/scanCharging"
							})
						}
					}
				})
			}
			
			this.loadUserBalance();
		},
		methods: {
			loadUserBalance() {
				let userId = uni.getStorageSync("userId");
				// #ifdef H5
				userId = 28;
				// #endif
				
				if (userId) {				
					uni.request({
						url: this.$baseUrl + "/user_api/user/balance/" + userId,
						success: (res) => {
							if (res.data.code === 0) {
								this.balance = res.data.data.balance || 0;
							}
						},
						fail: (err) => {
							console.error("获取余额失败:", err);
						}
					})
				}
			},
			
			amountClick(row, col, amount) {
				this.otherAmount = ""; // 清空其他金额输入
				this.selectAmount = amount;
				
				// 重置所有选中状态
				this.amountList.forEach(row => {
					row.forEach(item => item.isActive = false);
				});
				
				// 设置当前选中状态
				this.amountList[row][col].isActive = true;
			},
			
			clearSelectedAmount() {
				if (this.otherAmount) {
					// 如果有其他金额输入，清除所有选中状态
					this.amountList.forEach(row => {
						row.forEach(item => item.isActive = false);
					});
					this.selectAmount = null;
				}
			},
			
			async recharge() {
							// 金额验证逻辑保持不变
							let amount = 0;
							if (this.otherAmount) {
								amount = parseInt(this.otherAmount);
								if (isNaN(amount) || amount < 50 || amount > 10000) {
									uni.showToast({ title: "请输入50-10000元的整数", icon: "none" });
									return;
								}
							} else if (this.selectAmount) {
								amount = this.selectAmount;
							} else {
								uni.showToast({ title: "请选择或输入充值金额", icon: "none" });
								return;
							}
							
							// 获取用户ID
							let userId = uni.getStorageSync("userId");
							// #ifdef H5
							userId = 28;
							// #endif
							
							this.isRecharging = true;
							
							// 使用uni.request发起充值请求
							uni.request({
								url: this.$baseUrl + "/user_api/user/charge",
								method: "POST",
								data: {
									userId: userId,
									amount: amount
								},
								success: (res) => {
									if (res.data.code === 0) {
										// 充值成功
										this.balance += amount;
										uni.showToast({
											title: "充值成功",
											icon: "success"
										});
									} else {
										// 业务逻辑错误
										uni.showToast({
											title: res.data.msg || "充值失败",
											icon: "none"
										});
									}
								},
								complete: () => {
									this.isRecharging = false;
								}
							});
						}
		}
	}
</script>

<style lang="scss">
	.page {
		padding: 30rpx;
		background-color: #f8f8f8;
		min-height: 100vh;
		box-sizing: border-box;
	}
	
	/* 新增全局居中样式 */
	.text-center {
		text-align: center;
	}
	
	.center {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
	
	.balance {
		background: linear-gradient(135deg, #0091FF, #0066CC);
		height: 240rpx;
		border-radius: 16rpx;
		color: white;
		margin-bottom: 40rpx;
		box-shadow: 0 4rpx 12rpx rgba(0, 145, 255, 0.2);
		width: 100%;
		
		&-label {
			font-size: 28rpx;
			opacity: 0.9;
			margin-bottom: 16rpx;
			width: 100%;
		}
		
		&-amount {
			font-size: 64rpx;
			font-weight: bold;
			width: 100%;
		}
	}
	
	.title {
		font-size: 32rpx;
		font-weight: 500;
		color: #333;
		margin: 40rpx 0 24rpx;
		width: 100%;
	}
	
	.amountContainer {
		display: flex;
		justify-content: center;
		margin-bottom: 24rpx;
		width: 100%;
		gap: 20rpx;
	}
	
	.amountItem {
		width: 210rpx;
		height: 104rpx;
		border: 1px solid #e0e0e0;
		border-radius: 8rpx;
		background-color: #fff;
		color: #333;
		font-size: 32rpx;
		transition: all 0.2s;
		display: flex;
		align-items: center;
		justify-content: center;
		
		&.active {
			border: 2px solid #0091FF;
			background-color: rgba(0, 145, 255, 0.05);
			color: #0091FF;
		}
	}
	
	.row {
		display: flex;
		flex-direction: column;
		align-items: center;
		margin: 40rpx 0;
		width: 100%;
		
		.title {
			margin: 0 0 20rpx 0;
		}
	}
	
	.input-border {
		width: 80%;
		height: 80rpx;
		border: 1px solid #e0e0e0;
		border-radius: 8rpx;
		padding: 0 24rpx;
		background-color: #fff;
		font-size: 28rpx;
		text-align: center;
	}
	
	.btn-container {
		width: 100%;
		margin-top: 40rpx;
	}
	
	.blue-btn {
		background: linear-gradient(135deg, #0091FF, #0066CC);
		height: 88rpx;
		width: 80%;
		border-radius: 44rpx;
		color: #fff;
		font-size: 32rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		box-shadow: 0 4rpx 12rpx rgba(0, 145, 255, 0.3);
		transition: opacity 0.3s;
		
		&:active {
			opacity: 0.8;
		}
		
		&[disabled] {
			opacity: 0.6;
		}
	}
</style>
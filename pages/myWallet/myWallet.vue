<template>
	<view class="page">
		<view class="balance center">
			<view style="font-size: 28rpx;">
				账户可用余额
			</view>
			<view style="font-size: 64rpx;">
				{{balance}}
			</view>
		</view>

		<view class="title">充值金额</view>
		<view v-for="(list,rowIndex) in amountList" class="amountContainer">

			<view v-for="(amount,colIndex) in list">
				<view class="amountItem center" :class="{active:amount.isActive}"
					@click="amountClick(rowIndex,colIndex,amount.value)">
					充{{amount.value}}元</view>
			</view>
		</view>
		<view class="row">
			<text class="title" style="margin-right: 48rpx;">其它金额</text>
			<input placeholder="请输入50-10000元的整数" class="input-border" v-model="otherAmount" />
		</view>
		<button class="blue-btn" @click="recharge"> 充值</button>
	</view>
</template>

<script>
	//浏览器运行需要指定userId

	
	export default {
		data() {
			return {
				balance: 0,
				selectAmount: "",
				otherAmount: "",
				amountList: [
					[{
						value: 500,
						isActive: false
					}, {
						value: 300,
						isActive: false
					}, {
						value: 200,
						isActive: false
					}],
					[{
						value: 100,
						isActive: false
					}, {
						value: 50,
						isActive: false
					}, {
						value: 20,
						isActive: false
					}]
				],

			}
		},
		onLoad(options) {
			var payResult=options.pay
			console.log("payResult="+payResult)
			if (payResult=="success"){
				uni.showModal({
					content:"你已充值成功",
					cancelText:"关闭",
					confirmText:"去充电",
					success(res) {
						console.log(res)
						if (res.confirm){
							uni.switchTab({
								url:"/pages/scanCharging/scanCharging"
							})
						}
						
						if (res.cancel){
							
						}
					}
				})
			}
			//debugger
			let userId = uni.getStorageSync("userId")
			// #ifdef H5
			userId = 28
			// #endif
			console.log(userId)
			if (userId) {				
				uni.request({
					url: this.$baseUrl + "/userServiceApi/charge/user/"+userId,													
					success: (res) => {
						//debugger
						let userVO = res.data.data
						this.balance = userVO.balance
						console.log(this.balance)
					}
				})
			}
		},
		methods: {
			"recharge": function() {
				//debugger
				let amount = 0;
				if (this.otherAmount) {
					amount = parseInt(this.otherAmount)
				} else {
					amount = this.selectAmount
				}
				if (amount < 1) {
					uni.showToast({
						title: "没有选择金额"
					})
					return
				}
				let userId = uni.getStorageSync("userId")
				// #ifdef H5
				userId = 28
				// #endif
				let url = this.$baseUrl + "/userServiceApi/create?userId=" + userId + "&amount=" + amount
				window.location.href = url

			},
			"amountClick": function(row, col, amount) {
				//debugger
				this.selectAmount = amount

				for (let rowIndex = 0; rowIndex < this.amountList.length; rowIndex++) {
					for (let colIndex = 0; colIndex < this.amountList[rowIndex].length; colIndex++) {
						this.amountList[rowIndex][colIndex].isActive = false
					}
				}
				this.amountList[row][col].isActive = true
				console.log("amountList[row][col]=" + this.amountList[row][col].isActive)
			}
		}
	}
</script>

<style>
	.page {
		padding: 30rpx;
	}

	.balance {
		background-color: #0091FF;
		height: 200rpx;
		/* 设置边框样式 */
		border: 0px solid #0091FF;
		/* 设置圆角半径 */
		border-radius: 4px;
		color: white;
	}

	.center {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.amountContainer {
		display: flex;
		flex-direction: row;
		margin-bottom: 24rpx;
	}

	.amountItem {
		width: 210rpx;
		height: 104rpx;
		margin-right: 20rpx;
		border: 1px solid #999999;
	}

	.active {
		border: 2px solid #0091FF;
	}

	.title {
		font-size: 32rpx;
		margin-top: 40rpx;
		margin-bottom: 40rpx;
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

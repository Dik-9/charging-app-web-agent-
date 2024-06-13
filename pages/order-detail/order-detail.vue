<template>
	<view>
		<view class="margin">
			<view class="title"><text>充电订单结束通知</text></view>
			<view class="row row-space">
				<view class="attribute-name"><text>订单编号:</text></view>
				<view class="value"><text class="value-left-space">{{billId}}</text></view>
			</view>
			<view class="row row-space">
				<view class="attribute-name"><text>设备编号:</text></view>
				<view class="value"><text class="value-left-space">4GM00005548</text></view>
			</view>
			<view class="row row-space">
				<view class="attribute-name"><text>充电时长:</text></view>
				<view class="value"><text class="value-left-space">{{billDetailVO.hours?billDetailVO.hours:0+":"+billDetailVO.minutes?billDetailVO.minutes:0+":"+billDetailVO.seconds?billDetailVO.seconds:0}}</text></view>
			</view>
			<view class="row row-space">
				<view class="attribute-name"><text>结束原因:</text></view>
				<view class="value"><text class="value-left-space">检测到负载功率降低为0</text></view>
			</view>
			<view class="row row-space">
				<view class="attribute-name"><text>消费金额:</text></view>
				<view class="value"><text class="value-left-space">{{billDetailVO.totalCost?billDetailVO.totalCost:0}}元</text></view>
			</view>

			<view class="row row-space">
				<view class="attribute-name"><text>备注信息:</text></view>
				<view class="value"><text class="value-left-space">充电之后帐户余额{{billDetailVO.balance?billDetailVO.balance:0}}元。</text>
					<!-- 本次充电之后帐户余额13.32元。造成该订单提交断电原因 1:充电器松动或脱落; 2:充电器待机,停止充电;如有疑问请致电运营商18912627609 -->
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				billId:"",
				billDetailVO:{}
			}
		},
		onLoad(options) {
			//联网取数据时，服务器返回延迟态长，界面不会自动更新
			this.billId = options.billId
		},
		onShow() {
			//debugger			
			this.getBillDetail(this.billId)
		},
		methods: {
			getBillDetail:function(billId){
				//debugger
				let url=this.$baseUrl+"/billServiceApi/charge/ChargingBill/normalStopBill?billId="+billId
				console.log("url="+url)
				uni.request({
					url:url,
					success: (res) => {
						//debugger
						this.billDetailVO=res.data.data
					}
				})
			}

		}
	}
</script>

<style>
	.row {
		flex-flow: row;
		justify-content: flex-start;
		display: flex;
	}

	.margin {
		padding: 24rpx 48rpx;
	}

	.title {
		font-size: 48rpx;
		margin-bottom: 57.6rpx;
	}

	.row-space {
		margin-bottom: 24rpx;
	}

	.attribute-name {

		width: 200rpx;
		color: gray;
	}

	.value {
		width: 550rpx;
		color: royalblue;
	}

	.value-left-space {
		margin-left: 48rpx;
	}
</style>

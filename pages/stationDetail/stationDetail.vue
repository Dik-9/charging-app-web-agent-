<template>
	<view>
		<view>
			<image class="detailImage" src="../../static/detail@2x.jpg"></image>
		</view>
		<view>
			<v-tabs v-model="activeTab" :scroll="false" :tabs="['场地信息', '设备详情']" @change="changeTab" lineHeight="6rpx">
			</v-tabs>
		</view>
		<view v-show="showStationInfo">


			<view class="detail-card">
				<view>
					<text class="title">{{stationInfo.stationName}}</text>
					<text class="station-address"> 运营中</text>
				</view>
				<view class="station-address">
					<view>地&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;址:&nbsp;&nbsp;{{stationInfo.address}}</view>
					<view class="list-box">
						<view>营业时间:&nbsp;&nbsp;8:00-18:00</view>
						<view>服务电话:&nbsp;&nbsp;010-88486655</view>
					</view>
					<view class="list-box">
						<view>场站类型:&nbsp;&nbsp;公共</view>
						<view>运营方式:&nbsp;&nbsp;自营</view>
					</view>
					<br>
					<view class="row station-address">
						<image class="distance-image" src="/static/detail-distance@2x.png"></image>
						1.2km
						<view class="vertical-gray-line"></view>

						<image class="fast-image" src="/static/detail-fast@2x.png"></image>
						10/20
						<view class="vertical-gray-line"></view>

						<image class="slow-image" src="/static/detail-slow@2x.png"></image>
						5/10
					</view>
				</view>
			</view>
			<view class="detail-card station-address margin-top">
				<view class="row">
					<view class="vertical-blue-line"> </view>
					<text class="title">促销活动</text>
				</view>
				<view class="row">
					<view class="active-content-view active-content-view-space row">
						<view>
							<image class="detail-money-image" src="/static/detail-money-icon.png"></image>
						</view>
						<view><text class="money-text">20</text></view>
						<view class="active-detail-view">
							<text>充2000</br>限部分场站</text>

						</view>
					</view>
					<view class="active-content-view row">
						<view>
							<image class="detail-money-image" src="/static/detail-money-icon.png"></image>
						</view>
						<view><text class="money-text">50</text></view>
						<view class="active-detail-view">
							<text>充5000</br>限部分场站</text>

						</view>
					</view>
				</view>
			</view>

			<view class="detail-card station-address margin-top">
				<view class="row">
					<view class="vertical-blue-line"> </view>
					<text class="title">收费说明</text>
				</view>
				电 费:&nbsp;&nbsp;1.01元/度<br>
				服 务 费:&nbsp;&nbsp;0.55元/度<br>
				停 车 费:&nbsp;&nbsp;免费停车2小时;首小时5元;第二小时白天2元/小时;晚上1元/小时;封顶30元。<br>
				支付方式:&nbsp;&nbsp;线上
			</view>



		</view>
		<view v-show="showDevice">

			<uni-card v-for="gunInfoVO in gunInfoVOList">
				<view class="row device-view">
					<view>
						<image class="device-icon-image" src="/static/detail-device-icon@2x.png"></image>
						<br>
						<text>{{gunInfoVO.gunName}}</text>
					</view>
					<view class="device-vertical-gray-line"></view>
					<view>
						<view class="row">

							<view v-if="gunInfoVO.gunStatus==1" class="idle-view center">空闲</view>
							<view v-if="gunInfoVO.gunStatus==2" class="use-view center">使用中</view>
							<view v-if="gunInfoVO.gunStatus==3" class="use-view center">故障</view>
						</view>
						<view>
							<view v-if="gunInfoVO.gunType==1" >慢充 功率{{gunInfoVO.electricity}}KW</view>
							<view v-if="gunInfoVO.gunType==2" >快充 功率{{gunInfoVO.electricity}}KW</view>
						</view>
					</view>
					<view>
						<view v-if="gunInfoVO.gunStatus==1" class="go-charging-idle-view center">去充电</view>
						<view v-else class="go-charging-use-view center">去充电</view>
					</view>
				</view>

			</uni-card>



		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				activeTab: 0,
				showStationInfo: true,
				showDevice: false,
				stationInfo: {},
				gunInfoVOList: []				
			}
		},
		onLoad(options) {
			
			let id=options.id
			this.getStation(id)
			this.getGun(id)
			
		},
		methods: {
			getStation(stationId){
				uni.request({
					url: this.$baseUrl+"/device_api/device/station/detail/" + stationId,
					method: 'GET',
					success: (res) => {
						//debugger
						this.stationInfo = res.data.data
						this.gunInfoVOList = this.stationInfo.gunInfoPOList
					}
				})
			},
			changeTab(index) {
				console.log('当前选中的项：' + index)
				if (index == 0) {
					this.showStationInfo = true
					this.showDevice = false
				} else {
					this.showDevice = true
					this.showStationInfo = false
				}
			}
		}
	}
</script>

<style>
	.detailImage {
		width: 750rpx;
		height: 180rpx
	}



	.detail-card {
		background-color: #FFFFFF;
		padding-left: 24rpx;
		padding-right: 24rpx;
		padding-top: 48rpx;
		padding-bottom: 48rpx;
	}

	.list-box {
		column-count: 2;
	}



	.margin-top {
		margin-top: 24rpx;
	}

	.tab {
		height: 104rpx;
		background-color: #FFFFFF;
		padding-top: 8rpx;
	}

	.row {
		flex-flow: row;
		justify-content: flex-start;
		display: flex;
	}

	.distance-image {
		width: 28rpx;
		height: 26rpx;
		margin-right: 12rpx;
	}

	.fast-image {
		width: 24rpx;
		height: 30rpx;
		margin-right: 12rpx;
	}

	.slow-image {
		width: 24rpx;
		height: 30rpx;
		margin-right: 12rpx;
	}

	.vertical-gray-line {
		width: 2rpx;
		height: 26rpx;
		background-color: #999999;
		margin-left: 36rpx;
		margin-right: 36rpx;
	}

	.device-vertical-gray-line {
		width: 2rpx;
		height: 88rpx;
		background-color: #999999;
		margin-left: 18rpx;
		margin-right: 18rpx;
	}

	.vertical-blue-line {
		width: 8rpx;
		height: 42rpx;
		background-color: #0091FF;

		margin-right: 36rpx;
	}

	.active-content-view {
		width: 310rpx;
		height: 114rpx;
		border: 2px solid #0091FF;
		border-radius: 8rpx;

		padding-left: 24rpx;
		padding-right: 24rpx;
		padding-top: 4rpx;
	}

	.active-content-view-space {
		margin-right: 36rpx;
	}

	.detail-money-image {
		width: 18rpx;
		height: 24rpx;
		margin-top: 16rpx;
	}

	.money-text {
		font-size: 80rpx;
		color: #0091FF;
		margin-left: 8rpx;
		margin-right: 8rpx;
	}

	.active-detail-view {
		padding-top: 16rpx;
	}

	.device-icon-image {
		width: 42rpx;
		height: 34rpx;
		margin-left: 12rpx;
	}

	.device-view {
		margin-top: 24rpx;
		margin-bottom: 24rpx;
	}

	.idle-view {
		background-color: #BCE2FF;
		height: 44rpx;
		width: 96rpx;
		border-radius: 22rpx;
		color: #1399FF;
		font-size: 24rpx;
		margin-right: 36rpx;
	}

	.use-view {
		background-color: #FFF1E0;
		height: 44rpx;
		width: 96rpx;
		border-radius: 22rpx;
		color: #FF9830;
		font-size: 24rpx;
		margin-right: 36rpx;
	}

	.go-charging-idle-view {
		background-color: #0091FF;
		height: 64rpx;
		width: 140rpx;
		border-radius: 42rpx;
		color: #FFFFFF;
		font-size: 28rpx;
		margin-left: 24rpx;
	}

	.go-charging-use-view {
		background-color: #EDF0E9;
		height: 64rpx;
		width: 140rpx;
		border-radius: 42rpx;
		color: #D4D7D1;
		font-size: 28rpx;
		margin-left: 24rpx;
	}

	.count-text {
		color: #1399FF;
		font-size: 24rpx;
	}
</style>

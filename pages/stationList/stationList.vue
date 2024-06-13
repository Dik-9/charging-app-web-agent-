<template>
	<view>
		<uni-card v-for="station in stationList" v-on:click="stationClick(station.id)" :key="station.id">
			<view>
				<view class="row">
					<view style="width: 90%;">
						<view class="station-name">{{station.stationName}}</view>
						<view class="station-address">{{station.address}}</view>
					</view>
					<view style="width: 10%;">
						<image class="to-detail" src="/static/to-detail@2x.png"></image>
					</view>
				</view>

				<view class="station-desc">
					促销 | 充过 | 公共 | 自营<br>
				</view>
				<view class="station-price">
					直流桩 | 1.5元/度
				</view>
			</view>
		</uni-card>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				stationList: []
			}
		},
		onLoad(options) {
			let data = {
				longitude: options.longitude,
				latitude: options.latitude,
				radius: 5000
			}
			uni.request({
				url: "http://act.codeboy.com:6003/charge/station/nearbyList",
				method: "GET",
				data: data,
				success: (res) => {
					console.log(res)
					this.stationList = res.data.data
				},
				fail: (err) => {
					console.log(err)
				}
			})
		},
		methods: {
			stationClick: function(id) {

				uni.navigateTo({
					url: "/pages/stationDetail/stationDetail?id=" + id
				})
			}
		}
	}
</script>

<style>
	.row {
		display: flex;
		flex-direction: row;
	}

	.to-detail {
		width: 76rpx;
		height: 76rpx;
	}
</style>

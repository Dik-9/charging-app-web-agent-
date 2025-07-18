<template>
	<view>
		<map style="width: 100%; height: 100vh;" :latitude="latitude" :longitude="longitude" :markers="covers"
			@markertap="markertap">
		</map>
		<cover-view class="map-cover-view">
			<text v-on:tap="listTap">列表</text>
		</cover-view>

		<zwy-popup :ishide='isshow' width="350px" height="180rpx" radius="16rpx">
			<!-- 自定义展示内容 -->
			<view class="content">
				<view>
					<image class="station-icon" src="../../static/station-icon@2x.png"></image>
				</view>
				<view>
					<view><text>{{mapClickStation.stationName}}</text><text class="price-color">1.50元/度</text></view>
					<view class="line-space"></view>
					<view class="row">
						<image class="map-icon" src="../../static/nearby_normal@2x.png"></image>
						<text class="distance-color">{{mapClickStation.distance}}KM</text>
						<view class="go-charging" @click="stationClick(mapClickStation.stationId)">详情</view>
					</view>
				</view>

			</view>
			<!-- 自定义关闭按钮 -->
			<view class="close" @click="isshow=false">✕</view>
		</zwy-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				latitude: 39.909,
				longitude: 116.39742,
				covers: [{
					latitude: 39.909,
					longitude: 116.39742,
					iconPath: '/static/location@3x.png'
				}],
				stationList: [],
				mapClickStation: {},
				isshow: false,
			}
		},
		onLoad() {
			let _this = this
			console.log(this.$baseUrl)
			
			//this.getNearbyStation()
			//获取用户的经纬度	
			//有时在浏览器中运行，定位失败，得不到附近充电桩信息		
			uni.getLocation({
				type: 'gcj02',
				success: function(res) {
					//debugger
					console.log(this)
					console.log('当前位置的经度：' + res.longitude)
					console.log('当前位置的纬度：' + res.latitude)

					_this.latitude = res.latitude
					_this.longitude = res.longitude

					_this.covers[0].latitude = res.latitude
					_this.covers[0].longitude = res.longitude

					//最后要打开，微信小程序开发工具中能定位成功。
					_this.getNearbyStation()

				},
				fail: function(error) {
					console.log("fail")
					console.log(error)
				}
			});
		},
		methods: {
			stationClick:function(id){
				
				uni.navigateTo({
					url:"/pages/stationDetail/stationDetail?id="+id
				})
			},
			listTap:function(){
				uni.navigateTo({
					url:"/pages/stationList/stationList?longitude="+this.longitude+"&latitude="+this.latitude
				})
			},
			markertap(marker) {
				console.log("markertap")
				console.log(marker)
				//debugger
				let id = marker.detail.markerId
				this.mapClickStation = this.stationList[id]
				console.log(this.mapClickStation)
				this.isshow = true
			},
			getNearbyStation: function() {
				//debugger
				//把地址写死了，定位到别的城市没有充电站
				this.latitude = 39.908663
				this.longitude = 116.398476

				let data = {
					longitude: this.longitude,
					latitude: this.latitude,
					radius: 5000
				}
				uni.request({
					url: this.$baseUrl+"/device_api/device/station/near",
					method: "GET",
					data: data,
					success: (res) => {
						console.log("附近充电站响应:"+res)
						console.log(res)
						this.stationList = res.data.data
						//debugger
						for (let i = 0; i < this.stationList.length; i++) {
							console.log(i)
							let station = this.stationList[i]
							//不指定id 单击事件不执行
							let marker = {
								id: i,
								latitude: station.stationLat,
								longitude: station.stationLng,
								iconPath: '/static/map_marker@2x.png'
							}
							this.covers.push(marker)
							//console.log(this.markers)
						}
					},
					fail: (err) => {
						console.log(err)
					}
				})
			}
		}
	}
</script>

<style>
	.map-cover-view{
		position: absolute;
		right: 0rpx;
		top: 0rpx;		
	}
	.content {
		width: 100%;
		height: 100%;
		display: flex;
		align-items: center;
		flex-direction: row;
		justify-content: left;
		padding-left: 24rpx;
		padding-right: 24rpx;
		padding-bottom: 24rpx;
	}

	.station-icon {
		width: 120rpx;
		height: 120rpx;
		margin-right: 24rpx;
	}
	.map-icon {
		width: 36rpx;
		height: 36rpx;
		margin-right: 8rpx;
	}
	.line-space {
		height: 24rpx;
	}

	.price-color {
		color: #0091FF;
	}

	.distance-color {
		color: #999999;
	}

	.row {
		display: flex;
		flex-direction: row;
	}

	.go-charging {

		background-color: #1DB3F3;
		width: 140rpx;
		height: 42rpx;
		border-radius: 42rpx;
		color: #FFFFFFFF;
		font-size: 36rpx;
		padding-left: 24rpx;
		padding-top: 6rpx;
		padding-bottom: 6rpx;
		margin-left: 48rpx;
	}

	.close {
		width: 60rpx;
		height: 60rpx;
		color: #FFFFFF;
		line-height: 60rpx;
		text-align: center;
		border-radius: 50%;
		border: 1px solid #FFFFFF;
		position: relative;
		bottom: -25%;
		left: 50%;
		transform: translate(-50%, -50%);
	}
</style>

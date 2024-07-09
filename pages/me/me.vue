<template>
	<view>
		<view class="center">
			<view v-if="nickName">
				<view>
					<image class="profilePhoto" src="../../static/me-profilePhoto.png"></image>
				</view>			
				<view>
					<text>呢称:{{nickName}}</text>
				</view>
			</view>
			<view v-else>
				<button @click="getUserInfo" >登录</button>
			</view>
			
			
		</view>
		<uni-list>
			<uni-list-item thumb="/static/me-point.png" :show-extra-icon="true" showArrow :extra-icon="extraIcon"
				title="我的积分" />
			<uni-list-item clickable @click="toMyWallet" thumb="/static/me-wallet.png" :show-extra-icon="true" showArrow :extra-icon="extraIcon"
				title="我的钱包" />
			<uni-list-item thumb="/static/me-order.png" :show-extra-icon="true" showArrow :extra-icon="extraIcon"
				title="我的订单" />
			<uni-list-item thumb="/static/me-coupon.png" :show-extra-icon="true" showArrow :extra-icon="extraIcon"
				title="优惠卷" />
			<uni-list-item clickable @click="toVehicleBind" thumb="/static/me-carAuth.png" :show-extra-icon="true" showArrow
				:extra-icon="extraIcon" title="车辆绑定" />
			<uni-list-item thumb="/static/me-settings.png" :show-extra-icon="true" showArrow :extra-icon="extraIcon"
				title="设置" />
		</uni-list>
		<cui-userprofiledialog ref="userProfileDialog" paddingBottom="92rpx"></cui-userprofiledialog>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				extraIcon: {
					color: '#4cd964',
					size: '22',
					type: 'gear-filled'
				},
				nickName:""
			}
		},
		onLoad() {
			let nickName=uni.getStorageSync("nickName")
			//debugger
			if (nickName){
				
				this.nickName=nickName
			}
		},
		methods: {
			"toVehicleBind":function(){
				uni.navigateTo({
					url:"/pages/vehicle-bind/vehicle-bind"
				})
			},
			"toMyWallet":function(){
				uni.navigateTo({
					url:"/pages/myWallet/myWallet"
				})
			},
			"getUserInfo": function() {
				this.$refs["userProfileDialog"].show({
					desc: "",
					avatarUrl: {
						requried: false, // 是否必填
						disable: true, // 是否隐藏
					}
				}).then(res => {
					console.log("结果！！！", res.avatarUrl, res.nickName)
					this.nickName=res.nickName
					uni.setStorageSync("nickName",this.nickName)
					
					//获取code,每次得到的都不一样。
					wx.login(
					{
						success:(res)=>{
							console.log(res)
							let code=res.code
							let url=this.$baseUrl+"/user_api/wx/login?code="+code+"&nickName="+this.nickName
							uni.request({
								url:url,
								success: (res) => {
									console.log("服务器端获取openid成功")
									console.log(res)
									let userId=res.data.data
									uni.setStorageSync("userId",userId)
								},
								fail: (res) => {
									console.log("服务器端获取openid失败")
									console.log(res)
								}
							})
						},
						fail: (res) => {
							console.log("获取id失败",res)
						}
					}
					)
					
				}, err => {
					console.log("取消")
				});
				
				
				
				
				
			},			

		}
	}
</script>

<style>
	.center {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		padding: 24rpx;
	}

	.profilePhoto {
		width: 132rpx;
		height: 132rpx;
		margin: 48rpx;
	}
</style>

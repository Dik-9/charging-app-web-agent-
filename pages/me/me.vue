<template>
	<view>
		<view class="center">
			<view v-if="nickName">
				<view>
					<image class="profilePhoto" src="../../static/me-profilePhoto.png"></image>
				</view>			
				<view class="nickname">
					<text>昵称: {{nickName}}</text>
				</view>
				<view class="logout-btn">
					<button @click="logout" size="mini" type="warn">退出登录</button>
				</view>
			</view>
			<view v-else class="login-btn">
				<button @click="getUserInfo" type="primary">登录</button>
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
				nickName: "",
				userId: ""
			}
		},
		onLoad() {
			this.loadUserInfo();
		},
		methods: {
			loadUserInfo() {
				this.nickName = uni.getStorageSync("nickName");
				this.userId = uni.getStorageSync("userId");
			},
			logout() {
				uni.showModal({
					title: '提示',
					content: '确定要退出登录吗？',
					success: (res) => {
						if (res.confirm) {
							// 清除本地存储的用户信息
							uni.removeStorageSync("nickName");
							uni.removeStorageSync("userId");
							
							// 重置页面数据
							this.nickName = "";
							this.userId = "";
							
							uni.showToast({
								title: '已退出登录',
								icon: 'success',
								duration: 2000
							});
						}
					}
				});
			},
			toVehicleBind() {
				uni.navigateTo({
					url:"/pages/vehicle-bind/vehicle-bind"
				})
			},
			toMyWallet() {
				uni.navigateTo({
					url:"/pages/myWallet/myWallet"
				})
			},
			getUserInfo() {
				this.$refs["userProfileDialog"].show({
					desc: "",
					avatarUrl: {
						requried: false,
						disable: true,
					}
				}).then(res => {
					this.nickName = res.nickName;
					let userId = uni.getStorageSync("userId");
					
					if(userId) {
						uni.setStorageSync("nickName", this.nickName);
					}
					
					wx.login({
						success: (res) => {
							let code = res.code;
							let url = this.$baseUrl + "/user_api/user/wx/login?code=" + code + "&nickName=" + this.nickName;
							uni.request({
								url: url,
								success: (res) => {
									let userId = res.data.data;
									uni.setStorageSync("userId", userId);
									uni.setStorageSync("nickName", this.nickName);
									this.userId = userId;
									
									uni.showToast({
										title: '登录成功',
										icon: 'success',
										duration: 2000
									});
								},
								fail: (res) => {
									console.log("服务器端获取openid失败", res);
									uni.showToast({
										title: '登录失败',
										icon: 'none',
										duration: 2000
									});
								}
							});
						},
						fail: (res) => {
							console.log("获取id失败", res);
							uni.showToast({
								title: '登录失败',
								icon: 'none',
								duration: 2000
							});
						}
					});
				}, err => {
					console.log("取消");
				});
			}
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
		background-color: #f8f8f8;
		border-radius: 16rpx;
		margin: 20rpx;
		box-shadow: 0 2rpx 10rpx rgba(0,0,0,0.1);
	}

	.profilePhoto {
		width: 132rpx;
		height: 132rpx;
		margin: 48rpx;
		border-radius: 50%;
		border: 2rpx solid #eee;
	}
	
	.nickname {
		margin: 20rpx 0;
		font-size: 32rpx;
		color: #333;
		font-weight: bold;
	}
	
	.login-btn button {
		width: 200rpx;
		margin: 30rpx 0;
	}
	
	.logout-btn {
		margin-top: 20rpx;
	}
	
	.logout-btn button {
		width: 200rpx;
	}
</style>
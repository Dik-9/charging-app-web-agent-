<template>
	<view class="device-width">
		<image src="/static/logo.jpg" class="logo-image"></image>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',

			}
		},
		onLoad() {

			setTimeout(function() {

				uni.switchTab({
					url: '/pages/nearbyStation/nearbyStation'
				})

			}, 1000)
		},
		methods: {

		}
	}
</script>

<style>
	.logo-image {
		height: 100vh;
		width: 750px;
	}

	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>

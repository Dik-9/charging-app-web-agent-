<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
	.device-width {
		max-width: 750px;
		margin: 0 auto;
	}
	
	.blue-btn {
		background-color: #1DB3F3;
		height: 96rpx;
		width: 600rpx;
		border-radius: 42rpx;
		color: #FFFFFFFF;
		font-size: 36rpx;
	
	}
</style>

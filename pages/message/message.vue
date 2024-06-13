<template>
	<view>
		<uni-list>
			<block v-for="message in messageList">

				<uni-list-item :title="message.title" :note="message.content" showArrow
					:thumb="messageIcon[message.type]" thumb-size="lg" />
			</block>
		</uni-list>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				messageList: [],
				messageIcon: ["/static/icon_charging_message.png", "/static/icon_charging_exception_message.png",
					"/static/icon_recharge_message.png"
				]
			}
		},
		onLoad() {
					
		},
		onShow() {
			console.log("onshow()")
			//debugger
			let str=uni.getStorageSync('messageList')
			let messageList = JSON.parse(str);
			this.messageList=messageList	
		},
		methods: {

		}
	}
</script>

<style>

</style>

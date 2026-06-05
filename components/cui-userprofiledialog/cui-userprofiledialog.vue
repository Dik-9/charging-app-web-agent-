<template>
	<div>
		<uni-popup ref="popup" type="bottom" background-color="white" @change="onPopupChange">
			<form @submit="onSubmit">
				<div class="header">{{title}}</div>
				<div v-if="options.desc" class="desc">{{options.desc}}</div>
				<div class="content">
					<view v-if="!options.avatarUrl.disable" class="section-line">
						<view class="section-line-title">头像</view>
						<button v-if="userNewUserProfileMethod" class="avatar-wrapper flex-cncc"
							@chooseavatar="tapAvatar" open-type="chooseAvatar">
							<image mode="aspectFit" class="avatar" :src="formData.avatarUrl"></image>
						</button>
						<button v-else class="avatar-wrapper flex-cncc" @click="tapAvatarByOldMethod">
							<image mode="aspectFit" class="avatar" :src="formData.avatarUrl"></image>
						</button>
					</view>
					<view v-if="!options.nickName.disable" class="section-line">
						<view class="section-line-title">昵称</view>
						<input class="section-line-inputText" type="nickname" name="nickName" placeholder="请输入昵称"
							v-model="formData.nickName" maxlength="16"></input>
					</view>
				</div>

				<div class="footer">
					<button class="btn cancel-btn" @click="tapCancel">取消</button>
					<button formType="submit" class="btn confirm-btn">确认</button>
				</div>
			</form>
			<div :style="{height:paddingBottom}"></div>
			<div class="iphoneX_bottom"></div>

		</uni-popup>
		<ImageCropper ref="imageCropper"></ImageCropper>

	</div>


</template>

<script>
	// import CosWrap from "@lib/cos/cos_wrap.js";
	import ImageCropper from "./ImageCropper/ImageCropper.vue";
	import UniPopup from "./uni-popup/uni-popup.vue";

	function deepMerge(target, other) {
		const targetToString = Object.prototype.toString.call(target);
		const otherToString = Object.prototype.toString.call(target);
		if (targetToString === "[object Object]" && otherToString === "[object Object]") {
			for (let [key, val] of Object.entries(other)) {
				if (!target[key]) {
					target[key] = val;
				} else {
					target[key] = deepMerge(target[key], val);
				}
			}
		} else if (targetToString === "[object Array]" && otherToString === "[object Array]") {
			for (let [key, val] of Object.entries(other)) {
				if (target[key]) {
					target[key] = deepMerge(target[key], val);
				} else {
					target.push(val);
				}
			}
		}
		return target;
	}
	/**
	 * 判断字符串是否为空
	 */
	function isEmptyStr(str) {
		return str == null || str.trim().length == 0;
	}

	export default {
		name: "UserProfileDialog",
		props: {
			paddingBottom: {
				type: String,
				default: "0rpx"
			},
		},
		components: {
			ImageCropper,
			UniPopup
		},
		data() {
			return {
				title: "请输入头像和昵称",
				confirmFunc: () => {},
				cancelFunc: () => {},
				cancel: false,
				formData: {
					avatarUrl: null,
					nickName: null
				},
				options: {
					desc: null, //描述
					nickName: {
						requried: true, // 是否必填
						disable: false // 是否隐藏
					},
					avatarUrl: {
						requried: true, // 是否必填
						disable: true // 是否隐藏
					}
				},
				userNewUserProfileMethod: true
			};
		},

		methods: {
			/**
			 * 
			 */
			show(options) {
				if (options) {
					deepMerge(this.options, options);
				}
				let titleEle = [];
				if (!this.options.avatarUrl.disable) {
					titleEle.push("头像")
				}
				if (!this.options.nickName.disable) {
					titleEle.push("昵称")
				}
				this.title = "请输入" + titleEle.join("和");

				this.cancel = false;
				return new Promise((resolve, reject) => {
					this.$refs["popup"].open();
					this.confirmFunc = resolve;
					this.cancelFunc = reject;
				});
			},


			tapAvatar(e) {
				this.$refs["imageCropper"].show({
					w: 128,
					h: 128,
					avatarUrl: e.detail.avatarUrl
				}).then(res => {
					let self = this;
					this.formData.avatarUrl = res.tempFilePath;

					console.log(res);
					// 上传到腾讯云存储
					// uni.showLoading({
					// 	title: '上传中'
					// });
					// let file = {
					// 	subKey: 'avatar/' + res.tempFilePath.substring(res.tempFilePath.lastIndexOf('/') + 1),
					// 	path: res.tempFilePath,
					// 	size: res.fileSize
					// };
					// CosWrap.postObject(file.subKey, file).then(cosRes => {
					// 	console.log('上传成功', cosRes); // 上传成功

					// 	uni.hideLoading();
					// 	this.formData.avatarUrl = cosRes.Location;

					// }, err => {
					// 	console.error(err);
					// 	uni.hideLoading();
					// 	Util.showError(err, "上传");
					// });
				})
			},

			tapAvatarByOldMethod() {
				uni.chooseImage({
					count: 1,
				}).then(res => {
					console.log(res);

					let tempFilePath = res.tempFilePaths[0];
					this.tapAvatar({
						detail: {
							avatarUrl: tempFilePath
						}
					})
				});
			},

			onSubmit(e) {
				// console.log("!!!!", this.options)
				// 通过微信api获取的昵称，没有进一步修改的情况下只能在form的submit回调中获得成功
				this.formData.nickName = e.detail.value.nickName;

				if (!this.formData.avatarUrl && !this.options.avatarUrl.disable && this.options.avatarUrl.requried) {
					uni.showToast({
						icon: "none",
						title: "请上传头像"
					})
					return;
				}
				// console.log(Util.isEmptyStr(this.formData.nickName), !this.options.nickName.disable, this.options.nickName
				// 	.requried)
				if (isEmptyStr(this.formData.nickName) && !this.options.nickName.disable && this.options.nickName
					.requried) {
					uni.showToast({
						icon: "none",
						title: "请输入昵称"
					})
					return;
				}
				let resultData = Object.assign({}, this.formData);

				this.confirmFunc(resultData);
				this.$refs["popup"].close();
			},

			close() {
				this.$refs["popup"].close();
			},
			tapCancel() {
				this.cancel = true;
				this.$refs["popup"].close();
			},
			onPopupChange(e) {
				if (!e.show) {
					this.cancelFunc(this.cancel ? "cancel" : "close");
				}
			},


		},
	};
</script>

<style lang="scss" scoped>
	.header {
		width: 100%;
		box-sizing: border-box;
		padding: 40rpx 32rpx 20rpx;
		text-align: center;
		font-size: 36rpx;
		font-weight: 600;
		color: #333;
	}

	.desc {
		width: 100%;
		box-sizing: border-box;
		text-align: center;
		color: #999;
		font-size: 26rpx;
		padding-bottom: 20rpx;
	}

	.content {
		width: 100%;
		padding: 0 32rpx;
		box-sizing: border-box;
	}

	.footer {
		width: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
		box-sizing: border-box;
		padding: 32rpx;
		gap: 32rpx;

		.btn {
			width: 260rpx;
			height: 88rpx;
			border-radius: 44rpx;
			font-size: 32rpx;
			font-weight: 500;
		}
	}

	.cancel-btn {
		background: #f5f5f5;
		color: #666;
		border: none;
		
		&:active {
			background: #e8e8e8;
		}
	}

	.confirm-btn {
		background: linear-gradient(135deg, #007AFF 0%, #0055CC 100%);
		color: #fff;
		border: none;
		box-shadow: 0 8rpx 20rpx rgba(0, 122, 255, 0.3);
		
		&:active {
			background: linear-gradient(135deg, #0066DD 0%, #0044AA 100%);
		}
	}

	.section-line {
		position: relative;
		display: flex;
		width: 100%;
		padding: 0;
		align-items: center;
		background-color: #f8f9fa;
		border-radius: 16rpx;
		padding: 0 24rpx;
		height: 100rpx;
		margin-bottom: 24rpx;
	}

	.section-line-title {
		font-size: 32rpx;
		color: #666;
		width: 120rpx;
		text-align: right;
		margin-right: 24rpx;
	}

	.section-line-inputText {
		font-size: 32rpx;
		height: 100%;
		text-align: left;
		line-height: 100rpx;
		background: transparent;
		flex: 1;
	}

	.avatar-wrapper {
		padding: 8rpx 0rpx;
	}

	.avatar {
		width: 100rpx;
		height: 100rpx;
		margin-left: 10rpx;
		box-sizing: border-box;
		border: 2rpx #ccc dashed;
	}

	button {
		padding: unset;
		background-color: transparent;
		border-radius: 0;
		border: none;
		box-sizing: unset;
		margin: unset;
		line-height: normal;
	}

	button::after {
		border: none;
		border-style: none;
		border-width: 0;
		border-radius: 0;
	}

	button text {
		line-height: 1;
	}

	.iphoneX_bottom {
		height: env(safe-area-inset-bottom);
	}

	.flex-cncc {
		display: flex;
		justify-content: center;
		align-items: center;
	}
</style>
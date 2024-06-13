# signature 获取昵称、头像的弹窗，适用最新微信小程序
> uniapp 获取昵称、头像弹窗，适用最新微信小程序，官方回收getUserProfile接口后，使用button的开放能力chooseAvatar替换获取头像和昵称。自带图片裁剪功能

## 平台兼容

| H5  | 微信小程序 | 支付宝小程序 | 百度小程序 | 头条小程序 | QQ 小程序 | App |
| --- | ---------- | ------------ | ---------- | ---------- | --------- | --- |
| 未测   | √          | 未测         | 未测       | 未测          | 未测      | 未测    |


## 代码演示

### 基本用法
```html
<view>
	<UserProfileDialog ref="userProfileDialog" paddingBottom="92rpx"></UserProfileDialog>
</view>
```

```js
export default {
	data() {
		return {
		}
	},
	methods: {
		tapGetUserProfile() {
			// 使用promise语法
			this.$refs["userProfileDialog"].show({
				desc: "用于显示个人资料", // 说明
				avatarUrl: {
					requried: true, // 是否必填
					disable: false, // 是否隐藏
				},
				nickName: {
					requried: true, // 是否必填
					disable: false, // 是否隐藏
				}
			}).then(res => {
				console.log("结果！！！", res.avatarUrl, res.nickName)
			}, err => {
				console.log("取消")
			});
		},
	}
}

```

### 插件标签
- 默认 UserProfileDialog 为 component
- cui-button 为 自定义的按钮组件，如果不需要可删掉自己写
- ImageCropper 为图片裁剪组件
## API
### Props

| 参数             | 说明                  | 类型              | 默认值        |
| --------------   | ------------         | ----------------  | ------------ |
| paddingBottom       | 下边距           | <em>String</em>   | `0rpx`       |


### 常见问题
- 依赖 uni-popup，为了方便使用打包进组件包里了，如果项目已经依赖了uni-popup，可以删掉以节约空间
- 组件内有个tapAvatar函数，注释掉的代码是把图片上传到腾讯云存储，建议可以换成自己的上传逻辑，返回的图片url就可以直接用了，否则返回的本地缓存地址

### 示例小程序
![](https://mp-eafdbd84-b470-4ced-b18c-57e5f0e4b9fd.cdn.bspapp.com/cloudstorage/a56b3042-3548-4e79-8ba4-2be8701d6b51.jpg)
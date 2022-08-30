<template>
	<view class="content">
	    <view class="list">
			<!-- 输入框(uni-app官网中提供的代码) -->
			<view class="uni-form-item uni-column">
				<view class="title">最大值</view>
				<input class="uni-input" v-model="max" type="number" placeholder="在此处输入最大值" />
			</view>
			<view class="uni-form-item uni-column">
				<view class="title">最小值</view>
				<input class="uni-input" v-model="min" type="number" placeholder="在此处输入最小值" />
			</view>
			<view class="uni-form-item uni-column">
				<view class="title">时间间隔(ms)</view>
				<input class="uni-input" v-model="interval" type="number" placeholder="在此处输入时间间隔" />
			</view>
	    </view>
		<view class="button-area">
			<button @click="onSave">保存</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				max:100,
				min:0,
				interval:100,
			}
		},
		// getter
		created(){
			let setting = uni.getStorageSync('setting') || {max: 100, min: 0, interval:100}
			this.max = setting.max
			this.min = setting.min
			this.interval = setting.interval
		},
		methods: {
			// 点击保存,将input值保存并同步到抽奖页面中  (setter)
			onSave(){
				let setting = {max: this.max, min: this.min, interval: this.interval}
				// 存储API  setStorageSync('key','value')
				uni.setStorageSync('setting',setting);
			}
		}
	}
</script>

<style lang="scss">
    .content{
		padding:40rpx;
	}
	.uni-form-item{
		margin-top: 20rpx;
	}
	.uni-input{
		border-bottom:1px solid $uni-border-color;
		font-size: 26rpx;
		margin-top: 16rpx;
	}
	.button-area{
		margin-top: 120rpx;
		display: flex;
		justify-content: center;
		button{
			width: 400rpx;
		}
	}
</style>

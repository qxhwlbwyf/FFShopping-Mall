<template>
	<view>
		<form>
			<view class="cu-form-group margin-top">
				<view class="title">收货人</view>
				<view class="uni-list-cell-db">
					<input v-model="address.shippingUser" placeholder="收货人名称" />
				</view>
				<input disabled="true"></input>
			</view>
			<view class="cu-form-group">
				<view class="title">联系电话</view>
				<input style="margin: 0 0;" placeholder="收货人联系电话" v-model="address.shippingPhone"></input>
				<!-- <input placeholder="电子邮箱" name="input" placeholder="12345678912"></input> -->
			</view>
			<view class="cu-form-group">
				<view class="title">所在地区</view>
				<input v-model="address.province" placeholder="省" /><text>-</text><input v-model="address.city" placeholder="市" /><text>-</text><input
				 v-model="address.district" placeholder="区县" />
			</view>
			<!-- <view class="cu-form-group">
				<view class="title">所在地区2</view>
				
			</view> -->
			<view class="cu-form-group">
				<view class="title">详细地址</view>
				<textarea auto-height="true" placeholder="地址详细到户" style="word-wrap: break-word;" v-model="address.address"></textarea>
			</view>
			<view class="cu-form-group">
				<view class="title">设置默认地址</view>
				<switch :checked="address.isDefault" @change="switch1Change" />
			</view>
		</form>
		<view class="button"  style="width: 100%; position: absolute;bottom: 100rpx;left: 0rpx;text-align: center;">
			<button style="width: 200rpx; background-color: #ff0000;border-radius: 35px;" @click="submitAddress">保存</button>
		</view>
	</view>
</template>

<script>
	import request from '../../util/request.js'
	export default {
		data() {
			return {
				address: {
					"address": "",
					"city": "",
					"customerId": '',
					"district": "",
					"id": '',
					"isDefault": '',
					"modifiedTime": "",
					"province": "",
					"shippingPhone": "",
					"shippingUser": ""
				},
				update:{
					"address": "",
					  "city": "",
					  "customerId": '',
					  "district": "",
					  "id": '',
					  "province": "",
					  "shippingPhone": "",
					  "shippingUser": ""
				}
			}
		},
		onShow() {
			try {
				this.address = uni.getStorageSync('address')
				console.log(this.address)
			} catch (error) {
				console.log(error)
			}
		},
		methods: {
			submitAddress:function(){
				this.update.address=this.address.address;
				this.update.city=this.address.city;
				this.update.customerId=this.address.customerId;
				this.update.district=this.address.district;
				this.update.id=this.address.id;
				this.update.province=this.address.province;
				this.update.shippingPhone=this.address.shippingPhone;
				this.update.shippingUser=this.address.shippingUser;
				console.log("+-+-+-+-+-+-+-+-+-+-+--++-+--+-+--")
				console.log(this.update)
				let ins={
					url:'/customer/addrs',
					method:'put'
				}
				let data=this.update;
				request.TokenRequest(ins,data).then(res=>{
					console.log(res.data)
					uni.showToast({
						title: res.message,
						duration: 2000
					});
					uni.navigateBack({
						delta:1
					})
				}).catch(error=>{
					console.log(error)
				});
			},
			switch1Change: function(e) {
				console.log('switch1 发生 change 事件，携带值为', e.target.value)
				let i;
				if (e.target.value == true || e.target.value == 'true') {
					i = 1;
				} else {
					i = 0;
				}
				this.address.isDefault = i;
				console.log(this.address.isDefault+"--------")
				let ins = {
					url: '/wx/customer/addrs',
					method: 'put'
				}
				let data = {
					"addrId": this.address.id,
					"customerId": this.address.customerId
				}
				console.log(data)
				request.TokenRequest(ins, data).then(res => {
					console.log(res.data)
				}).catch(error => {
					console.log(error)
				});
			}
		}
	}
</script>

<style>
	.cu-form-group .title {
		min-width: calc(4em + 15px);
	}
</style>

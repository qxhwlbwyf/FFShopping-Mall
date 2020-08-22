<template>
	<view>
		<view class="di">
			<view class="submit">
				<button class="cu-btn round bg-red" @click="submitOrder">+新建地址</button>
			</view>
		</view>
		<view v-if="flag" style="font-size: 40rpx;color: #DD514C;text-align: center;padding-top: 150rpx;">还没有收货地址！</view>
		<view v-else class="orderlist" v-for="(item,index) in addressList" :key=index style="padding-top: 100rpx;">
			<view class="orderitem" style="position: relative;">
				<view style="width: 100%;height: 8rpx;background-color: #e2e2e2;"></view>
				<view style="height:50rpx;font-size: 32rpx;">
					<text>{{item.shippingUser}}</text>
					<text style="margin-left:120rpx;position: absolute;left: 120rpx;top: 10rpx;">电话：{{item.shippingPhone}}</text>
					<image v-if="item.isDefault" src="../../static/icon/default.png" style="width: 50rpx;height: 50rpx;margin-left: 200rpx;position: absolute;left: 380rpx;top: 10rpx;"></image>
					<image src="../../static/icon/modify.png" @click="update(item)" style="width: 50rpx;height: 50rpx;margin-left: 200rpx;position: absolute;left: 450rpx;top: 10rpx;"></image>
				</view>
				<view style="margin: 30rpx 10rpx;font-size: 28rpx;height: 60rpx;">
					<text>{{item.province}}{{item.city}}{{item.district}}{{item.address}}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import request from '../../util/request.js'
	export default {
		data() {
			return {
				flag:true,
				message:'',
				addressList:[]
			}
		},
		onShow() {
			var that=this;
			var id;
			try {
			    let u = uni.getStorageSync('user');
			    if (u) {
					id = u.id;
			    }
			} catch (e) {
			    // error
				console.log("啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊")
				console.log(e)
			}
			let cartins = {
				url: '/customer/addrs/' + id,
				method: 'get'
			}
			request.TokenRequest(cartins).then(res => {
				that.message=res.message;
				// console.log(that.message+"----------------------------------");
				that.addressList=res.data;
				for(let i=0;i<that.addressList.length;i++){
					if(that.addressList[i].isDefault==1||that.addressList[i].isDefault=='1'){
						that.addressList[i].isDefault=true;
					}else{
						that.addressList[i].isDefault=false;
					}
				}
				// console.log(that.addressList)
				uni.setStorageSync("addressList",that.addressList);
				if(that.addressList.length>0){
					that.flag=false;
				}
			}).catch(error => {
				console.log(error)
			});
		},
		methods: {
			submitOrder:function(){
				uni.navigateTo({
					url:'../adressNew/adressNew'
				})
			},
			update:function(item){
				console.log("我要修改")
				console.log(item.isDefault)
				uni.setStorageSync('address',item)
				uni.navigateTo({
					url:'../upadteaddress/upadteaddress'
				})
			}
		}
	}
</script>

<style>
.di {
	display: inline-block;
		position: absolute;
		width: 100%;
		top: 20rpx;
		left: 0rpx;
	}
	.di .submit {
		font-size: 40rpx;
		color: #000000;
		text-align: center;
	}
</style>

<template>
	<view>
		<scroll-view :scroll-top="scrollTop" scroll-y="true" class="scroll-Y" @scrolltoupper="upper" @scrolltolower="lower"
		 @scroll="scroll">
		<view style="margin-top: 0rpx;height: 70rpx;line-height: 60rpx;font-size: 35rpx;background-color: #ffffff;">
			<text style="font-family:monospace;">订单编号：</text><text style="font-size: 30rpx;">{{order.id}}</text>
		</view>
		<view style="height: 5rpx;background-color: #e1f0eb;width: 100%;"></view>
		<view style="height: 120rpx;line-height: 60rpx;font-size: 33rpx;background-color: #ffffff; position: relative;">
			<view >
				<text>收货人信息：</text>
			</view>
			<view>
				<text>收货人：{{order.shippingUser}}</text>
				<text style="position: absolute;left: 300rpx;top: 60rpx;">电话：{{order.shippingPhone}}</text>
			</view>
		</view>
		<view style="height: 5rpx;background-color: #e1f0eb;width: 100%;"></view>
		<view style="height: auto;line-height: 60rpx;font-size: 33rpx;background-color: #ffffff; position: relative;">
			<view >
				<text>地址信息:</text>
			</view>
			<view>
				<text>收货地址：{{order.province}}-{{order.city}}-{{order.district}}-{{order.address}}</text>
			</view>
		</view>
		<view style="height: 5rpx;background-color: #e1f0eb;width: 100%;"></view>
		<view style="margin-top: 0rpx;height: 50rpx;line-height: 50rpx;font-size: 30rpx;background-color: #ffffff;">
			<text style="font-family:monospace;">下单时间：</text><text style="font-size: 28rpx;">{{order.createTime|OrderDate}}</text>
		</view>
		<view style="height: 5rpx;background-color: #e1f0eb;width: 100%;"></view>
		<view style="margin-top: 0rpx;font-size: 35rpx;background-color: #ffffff;padding-bottom: 160rpx;height: 1000rpx;">
			<text>商品信息:</text>
			<view style="height: 3rpx;background-color: #f2f8f6;width: 100%;"></view>
			<view v-for="(item,index) in order.items" :key="index">
				
				<view style="padding: 25rpx 5rpx;position: relative;" @click="navtoProduct(item)">
					<view>
						<image :src="item.image" style="width: 300rpx;height: 300rpx;"></image>
					</view>
					<view style="position: absolute;left: 320rpx;top: 25rpx;">	
						<view >
							<text style="font-size: 30rpx;">{{item.name}}</text>
						</view>
						<view style="padding-top: 50rpx;">
							<text style="font-size: 30rpx;">商品单价：{{item.price/100|numFilter}}</text>
						</view>
					</view>
				</view>
				<view style="height: 4rpx;background-color: #e1f0eb;width: 100%;"></view>
			</view>
			<view style="width: 100%;text-align: center;font-size: 20rpx;padding-top: 40rpx;">我是有底线的！</view>
		</view>
		
		<view class="cu-bar bg-white tabbar border shop">
			<view class="action">
				<view class="money" style="width: 100rpx;font-size: 28rpx;padding-left: 5rpx;">
					总价：
				</view>
				<view class="money" style="width: 100rpx;font-size: 30rpx;">
					{{order.orderMoney/100|numFilter}}
				</view>
			</view>
			<button class="cu-btn bg-white round " @click="createOrder">删除订单</button>
			<button class="cu-btn bg-blue round " @click="createOrder">申请售后</button>			
			<button class="cu-btn bg-red round " @click="createOrder">再次购买</button>		
		</view>
		
		</scroll-view>
	</view>
</template>

<script>
	import request from '../../util/request.js'
	export default {
		data() {
			return {scrollTop: 0,
				order:{}
			}
		},
		filters:{
			numFilter(value) {
				let realMon = '';
				if (!isNaN(value) && value != '') {
					realMon = '￥' + parseFloat(value).toFixed(2);
				} else {
					realMon = '淦';
				}
				return realMon;
			},
			OrderDate(date){
				const nDate=new Date(date);
				const year=nDate.getFullYear();
				const month=(nDate.getMonth()+1).toString().padStart(2,0)
				// .toString().padStart(2,0)
				const day=(nDate.getDay()+16).toString().padStart(2,0)
				// .toString().padStart(2,0)
				return year+'-'+month+'-'+day;
				
			}
		},
		onLoad(option) {
			var that=this;
			var pre = "http://123.57.7.108:81/";
			let ins={
				url:'/orders/one/'+option.id,
				method:'get'
			}
			request.TokenRequest(ins).then(res=>{
				console.log(res.data);
				that.order=res.data;
				for(let item of that.order.items){
					item.image=pre+item.image;
					console.log(item.image)
				}
			}).catch(error=>{
				console.log(error)
			})
		},
		methods: {
			navtoProduct:function(item){
				uni.navigateTo({
					url:'../message/message?id='+item.skuId
				})
			}
		}
	}
</script>

<style>
.cu-bar {
		position: fixed;
		bottom: 0rpx;
		left: 0rpx;
		width: 100%;
	}
</style>

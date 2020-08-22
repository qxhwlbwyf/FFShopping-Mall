<template>
	<view>
		<view v-if="flag" style="font-size: 40rpx;color: #DD514C;text-align: center;padding-top: 150rpx;">还没有订单！</view>
		<view v-else class="orderlist" v-for="(item,index) in orderList" :key=index>
			<view class="orderitem" style="position: relative;">
				<view style="height:50rpx;font-size: 32rpx;">
					<text>订单编号：{{item.id}}</text>
					<image src="../../static/icon/point.png" @click="update(item.id)" style="width: 40rpx;height: 40rpx;margin-left: 200rpx;position: absolute;left: 450rpx;top: 5rpx;"></image>
				</view>
				<view style="width: 100%;height: 3rpx;background-color: #eaeaea;"></view>
				<view style="height:50rpx;font-size: 32rpx;">
					<text style="margin-left:10rpx">收货人：{{item.shippingUser}}</text>
					<text style="margin-left:130rpx;position: absolute;left: 120rpx;top: 55rpx;">电话：{{item.shippingPhone}}</text>
					<!-- <image v-if="item.isDefault" src="../../static/icon/default.png" @click="update" style="width: 50rpx;height: 50rpx;margin-left: 200rpx;position: absolute;left: 380rpx;top: 10rpx;"></image> -->
					<!-- <image src="../../static/icon/modify.png" @click="update(item)" style="width: 50rpx;height: 50rpx;margin-left: 200rpx;position: absolute;left: 450rpx;top: 10rpx;"></image> -->
				</view>
				<view style="margin: 5rpx 10rpx;font-size: 28rpx;height: 50rpx;">
					<text>收货地址：{{item.province}}{{item.city}}{{item.district}}{{item.address}}</text>
				</view>
				<view style="margin: 5rpx 10rpx;font-size: 28rpx;height: 50rpx;">
					<text>下单时间：{{item.createTime|OrderDate}}</text>
					<text style="padding-left: 80rpx;">订单金额：{{item.orderMoney/100|numFilter}}</text>
				</view>
				<view style="width: 100%;height: 8rpx;background-color: #e2e2e2;"></view>
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
				orderList:[]
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
		onShow() {
			var that =this
			let id;
			try{
				let user=uni.getStorageSync("user")
				id=user.id;
			}catch(error){
				console.log(error)
			}
			
			let ins={
				url:'/orders/'+id,
				method:'get'
			}
			request.TokenRequest(ins).then(res=>{
				console.log(res.data)
				that.orderList=res.data;
				if(that.orderList.length>0){
					that.flag=false;
				}
			}).catch(error=>{
				console.log(error)
			})
			
		},
		methods: {
			update:function(id){
				uni.navigateTo({
					url:'../orderdetail/orderdetail?id='+id
				})
			}
		}
	}
</script>

<style>

</style>

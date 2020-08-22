<template>
	<view>
		<view class="uni-list-cell-db" style="margin-top:20rpx ;">
			<picker @change="bindPickerChange" :value="index" :range="array" range-key="zonggong">
				<view class="orderitem" style="position: relative;">
					<view style="height:50rpx;font-size: 32rpx;line-height: 50rpx;">
						<text style="padding-left:15rpx ;">{{array[index].shippingUser}}</text>
						<text style="margin-left:120rpx;position: absolute;left: 120rpx;top: 0rpx;">电话：{{array[index].shippingPhone}}</text>
						<image v-if="array[index].isDefault" src="../../static/icon/default.png" @click="update" style="width: 50rpx;height: 50rpx;margin-left: 200rpx;position: absolute;left: 380rpx;top: 10rpx;"></image>
						<!-- <image src="../../static/icon/modify.png" @click="update(array[index])" style="width: 50rpx;height: 50rpx;margin-left: 200rpx;position: absolute;left: 450rpx;top: 10rpx;"></image> -->
					</view>
					<view style="margin: 20rpx 10rpx;font-size: 30rpx;height: 100rpx;">
						<text>地址：{{array[index].province}}{{array[index].city}}{{array[index].district}}{{array[index].address}}</text>
					</view>
					<view style="width: 100%;height: 8rpx;background-color: #e2e2e2;"></view>
				</view>
			</picker>
		</view>
		<scroll-view :scroll-top="scrollTop" scroll-y="true" class="scroll-Y" @scrolltoupper="upper" @scrolltolower="lower"
		 @scroll="scroll">
			<view style="margin-top:10rpx ;">
				<view class="cartitem" v-for="(item,index) in prodlist" :key="item.id" style="height: 160rpx;position: relative;margin: 15rpx 0rpx;">
					<image :src="item.skuImage" style="width: 150rpx;height: 150rpx;display: inline-block;float: left;margin: 5rpx 30rpx;"></image>
					<view style="height:80rpx;font-size: 25rpx;">
						<text>{{item.skuName}}</text>
					</view>
					<view style="margin: 35rpx 0rpx;">
						<text>{{item.skuPrice/100|numFilter}}</text>
						<view class="xiao" >
							<text style="width: 100rpx ;text-align: center;">数量：{{item.skuCount}}</text>
							<text @click="reduce(index)" style="font-size: 30rpx;position: absolute;left: 130rpx;top: 0rpx;">[-]</text>
							<text @click="add(index)" style="font-size: 30rpx;position: absolute;left: 160rpx;top: 0rpx;">[+]</text>
						</view>
					</view>
				</view>
			</view>
			<view style="width: 100%;height: 8rpx;background-color: #e2e2e2;"></view>
			<view class="cu-list menu" :class="[menuBorder?'sm-border':'',menuCard?'card-menu margin-top':'']">
				<view class="cu-item" :class="menuArrow?'arrow':''">
					<view class="content">
						<text class="cuIcon-warn text-green"></text>
						<text class="text-grey">立减</text>
					</view>
					<view class="action">
						<text class="text-grey text-sm">小目标还没有实现！</text>
					</view>
				</view>
				<view class="cu-item" :class="menuArrow?'arrow':''">
					<view class="content">
						<text class="cuIcon-warn text-green"></text>
						<text class="text-grey">优惠券</text>
					</view>
					<view class="action">
						<text class="text-grey text-sm">小目标还没有实现！</text>
					</view>
				</view>
				<view class="cu-item" :class="menuArrow?'arrow':''">
					<view class="content">
						<text class="cuIcon-warn text-green"></text>
						<text class="text-grey">礼品卡</text>
					</view>
					<view class="action">
						<text class="text-grey text-sm">无可用！</text>
					</view>
				</view>
				<view class="cu-item" :class="menuArrow?'arrow':''">
					<view class="content">
						<text class="cuIcon-warn text-green"></text>
						<text class="text-grey">发票</text>
					</view>
					<view class="action">
						<text class="text-grey text-sm">小目标还没有实现！</text>
					</view>
				</view>
				<view class="cu-item" :class="menuArrow?'arrow':''">
					<view class="content">
						<text class="cuIcon-warn text-green"></text>
						<text class="text-grey">支付方式</text>
					</view>
					<view class="action">
						<text class="text-grey text-sm">小目标还没有实现！</text>
					</view>
				</view>
			</view>
		</scroll-view>


		<view class="cu-bar bg-white tabbar border shop">
			<view class="action">
				<view class="money" style="width: 400rpx;font-size: 30rpx;">
					总价：{{orderMoney/100|numFilter}}
				</view>
			</view>
			<button class="cu-btn bg-red round lg" @click="createOrder">立即下单</button>

		</view>
	</view>
</template>

<script>
	import request from '../../util/request.js'
	export default {
		data() {
			return {
				scrollTop: 0,
				menuBorder: false,
				menuArrow: false,
				menuCard: false,
				index: 0,
				array: [],
				prodlist: [],
				orderMoney: 0,
				orderinformation: {
					"address": "",
					"city": "",
					"customerId": '',
					"district": "",
					"items": [],
					"orderMoney":'',
					"province": "",
					"shippingMoney": '',
					"shippingPhone": "",
					"shippingUser": ""
				},
				deleteinf:{
					  "cartId": '',
					  "cartItemIds": []
				}				
			}
		},
		onShow() {
			var data;
			var bol;
			try{
				bol=uni.getStorageSync("bool")
			}catch(error){
				console.log(error)
			}
			try {
				this.array = uni.getStorageSync("addressList")
				if (this.array.length > 0) {
					console.log(this.array)
				} else {
					console.log("aaaaaaaa")
				}
				data = this.array;
				var zhuyi;
				for (let i = 0; i < data.length; i++) {
					if (data[i].isDefault == true || data[i].isDefault == 'true') {
						zhuyi = "detail";
					} else {
						zhuyi = "nodetail";
					}
					data[i].zonggong = zhuyi + "-" + data[i].shippingUser + "-" + data[i].shippingPhone + "-" + data[i].address
				}
				this.array = data;
			} catch (e) {
				console.log(e)
			}
			try {
				if(bol){
					let o=uni.getStorageSync("proList")
					this.prodlist.push(o);
				}else{
					this.prodlist=uni.getStorageSync("proList")
				}				
				console.log("this.prodlist")
				console.log(this.prodlist)
				for (let i = 0; i < this.prodlist.length; i++) {
					this.orderMoney = (this.prodlist[i].skuPrice * this.prodlist[i].skuCount) + this.orderMoney
				}
				// console.log(this.orderMoney)
			} catch (e) {
				console.log(e)
			}

		},
		filters: {
			numFilter(value) {
				let realMon = '';
				if (!isNaN(value) && value != '') {
					realMon = '￥' + parseFloat(value).toFixed(2);
				} else {
					realMon = '淦';
				}
				return realMon;
			}
		},
		methods: {
			// 减号操作
			reduce(index) {
				let num = this.prodlist[index].skuCount
				let n= this.prodlist[index].skuCount
				// 需要判断是否会减到0，我在这里是最小为1.
				if (num > 1) {
					num -= 1
				} else {
					num = 1
					return
				}
				let min=n-num;
				this.prodlist[index].skuCount = num;
				console.log(this.prodlist[index].skuCount + "啊啊啊啊");
				this.orderMoney=this.orderMoney-this.prodlist[index].skuPrice*min;
			},
			// 加号操作
			add(index) {
				let n= this.prodlist[index].skuCount;
				this.prodlist[index].skuCount += 1;
				let num = this.prodlist[index].skuCount
				let max=num-n;
				console.log(this.prodlist[index].skuCount + "啊啊啊啊");
				this.orderMoney=this.orderMoney+this.prodlist[index].skuPrice*max;
			},
			upper: function(e) {
				console.log(e)
			},
			lower: function(e) {
				console.log(e)
			},
			scroll: function(e) {
				console.log(e)
				this.old.scrollTop = e.detail.scrollTop
			},
			bindPickerChange: function(e) {
				console.log('picker发送选择改变，携带值为', e.target.value)
				this.index = e.target.value
			},
			createOrder:function(){
				var that=this;
				for(let item of this.prodlist){
					let {skuId,skuCount} = item
					this.orderinformation.items.push({skuId,skuCount})
				}
				var no=this.index;
				this.orderinformation.address=this.array[no].address;
				this.orderinformation.city=this.array[no].city;
				this.orderinformation.district=this.array[no].district;
				this.orderinformation.province=this.array[no].province;
				this.orderinformation.shippingUser=this.array[no].shippingUser;
				this.orderinformation.shippingPhone=this.array[no].shippingPhone;
				this.orderinformation.shippingMoney=this.orderMoney;
				this.orderinformation.orderMoney=this.orderMoney;
				this.orderinformation.customerId=this.array[no].customerId;
				console.log(this.orderinformation)
				try{
					this.deleteinf.cartId=uni.getStorageSync("cartId")
				}catch(error){
					console.log(error)
				}
				for(let item of this.prodlist){
					let id=item.id
					this.deleteinf.cartItemIds.push(id)
				}
				let ins={
					url:'/orders',
					method:'post'
				}
				let data=this.orderinformation;
				request.TokenRequest(ins,data).then(res=>{
					console.log(res.data)
					uni.showToast({
						title:res.message
					})
					let inf={
						url:'/cart/items',
						method:'delete'
					}
					let dta=that.deleteinf;
					request.TokenRequest(inf,dta).then(res=>{
						console.log(res.message)
						uni.switchTab({
							url:'../index/index'
						})
					}).catch(error=>{
						console.log(error)
					})
					
				}).catch(error=>{
					console.log(error)
					uni.showToast({
						title:error						
					})
				});
			}
		}
	}
</script>

<style>
	.xiao {
		position: absolute;
		top: 110rpx;
		left: 450rpx;
		height: 50rpx;
		line-height: 50rpx;
	}

	.cu-bar {
		position: fixed;
		bottom: 0rpx;
		left: 0rpx;
		width: 100%;
	}
</style>

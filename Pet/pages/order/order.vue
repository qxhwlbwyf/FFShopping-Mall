<template>
	<view>
		<view class="" v-if="flag">
			购物车空空如也，请<navigator open-type="switchTab" url="../index/index">先选购</navigator>
		</view>

		<view class="big" v-else>
			<view class="cartlist">
				<view class="cartitem" v-for="(item,index) in cartlist" :key="item.id" style="height: 160rpx;position: relative;">
					<checkbox-group style="float: left;line-height: 160rpx;" @change="selected(item)">
						<checkbox :checked="item.selected"></checkbox>
					</checkbox-group>
					<image :src="item.skuImage" style="width: 150rpx;height: 150rpx;display: inline-block;float: left;margin: 5rpx 20rpx;"></image>
					<view style="height:80rpx;font-size: 25rpx;">
						<text>{{item.skuName}}</text>
					</view>
					<view style="margin: 35rpx 0rpx;">
						<text>{{item.skuPrice/100|numFilter}}</text>
						<view class="xiao">
							<text @click="reduce(item)" style="font-size: 30rpx;padding-right: 23rpx;">[-]</text>
							<text style="width: 100rpx ;text-align: center;">{{item.skuCount}}</text>
							<text @click="add(item)" style="font-size: 30rpx;padding-left: 15rpx;position: absolute;left: 80rpx;top: 0rpx;">[+]</text>
							<text class="del" style="text-align: center;width: 70rpx;margin-left: 15rpx;border-style: outset;background-color: #DD524D;position: absolute;left: 150rpx;top: 0rpx;"
							 @click="del(item,index)">删除</text>
						</view>
					</view>
				</view>
			</view>
			<view class="di">
				<view class="quanxuan">
					<checkbox-group @change="selectedall()">
						<checkbox class="quanxuananniu" :checked="allchecked" />全选
					</checkbox-group>
				</view>
				<view class="numprice">
					<view class="num">
						总数:{{totalNum}}
					</view>
					<view class="price">
						总价:{{totalPrice/100|numFilter}}
					</view>
				</view>
				<view class="submit">
					<button class="cu-btn round bg-blue" type="primary" @click="submitOrder">提交订单</button>
				</view>
			</view>
		</view>

	</view>
</template>

<script>
	//因为他们的功能我们不能全用上，所以自己封装个简单的（可以不封装）
	// import {
	// 	request,
	// 	toast
	// } from '../../utils/index.js'
	import request from '../../util/request.js'
	export default {
		data() {
			return {
				flag: true, // 用于判断用户购物车是否有商品，没有商品为true，有商品为false
				cartlist: [], // 购物车商品信息
				allchecked: false,
				selectednum: 0,
				cartitem: {
					"cartId": '',
					"count": '',
					"skuId": ''
				},
				deleteitem: {
					"cartId": '',
					"cartItemIds": []
				},
				selecteditem: {
					"cartId": '',
					"cartItemIds": []
				}
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
		computed: {
			// 计算选中商品数量
			totalNum() {
				let totalNum = 0;
				this.cartlist.map(item => {
					item.selected ? totalNum += item.skuCount : totalNum += 0
				})
				return totalNum
			},
			//计算选中商品的总价
			totalPrice() {
				let totalPrice = 0;
				this.cartlist.map(item => {
					item.selected ? totalPrice += item.skuCount * item.skuPrice : totalPrice += 0
				})
				return totalPrice
			}
		},
		components: {

		},
		onShow() {
			var f;
			const _this = this
			var id;
			var num=0;
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
			var pre = "http://123.57.7.108:81/";
			let cartins = {
				url: '/cart/' + id,
				method: 'get'
			}
			request.TokenRequest(cartins).then(res => {
				_this.cartitem.cartId = res.data.id;
				_this.deleteitem.cartId = res.data.id;
				uni.setStorageSync("cartId",res.data.id);
				_this.selecteditem.cartId = res.data.id;
				_this.cartlist = res.data.items;
				for (let i = 0; i < _this.cartlist.length; i++) {
					_this.cartlist[i].skuImage = pre + _this.cartlist[i].skuImage;
					if (_this.cartlist[i].selected == 1 || _this.cartlist[i].selected == '1') {
						num++;
						_this.cartlist[i].selected = true;
						console.log("1")
					} else {
						_this.cartlist[i].selected = false;
						console.log("2")
					}
				}
				if(_this.cartlist.length==num){
					_this.allchecked=true;
				}
				if (_this.cartlist.length > 0) {
					f = false;
				} else {
					f = true;
				}
				_this.flag = f;
				// if(_this.allchecked){
				// 	_this.cartlist.map(item => {
				
				// 							item.selected = true
				
				// 						})
				// }else{
				// 	_this.cartlist.map(item => {
				// 		item.selected = false
				// 	})
				// }
			}).catch(error => {
				console.log(error)
			});
			console.log(this.flag)
			
		},
		methods: {
			submitOrder: function() {
				let bool=false;
				uni.setStorageSync("bool",bool);
				let orderList=[];
				for(let i=0;i<this.cartlist.length;i++){
					if(this.cartlist[i].selected){
						// console.log(this.cartlist[i])
						orderList.push(this.cartlist[i])
					}					
				}
				uni.setStorageSync("proList",orderList);
				uni.navigateTo({
					url:"../newOrder/newOrder"
				})
			},
			// 减号操作
			reduce(item) {
				let num = item.skuCount
				// 需要判断是否会减到0，我在这里是最小为1.
				if (num > 1) {
					num -= 1
				} else {
					num = 1
					return
				}
				item.skuCount = num;
				this.cartitem.count = item.skuCount;
				this.cartitem.skuId = item.skuId;
				let opts = {
					url: '/wx/cart',
					//接口地址（写你自己的地址）
					method: 'put'
					//请求类型
				}
				let data = this.cartitem;
				request.TokenRequest(opts, data).then(res => {
					let lscart = res.data.items;
					uni.removeStorage({
						key: 'cart',
						success: function(res) {
							console.log('+++++++++++++++//success///++++++++++++++++++++++');
						}
					});
					uni.showToast({
						title: res.message,
						duration: 2000
					});
					uni.setStorage({
						key: 'cart',
						data: res.data
					});
				}).catch(error => {
					console.log(error)
				});
				console.log(item.skuCount + "啊啊啊啊");
			},
			// 加号操作
			add(item) {
				item.skuCount += 1;
				this.cartitem.count = item.skuCount;
				this.cartitem.skuId = item.skuId;
				let opts = {
					url: '/wx/cart',
					//接口地址（写你自己的地址）
					method: 'put'
					//请求类型
				}
				let data = this.cartitem;
				request.TokenRequest(opts, data).then(res => {
					let lscart = res.data.items;
					uni.removeStorage({
						key: 'cart',
						success: function(res) {
							console.log('+++++++++++++++//success///++++++++++++++++++++++');
						}
					});
					uni.showToast({
						title: res.message,
						duration: 2000
					});
					uni.setStorage({
						key: 'cart',
						data: res.data
					});
				}).catch(error => {
					console.log(error)
				});
				console.log(item.skuCount + "啊啊啊啊");
			},
			// 删除单挑购物车商品
			del(item, index) {
				this.deleteitem.cartItemIds.push(item.id)
				let ins = {
					url: '/cart/items',
					method: 'delete'
				}
				let data = this.deleteitem;
				request.TokenRequest(ins, data).then(res => {
					let lscart = res.data.items;
					uni.removeStorage({
						key: 'cart',
						success: function(res) {
							console.log('+++++++++++++++//success///++++++++++++++++++++++');
						}
					});
					uni.showToast({
						title: res.message,
						duration: 2000
					});
					uni.setStorage({
						key: 'cart',
						data: res.data
					});
				}).catch(error => {
					console.log(error)
				});
				console.log(index);
				this.cartlist.splice(index, 1)
				if (this.cartlist.length === 0) {
					this.flag = true
				}
			},
			// 单个商品前的勾选
			selected(item) {
				console.log(item.id)
				var that = this;
				var num = 0;
				var b=item.id;
				var a=false;
				if(this.selecteditem.cartItemIds.length>0){
					for (let i=0;i<this.selecteditem.cartItemIds.length;i++) {
						if(this.selecteditem.cartItemIds[i]==b){
							console.log("啊啊啊啊啊啊")
							this.selecteditem.cartItemIds.splice(i)
							item.selected=false
							a=true;
						}
					}
					if(a){
						that.allchecked = false;
						return
					}
					this.selecteditem.cartItemIds.push(item.id)
				}else{
					this.selecteditem.cartItemIds.push(item.id)
				}								
				let opts = {
					url: '/wx/cart/items',
					method: 'put'
				};
				let data = this.selecteditem;
				console.log(this.selecteditem)
				request.TokenRequest(opts, data).then(res => {
					that.cartlist = res.data.items;

					for (let i = 0; i < that.cartlist.length; i++) {

						that.cartlist[i].skuImage = "http://123.57.7.108:81/" + that.cartlist[i].skuImage;

						if (that.cartlist[i].selected == 1 || that.cartlist[i].selected == '1') {
							num++;
							that.cartlist[i].selected = true;
							if (num == that.cartlist.length) {
								console.log("//////////////////////////////")
								that.allchecked = true;
								console.log(that.allchecked)
							} else if (num < that.cartlist.length) {
								console.log("、、、、、、、、、、、、、、、、、")
								that.allchecked = false;
								console.log(that.allchecked)
							}
						} else {
							that.cartlist[i].selected = false;
						}
					}
				}).catch(error => {
					console.log(error)
				});
			},
			// 全选按钮
			selectedall() {
				this.allchecked = !this.allchecked
				console.log(this.allchecked)
				if (this.allchecked) {
					this.cartlist.map(item => {
						item.selected = true
					})
				} else {
					this.cartlist.map(item => {
						item.selected = false
					})
				}
			}

		}
	}
</script>

<style lang="scss">
	.cartlist {
		padding-top: 85rpx;
		background-color: #e6e6e6;
		width: 100%;
		min-height: 300rpx;
		// padding: 10px;

		.cartitem {
			background-color: #FFFFFF;
			height: 50px;
			margin-bottom: 10px;

			.xuanzhong {
				float: left;
				line-height: 50px;
				padding-left: 5px;
			}

			image {
				display: inline-block;
				height: 40px;
				width: 40px;
				padding: 5px 0;
				float: left;
			}

			.itemright {
				display: inline-block;
				padding: 5px;

				.del {
					margin-left: 10px;
					background-color: red;
					color: #FFFFFF;
					border-radius: 3px;
					padding: 0 5px;
				}
			}
		}

	}

	.xiao {
		position: absolute;
		top: 100rpx;
		left: 450rpx;
		height: 50rpx;
		line-height: 50rpx;
	}

	.di {

		position: absolute;
		top: 0rpx;
		left: 10rpx;
	}

	.di .quanxuan {
		margin-top: 10rpx;
		float: left;
	}

	.di .numprice {
		float: left;
		margin-left: 30rpx;
	}

	.di .submit {
		width: 200rpx;
		height: 40rpx;
		font-size: 40rpx;
		float: left;
		position: absolute;
		left: 500rpx;
		top: 10rpx;
	}
</style>

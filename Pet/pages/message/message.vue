<template>
	<view>
		<scroll-view scroll-y="true">
			<view style="width: 100%;height: 700rpx;">
				<swiper class="screen-swiper" :class="dotStyle?'square-dot':'round-dot'" :indicator-dots="false" :circular="true"
				 :autoplay="true" indicator-color="#8799a3" indicator-active-color="#0081ff" style="width: 100%;height: 700rpx;">
					<swiper-item v-for="(item,index) in product.skus[0].images" :key="index" :class="cardCur==index?'cur':''" style="width: 100%;height: 500rpx;">
						<view class="productitemimage" style="width: 100%;height: 700rpx;" @click="watchimg(index)">
							<image :src="item.largeImage" mode="scaleToFill"></image>
						</view>
					</swiper-item>
				</swiper>
			</view>
			<view class="box1">
				<view class="price">
					<text>{{product.skus[0].price/100|numFilter}}</text>
					<text>折扣</text>
				</view>
				<view class="name">
					<text>{{product.skus[0].name}}</text>
				</view>
			</view>
			<view class="line" style="width: 100%;height: 8rpx;background-color: #eaeaea;"></view>
			<view class="box2">
				<text>货号:{{product.skus[0].code}}</text>
				<text style="display: block;">库存:{{product.skus[0].count}}</text>
			</view>
			<view class="line" style="width: 100%;height: 8rpx;background-color: #eaeaea;"></view>
			<view class="box3" style="margin-bottom: 50rpx;">
				<text style="font-size: 32rpx;margin-left: 15rpx;padding: 20rpx 0rpx;">详细内容</text>
				<view class="information">
					<view class="" v-for="item in specGroups" style="font-size: 30rpx;color: #E54847;margin-left: 20rpx;">
						{{item.group}}
						<view class="" v-for="te in item.specsList" style="font-size: 28rpx;color: #000000;margin-left: 40rpx;">
							{{te.name}}:{{te.value}}
						</view>
					</view>
				</view>
				<view v-for="(item,index) in swiperList" :key="index" style="width: 100%;height: auto;" @click="seeimg(index)">
					<image :src="item" style="width: 100%;height: 400rpx;"></image>
				</view>

			</view>
		</scroll-view>
		<view class="nav">
			<uni-goods-nav :fill="true" :options="options" :buttonGroup="buttonGroup" @click="onClick" @buttonClick="buttonClick" />
		</view>
	</view>
</template>

<script>
	import uniGoodsNav from '@/components/uni-goods-nav/uni-goods-nav.vue'
	import request from '../../util/request.js'
	export default {
		data() {
			return {
				id: '',
				dotStyle: true,
				cardCur: '',
				swiperList: [],
				specGroups: [],
				product: {},
				cartitem: {
					"cartId": '',
					"count": 1,
					"skuId": ''
				},
				prodlist:{
					skuImage:'',
					skuName:'',
					skuPrice:'',
					skuCount:1,
					skuId:'',
					id:''
				},
				options: [{
					icon: 'headphones',
					text: '客服'
				}, {
					icon: 'shop',
					text: '店铺',
					info: 2,
					infoBackgroundColor: '#007aff',
					infoColor: "red"
				}, {
					icon: 'cart',
					text: '购物车',
					info: ''
				}],
				buttonGroup: [{
						text: '加入购物车',
						backgroundColor: '#ff0000',
						color: '#fff'
					},
					{
						text: '立即购买',
						backgroundColor: '#ffa200',
						color: '#fff'
					}
				]
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
		onLoad(Option) {
			var that = this;
			this.id = Option.id;
			let ins = {
				url: '/skudetails/' + this.id,
				method: 'get'
			}
			request.TokenRequest(ins).then(res => {
				that.product = res.data;
				console.log("-------------------------------")
				console.log(that.product.skus[0]);
				that.specGroups = res.data.skus[0].specGroups;
				var pre = "http://123.57.7.108:81/";
				for (let i = 0; i < that.product.skus[0].images.length; i++) {
					that.product.skus[0].images[i].image = pre + that.product.skus[0].images[i].image;
					that.product.skus[0].images[i].largeImage = pre + that.product.skus[0].images[i].largeImage;
				}
				that.cartitem.skuId = that.product.skus[0].id;
				let arr = [];
				for (let i = 0; i < 4; i++) {
					arr.push(pre + res.data.images[i]);
				}
				this.swiperList = arr;
			}).catch(error => {
				console.log(error)
			});
			console.log("/*/-++6+++++++++++++++++++++++++++++++++++")
			try {
			    let hjrr = uni.getStorageSync('cart');
			    if (hjrr) {
					that.cartitem.cartId = hjrr.id;
					that.options[2].info = hjrr.items.length;
					
			    }
			} catch (e) {
			    // error
				console.log("啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊")
				console.log(e)
			}
		},
		methods: {
			cardSwiper(e) {
				this.cardCur = e.detail.current
			},
			watchimg: function(current) {
				const urls = this.product.skus[0].images.map(item => {
					return item.largeImage;
				})
				console.log(urls);
				uni.previewImage({
					current,
					urls
				})
			},
			seeimg(current) {
				const urls = this.swiperList.map(item => {
					return item;
				})
				console.log(urls);
				uni.previewImage({
					current,
					urls
				})
			},
			onClick(e) {
				// uni.showToast({
				// 	title: `点击${e.content.text}`,
				// 	icon: 'none'
				// })
				if (e.content.text === "购物车") {
					uni.switchTab({
						url: '../order/order'
					})
				} else {
					// ------------------------------------------------------------------------------
					console.log("youqianzhenh ")
				}

			},
			buttonClick(e) {
				var that = this;
				console.log(e.content.text);
				if (e.content.text === "加入购物车") {
					let opts = {
						url: '/wx/cart',
						//接口地址（写你自己的地址）
						method: 'put'
						//请求类型
					}
					let data = that.cartitem;
					console.log(that.cartitem)
					request.TokenRequest(opts, data).then(res => {
						console.log(res.data.items)
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
						that.options[2].info++;
					}).catch(error => {
						console.log(error)
					});
				} else {
					this.prodlist.skuId=this.product.skus[0].id;
					this.prodlist.skuImage=this.product.skus[0].images[0].image;
					this.prodlist.skuName=this.product.skus[0].name;
					this.prodlist.skuPrice=this.product.skus[0].price;
					this.prodlist.id=this.product.id;
					let bool=true;
					uni.setStorageSync("bool",bool);
					uni.setStorageSync("proList",this.prodlist);
					uni.navigateTo({
						url:"../newOrder/newOrder"
					})
				}

			}
		},
		components: {
			uniGoodsNav
		}
	}
</script>

<style lang="scss">
	.box1 {
		margin-top: 5rpx;
		padding: 10px;

		.price {
			font-size: 35rpx;
			color: #DD514C;
			line-height: 80rpx;

			text:nth-child(2) {
				color: #ac9da9;
				font-size: 28rpx;
				text-decoration: line-through;
				margin-left: 30rpx;
			}
		}

		.name {
			font-size: 32rpx;
			line-height: 60rpx;
		}
	}

	.box2 {
		padding: 0px 10px;
		font-size: 28rpx;
		line-height: 60rpx;
	}

	.nav {
		position: fixed;
		bottom: 0;
		width: 100%;
	}
</style>

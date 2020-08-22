<template>
	<view>
		<view class="cu-bar search bg-white">
			<view class="search-form round">
				<text class="cuIcon-search"></text>
				<input @focus="InputFocus" @blur="InputBlur" :adjust-position="false" type="text" placeholder="搜索图片、文章、视频"
				 confirm-type="search" v-model="selecttext"></input>
			</view>
			<view class="action">
				<button class="cu-btn bg-green shadow-blur round" @click="select">搜索</button>
			</view>
		</view>
		<view class="mainview">
			<view class="imgview">
				<swiper class="card-swiper" :class="dotStyle?'square-dot':'round-dot'" :indicator-dots="true" :circular="true"
				 :autoplay="true" interval="5000" duration="500" @change="cardSwiper" indicator-color="#8799a3"
				 indicator-active-color="#0081ff">
					<swiper-item v-for="(item,index) in swiperList" :key="index" :class="cardCur==index?'cur':''">
						<view class="swiper-item" @click="clickImg(item)">
							<image :src="item.url" mode="aspectFill" v-if="item.type=='image'"></image>
							<!-- <video :src="item.url" autoplay loop muted :show-play-btn="false" :controls="false" objectFit="cover" v-if="item.type=='video'"></video> -->
						</view>
					</swiper-item>
				</swiper>
			</view>
			<scroll-view scroll-y="true">
				<view class="trade">
					<view class="texts" :class="curr==0?'active':''" data-index="0" @tap="setCurr">
						推荐商品
					</view>
					<view class="texts" :class="curr==1?'active':''" data-index="1" @tap="setCurr">
						宠物寄养
					</view>
					<view class="texts" :class="curr==2?'active':''" data-index="2" @tap="setCurr">
						宠物护理
					</view>
					<view class="text4" @tap="gotolist">
						商品列表
					</view>
				</view>
				<swiper :style="{height:swiperheight+'px'}" style="margin-top: 20rpx;" :current="curr" @change="setCurr">
					<swiper-item style="overflow: scroll;">
						<scroll-view scroll-y>
							<view class="ju shoplist">
								<block v-for="(shop,index) in shops" :key="shop.id">
									<productlist :shop="shop" :index="index" @goitemmessage="gomessage(shop.id)"></productlist>
								</block>
							</view>
						</scroll-view>
					</swiper-item>
					<swiper-item>
						<scroll-view>
							<view>
								<text>咋可能呢弟兄，我做着玩，没有店...</text>
							</view>
						</scroll-view>
					</swiper-item>
					<swiper-item>
						<scroll-view>
							<view>
								<text>咋可能呢弟兄，我做着玩，没有店...</text>
							</view>
						</scroll-view>
					</swiper-item>
				</swiper>
			</scroll-view>
		</view>



	</view>
</template>

<script>
	import productlist from '../../components/productlist/productlist.vue'
	import request from '../../util/request.js'
	export default {
		data() {
			return {
				swiperheight: 100,
				selecttext: "",
				swiperList: [{
					id: 0,
					type: 'image',
					url: 'https://img.alicdn.com/tfs/TB1cxAyRXY7gK0jSZKzXXaikpXa-520-280.jpg_q90_.webp'
				}, {
					id: 1,
					type: 'image',
					url: 'https://aecpm.alicdn.com/simba/img/TB1XotJXQfb_uJkSnhJSuvdDVXa.jpg',
				}, {
					id: 2,
					type: 'image',
					url: 'https://gw.alicdn.com/imgextra/i1/952917/O1CN01iVH3pY1XQ1viW7yeG_!!952917-0-lubanu.jpg'
				}, {
					id: 3,
					type: 'image',
					url: 'https://img.alicdn.com/tfs/TB1Va9UriMnBKNjSZFzXXc_qVXa-800-300.jpg'
				}],
				shops: [],
				dotStyle: true,
				curr: 0,
				cardCur: '',
				querylist: {
					pageNum: 1,
					pageSize: 50,
					query: ' '
				}
				// TabCur: 0,
				// scrollLeft: 0
			}
		},
		onLoad() {
			uni.getSystemInfo({
				success: (res) => {
					let height = res.windowHeight - uni.upx2px(100)
					this.swiperheight = height;
				}
			})
		},
		
		onShow() {
			var that = this;
			let ins = {
				url: '/skus',
				method: 'get'
			};
			let data = this.querylist;
			request.TokenRequest(ins, data).then(res => {
				that.shops = res.data.list;
				for (let i = 0; i < that.shops.length; i++) {
					that.shops[i].image = "http://123.57.7.108:81/" + that.shops[i].image;
				}
			}).catch(error => {
				console.log(error)
			});

		},
		components: {
			productlist
		},
		methods: {
			gotolist:function(){
				uni.navigateTo({
					url:"../List/List"
				})
			},
			//跳转页面
			gomessage: function(id) {
				uni.navigateTo({
					url: "../message/message?id=" + id
				})
			},
			
			select() {
				var that = this;
				console.log(this.selecttext)
				this.querylist.query=this.selecttext;
				let ins = {
					url: '/skus',
					method: 'get'
				};
				let data = this.querylist;
				request.TokenRequest(ins, data).then(res => {
					that.shops = res.data.list;
					for (let i = 0; i < that.shops.length; i++) {
						that.shops[i].image = "http://123.57.7.108:81/" + that.shops[i].image;
					}
				}).catch(error => {
					console.log(error)
				});
			},
			cardSwiper(e) {
				this.cardCur = e.detail.current
			},
			setCurr(e) {
				console.log(e.detail.current)
				let thisCurr = e.detail.current || e.currentTarget.dataset.index || 0;
				console.log(thisCurr)
				this.curr = thisCurr;
			},
			InputFocus() {
				console.log("lalla")
			},
			InputBlur() {
				uni.hideKeyboard();
				console.log("aaaaa")
			},
			clickImg(num) {
				console.log(num.id)
			}
		}
	}
</script>

<style>
	.ju {
		display: flex;
		flex: 1;
		flex-wrap: wrap;
		justify-content: space-around;
	}

	.shoplist {
		width: 100%;
	}

	.search {
		z-index: 999;
		background-color: #007AFF;
	}

	.trade {
		width: 1000rpx;
		color: #5c04c7;
		overflow: auto;
	}

	.trade view {
		text-align: center;
		margin-left: 30upx;
		width: 160rpx;
		font-size: 30rpx;
		float: left;
	}
	.text4{
		border:1px solid #F37B1D;
	}
	.trade .texts.active {
		border-bottom: 8upx solid #F0AD4E;
	}

	.imgview {
		/* background-color: #007AFF; */
	}
</style>

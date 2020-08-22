<template>
	<view>
		<scroll-view scroll-y>
			<view class="ju shoplist">
				<block v-for="(shop,index) in shops" :key="shop.id">
					<productlist :shop="shop" :index="index" @goitemmessage="gomessage(shop.id)"></productlist>
				</block>
			</view>
		</scroll-view>
		<view class="isOver" v-if="flag">
			-------我是有底线的-------
		</view>
	</view>
</template>

<script>
	import productlist from '../../components/productlist/productlist.vue'
	import request from '../../util/request.js'
	export default {
		data() {
			return {
				flag:false,
				shops: [],
				querylist: {
					pageNum: 1,
					pageSize: 50,
					query: ' '
				}
			}
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
		onPullDownRefresh(){
			console.log("下拉刷新***********");
			this.shops=[];
			this.flag=false;
			var that = this;
			setTimeout(()=>{
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
					if(that.shops.length>0){
						uni.stopPullDownRefresh();
					}
				}).catch(error => {
					console.log(error)
				});
				
			},1000);
		},
		onReachBottom() {
			var that = this;
			this.querylist.pageNum++;
			if(this.querylist.pageNum>5){
				this.flag=true;
				return;
			}
			let ins = {
				url: '/skus',
				method: 'get'
			};
			let data = this.querylist;
			request.TokenRequest(ins, data).then(res => {
				that.shops=[...that.shops,...res.data.list];
				for (let i = 0; i < that.shops.length; i++) {
					that.shops[i].image = "http://123.57.7.108:81/" + that.shops[i].image;
				}
				// if(!res.data.list.length>0){
				// 	that.flag=true;
				// }
			}).catch(error => {
				console.log(error)
			});
		},
		methods: {
			gomessage: function(id) {
				uni.navigateTo({
					url: "../message/message?id=" + id
				})
			}
			
		},
		components:{
			productlist
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
	.isOver{
		width: 100%;
		height: 60rpx;
		line-height: 60rpx;
		text-align: center;
		font-size: 40rpx;
		background-color: #e7e7e7;
	}
</style>

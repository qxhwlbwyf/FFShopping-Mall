<template>
	<view>
		<view class="cu-bar bg-black search">
			<view>
				<text></text>
			</view>
			<view class="action">
				<text class="cuIcon-scan" @click="scanimg"></text>
			</view>
		</view>
		<view class="header">
			<view class="headtop">
				<image v-for="item in Img" :src="item" @click="changePicture" class="head"></image> 
				<text class="name"  @click="changeName">{{name}}</text>
				<text class="btn" @click="userlogin">登录></text>
			</view>
			<template class="grid0">
				<uni-grid class="grid1" :column="3" :show-border="true" :square="false">
					<uni-grid-item>
						<text class="text">动态</text>
						<text>1</text>
					</uni-grid-item>
					<uni-grid-item>
						<text class="text">关注</text>
						<text>1</text>
					</uni-grid-item>
					<uni-grid-item>
						<text class="text">粉丝</text>
						<text>1</text>
					</uni-grid-item>
				</uni-grid>
			</template>
		</view>
		<view>
			<scroll-view :scroll-top="scrollTop" scroll-y="true" class="scroll-Y" @scrolltoupper="upper" @scrolltolower="lower"
			 @scroll="scroll">
				<view><text style="font-size: 30rpx;color:#000000;font-weight:bold">更多服务</text></view>
				<view id="demo3" class="scroll-view-item uni-bg-blue grid2">
					<template>
						<uni-list>
							<uni-list-item title="我的订单" thumb="../../static/icon/Myorder.png" clickable="true" @click="onClick" ></uni-list-item>
						</uni-list>
						<uni-list>
							<uni-list-item title="我的收藏" thumb="../../static/icon/Mycollection.png" clickable="true" @click="noMessage"></uni-list-item>
						</uni-list>
						<uni-list>
							<uni-list-item title="我的卡包" thumb="../../static/icon/Mycard.png" clickable="true" @click="noMessage"></uni-list-item>
						</uni-list>
						<uni-list>
							<uni-list-item title="地址管理" thumb="../../static/icon/Address.png" clickable="true" @click="clickaddress" ></uni-list-item>
						</uni-list>
						<uni-list>
							<uni-list-item title="绑定设备" thumb="../../static/icon/binding.png" clickable="true" @click="noMessage"></uni-list-item>
						</uni-list>
						<uni-list>
							<uni-list-item title="邀请有礼" thumb="../../static/icon/invitation.png" clickable="true" @click="noMessage"></uni-list-item>
						</uni-list>
					</template>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	import uniGrid from "@/components/uni-grid/uni-grid.vue"
	import uniGridItem from "@/components/uni-grid-item/uni-grid-item.vue"
	import uniList from "@/components/uni-list/uni-list.vue"
	import uniListItem from "@/components/uni-list-item/uni-list-item.vue"
	export default {
		data() {
			return {
				Img: ["../../static/icon/111.jpg"],
				name:"FF商城用户",
				scrollTop: 0
			}
		},
		created() {
			this.init();
		},
		components: {
			uniGrid,
			uniGridItem,
			uniList,
			uniListItem
		},
		methods: {
			init() {
				console.log(this.Img);
			},
			noMessage(){
				uni.showToast({
					title:"仅供学习，还未实现..."
				})
			},
			userlogin(){
				uni.navigateTo({
					url:'../login/login'
				})
			},
			changePicture() {
				uni.chooseImage({
					sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
					sourceType: ['album'], //从相册选择
					success: res => {
						console.log(JSON.stringify(res.tempFilePaths));
						this.Img = [];
						this.Img = res.tempFilePaths;
						console.log(this.Img);
					}
				});
			},
			changeName() {
				console.log("点解名称了");
				uni.navigateTo({
					url: "../message/message"
				});
			},
			onClick(){
				uni.navigateTo({
					url:'../myorder/myorder'
				})
			},
			clickaddress(){
				uni.navigateTo({
					url:'../myaddress/myaddress'
				})
			}
		}
	}
</script>

<style>
	.navbar {
		height: 50rpx;
		line-height: 50rpx;
	}

	.header {
		/* 
		margin-top: 50rpx; */
		height: 230rpx;
		width: 750rpx;
	}

	.headtop {
		margin-top: 10rpx;
		height: 130rpx;
	}

	.head {
		float: left;
		margin-left: 35rpx;
		height: 100rpx;
		width: 100rpx;
		border-radius: 100%;
	}

	.name {
		float: left;
		height: 100rpx;
		line-height: 100rpx;
		margin-left: 35rpx;
	}

	.btn {
		color: #000000;
		float: left;
		height: 100rpx;
		line-height: 100rpx;
		margin-left: 320rpx;
	}

	.grid1 {
		text-align: center;
	}

	.grid {
		height: 110rpx;
		text-align: center;
	}

	.grid2 {
		margin-top: 20rpx;
		height: 800rpx;
	}
</style>

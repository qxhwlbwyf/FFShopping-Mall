<template>
	<view>
		<view class="VerticalBox">
			<scroll-view class="VerticalNav nav" show-scrollbar=false scroll-y :scroll-top="verticalNavTop" style="height:calc(200vh - 375upx);background-color: #007AFF;width: 300rpx;">
				<view class="cu-item" :class="index==tabCur?'text-green cur':''" v-for="(item,index) in list" :key="index" @tap="TabSelect"
				 :data-id="index">
					{{item.name}}
				</view>
			</scroll-view>
			<view class='right'>
				<scroll-view scroll-y="true" class="scroll" @scroll="scroll" :scroll-into-view="posi" :scroll-top="scrollTop">
					<view class="bg-white padding" style="height:calc(200vh - 375upx) ;">
						<view >
							<view v-for="(item,index) in list[mainCur].children" :key="index"  style="height: auto;width: 100%;font-size: 35rpx;font-family: serif;color: #333333;">
								{{item.name}}
								<view style="width: 100%;height: 5rpx;background-color: #e7e7e7;"></view>
								 <view v-for="(na,ind) in item.children" :key="ind">
									<text class="text" style="height: 60rpx;line-height: 60rpx;font-size: 30rpx;padding-left: 50rpx;font-family:Georgia, 'Times New Roman', Times, serif;color: #000000;">{{na.name}}</text>
								 </view> 
							</view>
						</view>
					</view>
				</scroll-view>
			</view>
		</view>
	</view>
</template>

<script>
	import request from '../../util/request.js'
	export default {
		data() {
			return {
				avatar: ['../../static/icon/Scan.png',
					'../../static/icon/my-select.png',
					'https://ossweb-img.qq.com/images/lol/web201310/skin/big25002.jpg',
					'https://ossweb-img.qq.com/images/lol/web201310/skin/big99008.jpg'
				],
				posi: '',
				scrollTop: 0,
				list: [],
				tabCur: 0,
				mainCur: 0,
				verticalNavTop: 0,
				load: true
			};
		},
		onLoad() {
			uni.showLoading({
				title: '加载中...',
				mask: true
			});
			let ins={
				url:'/categories',
				method:'get'
			}
			request.TokenRequest(ins).then(res=>{
				console.log(res.data)
				this.list = res.data
			}).catch(error=>{
				console.log(error)
			})
			// this.listCur = list[0];
		},
		onReady() {
			uni.hideLoading()
		},
		methods: {
			TabSelect(e) {
				this.tabCur = e.currentTarget.dataset.id;
				this.mainCur = e.currentTarget.dataset.id;
				this.verticalNavTop = (e.currentTarget.dataset.id - 1) * 50
				console.log("点击到我了" + this.verticalNavTop)
			},
			//希望有大佬给我讲解一下这个双向联动函数
			VerticalMain(e) {
				// #ifdef MP-ALIPAY
				return false //支付宝小程序暂时不支持双向联动 
				// #endif
				let that = this;
				let tabHeight = 0;
				if (this.load) {
					for (let i = 0; i < this.list.length; i++) {
						let view = uni.createSelectorQuery().select("#main-" + this.list[i].id);
						view.fields({
							size: true
						}, data => {
							this.list[i].top = tabHeight;
							tabHeight = tabHeight + data.height;
							this.list[i].bottom = tabHeight;
						}).exec();
					}
					this.load = false
				}
				let scrollTop = e.detail.scrollTop + 10;
				for (let i = 0; i < this.list.length; i++) {
					if (scrollTop > this.list[i].top && scrollTop < this.list[i].bottom) {
						this.verticalNavTop = (this.list[i].id - 1) * 50
						this.tabCur = this.list[i].id
						console.log(scrollTop)
						return false
					}
				}
			}
		},
	}
</script>

<style>
	.right {
		width: 75%;
		height: 1000rpx;
		margin: 0;
	}

	.VerticalNav.nav {
		width: 200upx;
		white-space: initial;
	}

	.VerticalNav.nav .cu-item {
		width: 100%;
		text-align: center;
		background-color: #fff;
		margin: 0;
		border: none;
		height: 50px;
		position: relative;
	}

	.VerticalNav.nav .cu-item.cur {
		background-color: #f1f1f1;
	}

	.VerticalNav.nav .cu-item.cur::after {
		content: "";
		width: 8upx;
		height: 30upx;
		border-radius: 10upx 0 0 10upx;
		position: absolute;
		background-color: currentColor;
		top: 0;
		right: 0upx;
		bottom: 0;
		margin: auto;
	}

	.VerticalBox {
		display: flex;
	}

	.VerticalMain {
		background-color: #f1f1f1;
		flex: 1;
	}
</style>

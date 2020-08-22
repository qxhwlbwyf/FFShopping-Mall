<template>
	<view>
		<view class="jc shoplistitem" @click="navigetor(shop.id)">
			<view class="proimage">
				<image :src="shop.image" :lazy-load="true"></image>
			</view>
			<view class="proinformation">
				<view class="proname">
					<text class="text-cut" style="width: 300rpx;">{{shop.name}}</text>
				</view>
				<view class="proprice">
					<text>{{shop.price/100|numFilter}}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {


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
		props: [
			"shop",
			"index"
		],
		// onReachBottom(){
		// 	console.log("到底了")
		// },		
		methods: {

			navigetor: function(id) {
				this.$emit("goitemmessage", id)
			}
		}
	}
</script>

<style>
	.jc {
		margin-top: 30rpx;
		display: flex;
		flex-direction: column;
		background-color: #eaeaea;
		border: 0.5px solid #eaeaea;
		border-radius: 10px;
	}

	.proimage {
		width: 300rpx;
		height: 250upx;
	}

	.proimage>image {
		width: 100%;
		height: 100%;
		border-top-left-radius: 20upx;
		border-top-right-radius: 20upx;
	}

	.proinformation {
		width: 100%;
		height: 90upx;
	}

	.proname {
		width: 100%;
		height: 40upx;
		overflow: hidden;
	}

	.proname>text {
		width: 100%;
		height: 100%;
		text-overflow: ellipsis;
		font-size: 22upx;
		display: inline-block;
	}

	.proprice {
		width: 100%;
		height: 50upx;
		overflow: hidden;
	}

	.proprice>text {
		width: 100%;
		height: 50upx;
		color: #ff0000;
		text-overflow: ellipsis;
		font-size: 30upx;
	}
</style>

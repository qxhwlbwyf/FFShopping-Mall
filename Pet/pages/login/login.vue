<template>
	<view>
		<view class="img flex  padding align-center justify-center ">
			<view class="bg-grey padding-sm margin-xs cu-avatar xl round margin-left" style="background-image:url(https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=2649574139,2472850608&fm=26&gp=0.jpg);">
			</view>
		</view>
		<view class="big">
			<form>
				<view class="cu-form-group margin-top">
					<view class="title">用户名</view>
					<input placeholder="账户用户名" name="input" v-model="user.username"></input>
				</view>
				<view class="cu-form-group">
					<view class="title">密码</view>
					<input placeholder="账户密码" name="password" type="password" v-model="user.password"></input>
				</view>
				<view class="grid col-2 padding-sm">
					<view class="margin-tb-sm text-center">
						<button class="cu-btn round line-red" @click="register">注册</button>
					</view>
					<view class="margin-tb-sm text-center">
						<button class="cu-btn round line-blue" @click="login">登录</button>
					</view>
				</view>
			</form>
		</view>
	</view>
</template>

<script>
	import request from '../../util/request.js'
	export default {
		data() {
			return {
				user: {
					username: "",
					password: ""
				}
			}
		},
		methods: {
			register: function() {
				uni.navigateTo({
					url: "../register/register"
				})
			},
			login() {
				var _this = this;
				let id;
				let opts = {
					url: '/login',
					//接口地址（写你自己的地址）
					method: 'post'
					//请求类型
				};
				let data = _this.user;
				request.baseRequest(opts, data).then(res => {
					// console.log(JSON.stringify(res));
					//打印请求返回的数据         
					uni.setStorage({
						key: 'hjr_token',
						data: "Bearer" + " " + res.data.token,
						success: function() {
							console.log("Bearer " + res.data.token);
						}
					});
					id = res.data.user.id;
					let ins = {
						url: '/customer/' + id,
						method: 'get'
					}
					request.TokenRequest(ins).then(res => {
						uni.setStorage({
							key: 'user',
							data: res.data
						});
					}).catch(error => {
						console.log(error)
					});
					let cartins = {
						url: '/cart/' + id,
						method: 'get'
					}
					request.TokenRequest(cartins).then(res => {
						console.log("/*-/*-/*-/*-/*-/*-/*-*/-*/-*/*-/-**/-*/-*/-*-")
						console.log(res.data)
						uni.setStorage({
							key: 'cart',
							data: res.data
						});
					}).catch(error => {
						console.log(error)
					});
					uni.showToast({
						title: res.message,
						position:'bottom',
						duration: 2000
					});
				}, error => {
					console.log("出错了");
					console.log(error);
				});



				uni.switchTab({
					url: "../index/index"
				})
			},
		}
	}
</script>

<style>
	.img {
		margin-top: 120rpx;
	}

	.cu-form-group .title {
		min-width: calc(4em + 15px);
	}

	.big {
		margin-top: 230rpx;
	}
</style>

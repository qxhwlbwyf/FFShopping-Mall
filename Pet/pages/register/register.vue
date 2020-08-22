<template>
	<view>
		<view class="img flex  padding align-center justify-center ">
			<view class="bg-grey padding-sm margin-xs cu-avatar xl round margin-left" style="background-image:url(https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=2649574139,2472850608&fm=26&gp=0.jpg);">
			</view>
		</view>
		<form>
			<view class="cu-form-group margin-top">
				<view class="title">生日</view>
				<view class="uni-list-cell-db">
					<picker mode="date" :value="date" :start="startDate" :end="endDate" @change="bindDateChange">
						<view class="uni-input">{{date}}</view>
					</picker>
				</view>
				<input  disabled="true"></input>
			</view>
			<view class="cu-form-group">
				<view class="title">邮箱</view>
				<input placeholder="电子邮箱" name="input" v-model="user.email"></input>
			</view>
			<view class="cu-form-group">
				<view class="title">性别</view>
				<radio-group @change="radioChange" style="width:700rpx;">
					<label class="uni-list-cell uni-list-cell-pd" v-for="(item, index) in items" :key="item.value" style="float: left;margin:0rpx 40rpx;">
						<view>
							<radio :value="item.value" :checked="index === current" />
						</view>
						<view>{{item.name}}</view>
					</label>
				</radio-group>
			</view>
			<view class="cu-form-group">
				<view class="title">用户名</view>
				<input placeholder="用户名" name="input" v-model="user.username"></input>
			</view>
			<view class="cu-form-group">
				<view class="title">真实姓名</view>
				<input placeholder="真实姓名" name="input" v-model="user.realname"></input>
			</view>
			<view class="cu-form-group">
				<view class="title">密码</view>
				<input placeholder="密码" name="input" v-model="user.password"></input>
			</view>
			<view class="cu-form-group">
				<view class="title">手机号码</view>
				<input placeholder="输入框带标签" name="input" v-model="user.mobilePhone"></input>
				<view class="cu-capsule radius">
					<view class='cu-tag bg-blue '>+86</view>
					<view class="cu-tag line-blue">中国大陆</view>
				</view>
			</view>
			<view class="cu-form-group">
				<view class="title">证件类型</view>
				<radio-group @change="cardchange" style="width:700rpx;">
					<label class="uni-list-cell uni-list-cell-pd" v-for="(item, index) in cards" :key="item.value" style="float: left;margin:0rpx 40rpx;">
						<view>
							<radio :value="item.value" :checked="index === no" />
						</view>
						<view>{{item.name}}</view>
					</label>
				</radio-group>
			</view>
			<view class="cu-form-group">
				<view class="title">证件号码</view>
				<input placeholder="编号" name="input" v-model="user.identityCardNo"></input>
			</view>
			<view>
				<view class="margin-tb-lg" style="margin-left: 550rpx;margin-top: 10rpx;">
					<button class="cu-btn round line-blue" @click="register">注册</button>
				</view>
			</view>
		</form>
	</view>
</template>

<script>
	import request from '../../util/request.js'
	export default {
		data() {
			const currentDate = this.getDate({
				format: true
			});
			return {
				date: currentDate,
				user: {
					birthDate: '',
					email: "",
					gender: 1,
					identityCardNo: "",
					identityCardType: 1,
					mobilePhone: "",
					password: "",
					realname: "",
					username: ""
				},
				items: [{
						value: 'girl',
						name: '女'
					},
					{
						value: 'boy',
						name: '男'
					}
				],
				current: 1,
				cards: [{
						value: 'zid',
						name: '暂住证'
					},
					{
						value: 'id',
						name: '身份证件'
					}
				],
				no: 1
			}
		},
		computed: {
			startDate() {
				return this.getDate('start');
			},
			endDate() {
				return this.getDate('end');
			}
		},
		methods: {
			bindDateChange: function(e) {
				this.date = e.target.value
			},
			getDate(type) {
				const date = new Date();
				let year = date.getFullYear();
				let month = date.getMonth() + 1;
				let day = date.getDate();
			
				if (type === 'start') {
					year = year - 60;
				} else if (type === 'end') {
					year = year + 2;
				}
				month = month > 9 ? month : '0' + month;;
				day = day > 9 ? day : '0' + day;
				return `${year}-${month}-${day}`;
			},
			radioChange: function(evt) {
				for (let i = 0; i < this.items.length; i++) {
					if (this.items[i].value === evt.target.value) {
						this.current = i;
						this.user.gender = this.current;
						console.log(this.user.gender);
						break;
					}
				}
			},
			cardchange: function(evt) {
				for (let i = 0; i < this.cards.length; i++) {
					if (this.cards[i].value === evt.target.value) {
						this.no = i;
						this.user.identityCardType = this.no;
						console.log(this.user.identityCardType);
						break;
					}
				}
			},
			register: function() {
				this.user.birthDate=this.date;
				console.log(this.user.birthDate);
				console.log(this.user.email);
				console.log(this.user.gender);
				console.log(this.user.username);
				console.log(this.user.password);
				console.log(this.user.mobilePhone);
				console.log(this.user.realname);
				console.log(this.user.identityCardType);
				console.log(this.user.identityCardNo);
				let ins={
					url:'/customer',
					method:'post'
				}
				let data=this.user;
				request.TokenRequest(ins,data).then(res=>{
					uni.showToast({
						title:res.message,
						duration: 2000
					})
				}).catch(error=>{
					console.log(error)
				})
				uni.redirectTo({
					url: "../login/login"
				})
			}
		}
	}
</script>
<style>
	.cu-form-group .title {
		min-width: calc(4em + 15px);
	}
</style>

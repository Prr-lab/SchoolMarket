<template>
	<view>
		<view class="user-section">
			<image class="bg" src="/static/user-bg.jpg"></image>
			<view class="topPart">
				<view class="portrait-box">
					<image class="portrait" :src="userInfo.portrait || '/static/missing-face.png'"></image>
					<text class="name">{{userInfo.username}}</text>
				</view>
			</view>
		</view>
		<uni-list class="user_detail">
			<uni-list-item title="学院" :rightText="userInfo.faculty"></uni-list-item>
			<uni-list-item title="地址" :rightText="userInfo.address"></uni-list-item>
			<uni-list-item title="QQ号" :rightText="userInfo.qqnumber"></uni-list-item>
			<uni-list-item title="微信号" :rightText="userInfo.vxnumber"></uni-list-item>
		</uni-list>

	</view>
</template>

<script>
	// import {  
	//     mapState,  
	//     mapMutations  
	// } from 'vuex';  
	export default {

		data() {
			return {
				userInfo: {
					username: '',
					faculty: '',
					address: '',
					qqnumber: '',
					vxnumber: ''
				}

			};
		},
		// computed:{
		// 	...mapState(['userInfo']),
		// },
		onLoad(options) {
			let id = options.id;
			this.getUserInfo(id)
		},
		methods: {
			//getuserbyownerid
			getUserInfo(id) {
				var that = this;
				uni.request({
					url: 'http://localhost:8080/user/getuserbyid?',
					method: 'GET',
					data: {
						id: id,
					},
					success: function(res) {
						console.log(res.data);
						that.userInfo.username = res.data.username;
						that.userInfo.faculty = res.data.faculty;
						that.userInfo.address = res.data.address;
						that.userInfo.qqnumber = res.data.qqnumber;
						that.userInfo.vxnumber = res.data.vxnumber;
					},
					fail: function(res) {
						console.log(res)
					},
				});

			}
		}
	}
</script>

<style lang="scss">
	page {
		background: $page-color-base;
	}

	.user-section {
		display: flex;
		align-items: center;
		justify-content: center;
		height: 460upx;
		padding: 40upx 30upx 0;
		position: relative;
		text-align: center;
		.bg {
			position: absolute;
			left: 0;
			top: 0;
			width: 100%;
			height: 100%;
			filter: blur(1px);
			opacity: .7;
		}

		.portrait-box {
			width: 200upx;
			height: 200upx;
			border: 6upx solid #fff;
			border-radius: 50%;
			position: relative;
			z-index: 2;
		}

		.portrait {
			position: relative;
			width: 100%;
			height: 100%;
			border-radius: 50%;
		}

		.yticon {
			position: absolute;
			line-height: 1;
			z-index: 5;
			font-size: 48upx;
			color: #fff;
			padding: 4upx 6upx;
			border-radius: 6upx;
			background: rgba(0, 0, 0, .4);
		}

		.pt-upload-btn {
			right: 0;
			bottom: 10upx;
		}

		.bg-upload-btn {
			right: 20upx;
			bottom: 16upx;
		}
		
		.name {
			width: 200upx;
			text-align: center;
		}
	}
</style>

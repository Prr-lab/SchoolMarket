<template>
	<view class="content">
		<view class="navbar">
			<view v-for="(item, index) in navList" :key="index" class="nav-item"
				:class="{current: tabCurrentIndex === index}" @click="tabClick(index)">
				{{item.text}}
			</view>
		</view>

		<swiper :current="tabCurrentIndex" class="swiper-box" duration="300" @change="changeTab">
			<swiper-item class="tab-content" v-for="(tabItem,tabIndex) in navList" :key="tabIndex">
				<scroll-view class="list-scroll-content" scroll-y>
					<!-- @scrolltolower="loadData" -->

					<!-- 空白页 -->
					<!-- <empty v-if="tabItem.loaded === true && tabItem.orderList.length === 0"></empty> -->

					<!-- 订单列表 " -->
					<view v-for="(orderItem, orderIndex) in tabItem.orderList" :key="orderIndex" class="order-item">
						<view class="i-top b-b">
							<text class="time">{{orderItem.datetime}}</text>
						</view>
						<view class="goods-box-single" @click="navToDetailPage(orderItem)">
							<image class="goods-img" :src="orderItem.img_url" mode="aspectFill"></image>
							<view class="right">
								<text class="title clamp">{{orderItem.productname}}</text>
								<!-- <text class="attr-box">{{goodsItem.attr}}  x {{goodsItem.number}}</text> -->
								<text class="price">{{orderItem.price}}</text>
							</view>
						</view>

						<!-- <view class="price-box">
							共
							<text class="num">7</text>
							件商品 实付款
							<text class="price">143.7</text>
						</view> -->
						<view class="action-box b-t">
							<button class="action-btn" @click="cancelOrder(orderItem)">取消订单</button>
							<button class="action-btn recom">立即支付</button>
						</view>
						<!-- <uni-load-more :status="tabItem.loadingType"></uni-load-more> -->
					</view>
				</scroll-view>
			</swiper-item>
		</swiper>
	</view>
</template>

<script>
	import uniLoadMore from '@/components/uni-load-more/uni-load-more.vue';
	import empty from "@/components/empty";
	import Json from '@/Json';
	import {
		mapState
	} from 'vuex';
	export default {
		components: {
			uniLoadMore,
			empty
		},
		computed: {
			...mapState(['hasLogin', 'userInfo'])
		},
		data() {
			return {
				tabCurrentIndex: 0,
				navList: [{
						state: 0,
						text: '我买的',
						loadingType: 'more',
						orderList: []
					},
					{
						state: 1,
						text: '我卖的',
						loadingType: 'more',
						orderList: []
					},
					{
						state: 2,
						text: '全部',
						loadingType: 'more',
						orderList: []
					}
				],


			};
		},

		onLoad(options) {
			/**
			 * 修复app端点击除全部订单外的按钮进入时不加载数据的问题
			 * 替换onLoad下代码即可
			 */
			this.tabCurrentIndex = +options.state;
			// // #ifndef MP
			// this.loadData()
			// // #endif
			// // #ifdef MP
			// if (options.state == 0) {
			// 	this.loadData()
			// }
			// // #endif
			this.loadData()

		},

		methods: {
			//获取订单列表
			loadData(source) {
				//这里是将订单挂载到tab列表下
				let index = this.tabCurrentIndex;
				let navItem = this.navList[index];
				let state = navItem.state;

				if (source === 'tabChange' && navItem.loaded === true) {
					//tab切换只有第一次需要加载数据
					return;
				}
				this.getAllOrder();
				this.getBuyOrder();
				this.getSellOrder();
				//this.getProductByUserId();
			},
			getAllOrder() {
				var that = this;
				uni.request({
					url: 'http://localhost:8080/order/getorderbyuserid/all?',
					method: 'GET',
					data: {
						id: that.userInfo.id,
					},
					success: function(res) {
						that.navList[2].orderList = res.data;
						console.log(res.data)
					},
					fail: function(res) {
						console.log(res)
					}

				});
			},
			getBuyOrder() {
				var that = this;
				uni.request({
					url: 'http://localhost:8080/order/getorderbyuserid/buy?',
					method: 'GET',
					data: {
						id: that.userInfo.id,
					},
					success: function(res) {
						that.navList[0].orderList = res.data;
						console.log(res.data)
					},
					fail: function(res) {
						console.log(res)
					}

				});
			},
			getSellOrder() {
				var that = this;
				uni.request({
					url: 'http://localhost:8080/order/getorderbyuserid/sell?',
					method: 'GET',
					data: {
						id: that.userInfo.id,
					},
					success: function(res) {
						that.navList[1].orderList = res.data;
						console.log(res.data)
					},
					fail: function(res) {
						console.log(res)
					}

				});
			},
			getProductByUserId() {
				var that = this;
				uni.request({
					url: 'http://localhost:8080/product/getproductbyuserid?',
					method: 'GET',
					data: {
						id: that.userInfo.id,
					},
					success: function(res) {
						that.navList[3].orderList = res.data;
						console.log(res.data)
					},
					fail: function(res) {
						console.log(res)
					}
				
				});
			},
			//swiper 切换
			changeTab(e) {
				this.tabCurrentIndex = e.target.current;
				this.loadData('tabChange');
			},
			//顶部tab点击
			tabClick(index) {
				this.tabCurrentIndex = index;
			},
			//删除订单
			deleteOrder(index) {
				uni.showLoading({
					title: '请稍后'
				})
				console.log(index)
				var that = this;
				uni.request({
					url: 'http://localhost:8080/order/deleteorderbyid?id='+index,
					method: 'DELETE',
					// data: {
					// 	id: index,
					// },
					success: function(res) {
						console.log(res.data)
						that.loadData()
					},
					fail: function(res) {
						console.log(res)
					}
				
				});
				// setTimeout(() => {
				// 	this.navList[this.tabCurrentIndex].orderList.splice(index, 1);
				// 	uni.hideLoading();
				// }, 600)
			},
			//取消订单
			cancelOrder(item) {
				uni.showLoading({
					title: '请稍后'
				})
				console.log(item)
				this.deleteOrder(item.orderid)
				uni.hideLoading();
				// setTimeout(() => {
				// 	let {
				// 		stateTip,
				// 		stateTipColor
				// 	} = this.orderStateExp(9);
				// 	item = Object.assign(item, {
				// 		state: 9,
				// 		stateTip,
				// 		stateTipColor
				// 	})

				// 	//取消订单后删除待付款中该项
				// 	let list = this.navList[1].orderList;
				// 	let index = list.findIndex(val => val.id === item.id);
				// 	index !== -1 && list.splice(index, 1);

				// 	uni.hideLoading();
				// }, 600)
			},

			//订单状态文字和颜色
			orderStateExp(state) {
				let stateTip = '',
					stateTipColor = '#fa436a';
				switch (+state) {
					case 1:
						stateTip = '待付款';
						break;
					case 2:
						stateTip = '待发货';
						break;
					case 9:
						stateTip = '订单已关闭';
						stateTipColor = '#909399';
						break;

						//更多自定义
				}
				return {
					stateTip,
					stateTipColor
				};
			},
			navToDetailPage(item) {
				let id = item.productid;
				uni.navigateTo({
					url: `/pages/product/product?id=${id}`
				})
			},
		},
	}
</script>

<style lang="scss">
	page,
	.content {
		background: $page-color-base;
		height: 100%;
	}

	.swiper-box {
		height: calc(100% - 40px);
	}

	.list-scroll-content {
		height: 100%;
	}

	.navbar {
		display: flex;
		height: 40px;
		padding: 0 5px;
		background: #fff;
		box-shadow: 0 1px 5px rgba(0, 0, 0, .06);
		position: relative;
		z-index: 10;

		.nav-item {
			flex: 1;
			display: flex;
			justify-content: center;
			align-items: center;
			height: 100%;
			font-size: 15px;
			color: $font-color-dark;
			position: relative;

			&.current {
				color: $base-color;

				&:after {
					content: '';
					position: absolute;
					left: 50%;
					bottom: 0;
					transform: translateX(-50%);
					width: 44px;
					height: 0;
					border-bottom: 2px solid $base-color;
				}
			}
		}
	}

	.uni-swiper-item {
		height: auto;
	}

	.order-item {
		display: flex;
		flex-direction: column;
		padding-left: 30upx;
		background: #fff;
		margin-top: 16upx;

		.i-top {
			display: flex;
			align-items: center;
			height: 80upx;
			padding-right: 30upx;
			font-size: $font-base;
			color: $font-color-dark;
			position: relative;

			.time {
				flex: 1;
			}

			.state {
				color: $base-color;
			}

			.del-btn {
				padding: 10upx 0 10upx 36upx;
				font-size: $font-lg;
				color: $font-color-light;
				position: relative;

				&:after {
					content: '';
					width: 0;
					height: 30upx;
					border-left: 1px solid $border-color-dark;
					position: absolute;
					left: 20upx;
					top: 50%;
					transform: translateY(-50%);
				}
			}
		}

		/* 多条商品 */
		.goods-box {
			height: 160upx;
			padding: 20upx 0;
			white-space: nowrap;

			.goods-item {
				width: 120upx;
				height: 120upx;
				display: inline-block;
				margin-right: 24upx;
			}

			.goods-img {
				display: block;
				width: 100%;
				height: 100%;
			}
		}

		/* 单条商品 */
		.goods-box-single {
			display: flex;
			padding: 20upx 0;

			.goods-img {
				display: block;
				width: 120upx;
				height: 120upx;
			}

			.right {
				flex: 1;
				display: flex;
				flex-direction: column;
				padding: 0 30upx 0 24upx;
				overflow: hidden;

				.title {
					font-size: $font-base + 2upx;
					color: $font-color-dark;
					line-height: 1;
				}

				.attr-box {
					font-size: $font-sm + 2upx;
					color: $font-color-light;
					padding: 10upx 12upx;
				}

				.price {
					font-size: $font-base + 2upx;
					color: $font-color-dark;

					&:before {
						content: '￥';
						font-size: $font-sm;
						margin: 0 2upx 0 8upx;
					}
				}
			}
		}

		.price-box {
			display: flex;
			justify-content: flex-end;
			align-items: baseline;
			padding: 20upx 30upx;
			font-size: $font-sm + 2upx;
			color: $font-color-light;

			.num {
				margin: 0 8upx;
				color: $font-color-dark;
			}

			.price {
				font-size: $font-lg;
				color: $font-color-dark;

				&:before {
					content: '￥';
					font-size: $font-sm;
					margin: 0 2upx 0 8upx;
				}
			}
		}

		.action-box {
			display: flex;
			justify-content: flex-end;
			align-items: center;
			height: 100upx;
			position: relative;
			padding-right: 30upx;
		}

		.action-btn {
			width: 160upx;
			height: 60upx;
			margin: 0;
			margin-left: 24upx;
			padding: 0;
			text-align: center;
			line-height: 60upx;
			font-size: $font-sm + 2upx;
			color: $font-color-dark;
			background: #fff;
			border-radius: 100px;

			&:after {
				border-radius: 100px;
			}

			&.recom {
				background: #fff9f9;
				color: $base-color;

				&:after {
					border-color: #f7bcc8;
				}
			}
		}
	}


	/* load-more */
	.uni-load-more {
		display: flex;
		flex-direction: row;
		height: 80upx;
		align-items: center;
		justify-content: center
	}

	.uni-load-more__text {
		font-size: 28upx;
		color: #999
	}

	.uni-load-more__img {
		height: 24px;
		width: 24px;
		margin-right: 10px
	}

	.uni-load-more__img>view {
		position: absolute
	}

	.uni-load-more__img>view view {
		width: 6px;
		height: 2px;
		border-top-left-radius: 1px;
		border-bottom-left-radius: 1px;
		background: #999;
		position: absolute;
		opacity: .2;
		transform-origin: 50%;
		animation: load 1.56s ease infinite
	}

	.uni-load-more__img>view view:nth-child(1) {
		transform: rotate(90deg);
		top: 2px;
		left: 9px
	}

	.uni-load-more__img>view view:nth-child(2) {
		transform: rotate(180deg);
		top: 11px;
		right: 0
	}

	.uni-load-more__img>view view:nth-child(3) {
		transform: rotate(270deg);
		bottom: 2px;
		left: 9px
	}

	.uni-load-more__img>view view:nth-child(4) {
		top: 11px;
		left: 0
	}

	.load1,
	.load2,
	.load3 {
		height: 24px;
		width: 24px
	}

	.load2 {
		transform: rotate(30deg)
	}

	.load3 {
		transform: rotate(60deg)
	}

	.load1 view:nth-child(1) {
		animation-delay: 0s
	}

	.load2 view:nth-child(1) {
		animation-delay: .13s
	}

	.load3 view:nth-child(1) {
		animation-delay: .26s
	}

	.load1 view:nth-child(2) {
		animation-delay: .39s
	}

	.load2 view:nth-child(2) {
		animation-delay: .52s
	}

	.load3 view:nth-child(2) {
		animation-delay: .65s
	}

	.load1 view:nth-child(3) {
		animation-delay: .78s
	}

	.load2 view:nth-child(3) {
		animation-delay: .91s
	}

	.load3 view:nth-child(3) {
		animation-delay: 1.04s
	}

	.load1 view:nth-child(4) {
		animation-delay: 1.17s
	}

	.load2 view:nth-child(4) {
		animation-delay: 1.3s
	}

	.load3 view:nth-child(4) {
		animation-delay: 1.43s
	}

	@-webkit-keyframes load {
		0% {
			opacity: 1
		}

		100% {
			opacity: .2
		}
	}
</style>

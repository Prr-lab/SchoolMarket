<template>
	<view class="container">
		<view class="head-nick" @click="ownerinfo">
			<image src="../../static/missing-face.png" />
			<text class="head-name"> {{goodinfo.owner_name}} </text>
			<text class="publish-time"> 发布于 {{goodinfo.datetime}} </text>
		</view>

		<view class="introduce-section">
			<text class="title">{{goodinfo.productname}}</text>
			<image src="../../static/offshelf.png" class="offshelf" mode="aspectFit" v-show="goodinfo.state"></image>
			<view class="price-box">
				<text class="price-tip">¥</text>
				<text class="price">{{goodinfo.price}}</text>
			</view>
			
			<!-- 			<view class="bot-row">
				<text>销量: 108</text>
				<text>库存: 4690</text>
				<text>浏览量: 768</text>
			</view> -->
		</view>

		<!--  分享 -->

		<view class="c-list">
			<view class="c-row b-b">
				<text class="tit">商品类型</text>
				<view class="bz-list con">
					<text> {{goodinfo.type}} </text>
				</view>
			</view>
			<view class="c-row b-b">
				<text class="tit">商品详情</text>
				<view class="con-list">
					<text> {{goodinfo.detail}} </text>
				</view>
			</view>
			<view class="c-row b-b">
				<text class="tit">备注</text>
				<view class="bz-list con">
					<text>可当场验货</text>
				</view>
			</view>
		</view>


		<view class="detail-desc">
			<view class="d-header">
				<text>图文详情</text>
			</view>
			<image :src="goodinfo.img_url" mode="widthFix"></image>
			<!-- 
			<rich-text :nodes="desc"></rich-text> -->
		</view>

		<!-- 留言 -->
		<view class="eva-section">
			<view class="e-header">
				<image class="e-icon" src="../../static/comment-icon.png" mode="aspectFill"></image>
				<text class="tit">留言</text>
			</view>
			<view class="e-add">
				<image class="head_img" src="../../static/missing-face.png" mode="aspectFill"></image>
				<input class="cmt-ipt" placeholder="问问更多细节吧~~~" v-model="newComment" id="newCmt"/>
				<button class="cmt-btn" hover-class="cmt-btn-prs" @click="addComment">留言</button>
			</view>
			<view class="eva-box" v-for="(item,index) in commentList" :key="index">
				<image class="portrait" src="../../static/missing-face.png" mode="aspectFill"></image>
				<view class="right">
					<text class="name">{{item.user_name}}</text>
					<text class="con">{{item.content}}</text>
					<view class="bot">
						<text class="time">{{item.datetime}}</text>
					</view>
				</view>

			</view>
		</view>

		<!-- 底部操作菜单 -->
		<view class="page-bottom">
			<!-- <view class="p-b-btn"> -->
				<navigator url="/pages/index/index" open-type="switchTab" class="p-b-btn">
					<text class="yticon icon-xiatubiao--copy"></text>
					<text>返回首页</text>
				</navigator>
				<view class="p-b-btn" :class="{active: favorite}" @click="toFavorite">
					<text class="yticon icon-shoucang"></text>
					<text>加入心愿单</text>
				</view>
			<!-- </view> -->
			<view class="action-btn-group">
				<!-- <button type="primary" class=" action-btn no-border buy-now-btn">聊一聊</button> -->
				<button type="primary" class=" action-btn no-border add-cart-btn" @click="buy">我想要</button>
			</view>
		</view>


		<!-- 分享 -->
		<share ref="share" :contentHeight="580" :shareList="shareList"></share>

	</view>

</template>

<script>
	import share from '@/components/share';
	import {
	    mapState 
	} from 'vuex'; 
	export default {
		components: {
			share
		},
		data() {
			return {
				goodid: -1,
				specClass: 'none',
				specSelected: [],
				goodinfo: {
					id: -1,
					productname: "",
					price: "",
					type: "",
					detail: "",
					owner_id: "",
					owner_name: "",
					img_url: "",
					state: ""
				},
				favorite: true,
				shareList: [],
				commentList: [],
				newComment: ""
			};
		},
		onLoad(options) {

			//接收传值,id里面放的是标题，因为测试数据并没写id 
			let id = options.id;
			if (id) {
				this.$api.msg(`点击了${id}`);
			}
			this.goodid=id;
			this.getProductInfo(id);
			this.getComment(id);
			//this.shareList = await this.$api.json('shareList');
			//var now=new Date();
			//console.log(now);
		},
		
		computed: {
			...mapState(['hasLogin','userInfo'])
		},
		methods: {
			//规格弹窗开关
			toggleSpec() {
				if (this.specClass === 'show') {
					this.specClass = 'hide';
					setTimeout(() => {
						this.specClass = 'none';
					}, 250);
				} else if (this.specClass === 'none') {
					this.specClass = 'show';
				}
			},
			//获取商品信息
			getProductInfo(id) {
				var that = this;
				uni.request({
					url: 'http://localhost:8080/product/getproductbyid?',
					method: 'GET',
					data: {
						id: id,
					},
					success: function(res) {
						that.goodinfo = res.data
						//console.log(res.data)
					},
					fail: function(res) {
						//console.log(res)
					}

				});
			},
			getComment(id) {
				var that = this;
				uni.request({
					url: 'http://localhost:8080/comment/getcommentbyproductid?',
					method: 'GET',
					data: {
						id: id,
					},
					success: function(res) {
						that.commentList = res.data
						//console.log(res.data)
					},
					fail: function(res) {
						//console.log(res)
					},
				});

			},
			addComment() {
				//未登录需先登录
				if(!this.hasLogin){
					uni.navigateTo({
						url: '../public/login'
					})
				}else if(this.newComment==""){
					//判断评论内容非空
					this.$api.msg("留言内容不得为空");
					
				}else{
					//var params = this.GetRequest();
				    //console.log(params['id']);
					var now = new Date();
					//var test=this.formateTime(now);
					//console.log(test);
					this.AddComment(now);
					this.newComment="";
				}
				//调用添加留言接口
				
				//
			},
			formateTime(now) {
		
				var str = "YYYY-MM-DD hh:mm:ss";
				var Week = ['日','一','二','三','四','五','六'];
				 
				str=str.replace(/yyyy|YYYY/,now.getFullYear());
				//str=str.replace(/yy|YY/,(this.getYear() % 100)>9?(this.getYear() % 100).toString():’0′ + (this.getYear() % 100));
				
				var month=now.getMonth()+1;
				str=str.replace(/MM/,month>9?month.toString():'0' + month);
				//str=str.replace(/M/g,this.getMonth());
				 
				str=str.replace(/w|W/g,Week[now.getDay()]);
				 
				str=str.replace(/dd|DD/,now.getDate()>9?now.getDate().toString():'0' + now.getDate());
				str=str.replace(/d|D/g,now.getDate());
				 
				str=str.replace(/hh|HH/,now.getHours()>9?now.getHours().toString():'0' + now.getHours());
				str=str.replace(/h|H/g,now.getHours());
				str=str.replace(/mm/,now.getMinutes()>9?now.getMinutes().toString():'0' + now.getMinutes());
				str=str.replace(/m/g,now.getMinutes());
				 
				str=str.replace(/ss|SS/,now.getSeconds()>9?now.getSeconds().toString():'0' + now.getSeconds());
				str=str.replace(/s|S/g,now.getSeconds());
				 
				return str;
				
			},
			AddComment(now) {
				var that = this;
				//console.log(that.userInfo);
				var nowdatetime=this.formateTime(now);
				uni.request({
					url: 'http://localhost:8080/comment/addcomment',
					method: 'POST',
					data: {
						id: -1,
						content: that.newComment,
						user_id: that.userInfo.id,
						user_name: that.userInfo.username,
						product_id: that.goodid,
						datetime: nowdatetime
					},
					header: {
						'content-type': 'application/x-www-form-urlencoded'
					},
					success: function(res) {
						//console.log(res);
						that.getComment(that.goodid);
					},
					fail: function(res) {
						//console.log(res)
					},
				});
			},
			//获取url参数
			GetRequest() {
				var url = window.location.hash; //获取url中"?"符后的字串
				var theRequest = new Object();
				
				if (url.indexOf("?") != -1) {
					var str = url.split('?')[1];
					//console.log(str);
					var strs = str.split("&");
					for (var i = 0; i < strs.length; i++) {
						theRequest[strs[i].split("=")[0]] = decodeURIComponent(strs[i].split("=")[1]);
					}
				}
				return theRequest;
			},
			
			//分享
			share() {
				this.$refs.share.toggleMask();
			},
			//收藏
			toFavorite() {
				this.favorite = !this.favorite;
			},
			buy() {
				var id=this.goodid;
				uni.navigateTo({
					url: `/pages/order/createOrder?id=${id}`,
					
				})
			},
			stopPrevent() {},
			ownerinfo() {
				let id = this.goodinfo.owner_id;
				uni.navigateTo({
					url: `/pages/userinfo/userinfo?id=${id}`
				})
				console.log(id);
				console.log(this.goodinfo);
			}
		},

	}
</script>

<style lang='scss'>
	page {
		background: $page-color-base;
		padding-bottom: 160upx;
	}

	.icon-you {
		font-size: $font-base + 2upx;
		color: #888;
	}

	.carousel {
		height: 722upx;
		position: relative;

		swiper {
			height: 100%;
		}

		.image-wrapper {
			width: 100%;
			height: 100%;
		}

		.swiper-item {
			display: flex;
			justify-content: center;
			align-content: center;
			height: 750upx;
			overflow: hidden;

			image {
				width: 100%;
				height: 100%;
			}
		}

	}

	/* 卖家信息 */
	.head-nick {
		background: #fff;
		display: flex;
		justify-content: flex-start;
		padding: 88upx 30upx 40upx;
		align-items: center;

		image {
			width: 72upx;
			height: 72upx;
			margin-right: 26upx;
			border-radius: 50%;
		}

		.head-name {
			font-size: 16upx;
			font-weight: bold;
			text-align: left;
			line-height: 72upx;
			color: #000000;
		}
		.publish-time {
			flex: 1;
			display: flex;
			flex-direction: column;
			font-size: 10px;
			color: $font-color-base;
			padding-left: 200upx;
			
		}
	}


	/* 标题简介 */
	.introduce-section {
		background: #fff;
		padding: 20upx 30upx;

		.title {
			font-size: 32upx;
			color: $font-color-dark;
			height: 50upx;
			line-height: 50upx;
		}

		.price-box {
			display: flex;
			align-items: baseline;
			height: 64upx;
			padding: 10upx 0;
			font-size: 26upx;
			color: $uni-color-primary;
		}

		.price {
			font-size: $font-lg + 2upx;
		}

		.m-price {
			margin: 0 12upx;
			color: $font-color-light;
			text-decoration: line-through;
		}

		.coupon-tip {
			align-items: center;
			padding: 4upx 10upx;
			background: $uni-color-primary;
			font-size: $font-sm;
			color: #fff;
			border-radius: 6upx;
			line-height: 1;
			transform: translateY(-4upx);
		}

		.bot-row {
			display: flex;
			align-items: center;
			height: 50upx;
			font-size: $font-sm;
			color: $font-color-light;

			text {
				flex: 1;
			}
		}
	}
	/* 下架 */
	.offshelf {
		position: absolute;
		height: 80px;
		width: 100px;
		right: 50px;
	}

	/* 分享 */
	.share-section {
		display: flex;
		align-items: center;
		color: $font-color-base;
		background: linear-gradient(left, #fdf5f6, #fbebf6);
		padding: 12upx 30upx;

		.share-icon {
			display: flex;
			align-items: center;
			width: 70upx;
			height: 30upx;
			line-height: 1;
			border: 1px solid $uni-color-primary;
			border-radius: 4upx;
			position: relative;
			overflow: hidden;
			font-size: 22upx;
			color: $uni-color-primary;

			&:after {
				content: '';
				width: 50upx;
				height: 50upx;
				border-radius: 50%;
				left: -20upx;
				top: -12upx;
				position: absolute;
				background: $uni-color-primary;
			}
		}

		.icon-xingxing {
			position: relative;
			z-index: 1;
			font-size: 24upx;
			margin-left: 2upx;
			margin-right: 10upx;
			color: #fff;
			line-height: 1;
		}

		.tit {
			font-size: $font-base;
			margin-left: 10upx;
		}

		.icon-bangzhu1 {
			padding: 10upx;
			font-size: 30upx;
			line-height: 1;
		}

		.share-btn {
			flex: 1;
			text-align: right;
			font-size: $font-sm;
			color: $uni-color-primary;
		}

		.icon-you {
			font-size: $font-sm;
			margin-left: 4upx;
			color: $uni-color-primary;
		}
	}

	.c-list {
		font-size: $font-sm + 2upx;
		color: $font-color-base;
		background: #fff;

		.c-row {
			display: flex;
			align-items: center;
			padding: 20upx 30upx;
			position: relative;
		}

		.tit {
			width: 140upx;
		}

		.con {
			flex: 1;
			color: $font-color-dark;

			.selected-text {
				margin-right: 10upx;
			}
		}

		.bz-list {
			height: 40upx;
			font-size: $font-sm+2upx;
			color: $font-color-dark;

			text {
				display: inline-block;
				margin-right: 30upx;
			}
		}

		.con-list {
			flex: 1;
			display: flex;
			flex-direction: column;
			color: $font-color-dark;
			line-height: 40upx;
		}

		.red {
			color: $uni-color-primary;
		}
	}

	/* 评价 */
	.eva-section {
		display: flex;
		flex-direction: column;
		padding: 20upx 30upx;
		background: #fff;
		margin-top: 16upx;

		.e-header {
			display: flex;
			align-items: center;
			height: 70upx;

			.tit {
				font-size: $font-base + 2upx;
				font-weight: bold;
				color: $font-color-dark;
				margin-right: 4upx;
				margin-left: 20upx;
			}

			.e-icon {
				flex-shrink: 0;
				padding: 8upx;
				width: 50upx;
				height: 50upx;
			}
		}

		.e-add {
			display: flex;
			align-items: center;
			height: 80upx;
			margin: 20upx 0upx;

			.head_img {
				flex-shrink: 0;
				width: 80upx;
				height: 80upx;
				border-radius: 100px;
				margin: 5upx;
			}

			.cmt-ipt {
				background-color: #eeeeee;
				margin-left: 45upx;
				padding-left: 30upx;
				height: 70upx;
				left: 30upx;
				border-radius: 50upx 0upx 0upx 50upx;
			}

			.cmt-btn {
				background: linear-gradient(to right, #ffac30, #fa436a, #F56C6C);
				border-radius: 0upx 50upx 50upx 0upx;
				height: 70upx;
				margin-left: 0;
				color: #ffffff;
				text-align: center;
				line-height: 70upx;
			}

			.cmt-btn-prs {
				color: #C0C4CC;
			}


		}
	}





	.eva-box {
		display: flex;
		padding: 20upx 0;

		.portrait {
			flex-shrink: 0;
			width: 80upx;
			height: 80upx;
			border-radius: 100px;
		}

		.right {
			flex: 1;
			display: flex;
			flex-direction: column;
			font-size: $font-base;
			color: $font-color-base;
			padding-left: 26upx;

			.con {
				font-size: $font-base;
				color: $font-color-dark;
				padding: 20upx 0;
			}

			.bot {
				display: flex;
				justify-content: space-between;
				font-size: $font-sm;
				color: $font-color-light;
			}
		}
	}

	/*  详情 */
	.detail-desc {
		background: #fff;
		margin-top: 16upx;

		image {

			width: 100%;
			/* height: 100%; */
			display: block;
		}

		.d-header {
			display: flex;
			justify-content: center;
			align-items: center;
			height: 80upx;
			font-size: $font-base + 2upx;
			color: $font-color-dark;
			position: relative;

			text {
				padding: 0 20upx;
				background: #fff;
				position: relative;
				z-index: 1;
			}

			&:after {
				position: absolute;
				left: 50%;
				top: 50%;
				transform: translateX(-50%);
				width: 300upx;
				height: 0;
				content: '';
				border-bottom: 1px solid #ccc;
			}
		}
	}

	/* 规格选择弹窗 */
	.attr-content {
		padding: 10upx 30upx;

		.a-t {
			display: flex;

			image {
				width: 170upx;
				height: 170upx;
				flex-shrink: 0;
				margin-top: -40upx;
				border-radius: 8upx;
				;
			}

			.right {
				display: flex;
				flex-direction: column;
				padding-left: 24upx;
				font-size: $font-sm + 2upx;
				color: $font-color-base;
				line-height: 42upx;

				.price {
					font-size: $font-lg;
					color: $uni-color-primary;
					margin-bottom: 10upx;
				}

				.selected-text {
					margin-right: 10upx;
				}
			}
		}

		.attr-list {
			display: flex;
			flex-direction: column;
			font-size: $font-base + 2upx;
			color: $font-color-base;
			padding-top: 30upx;
			padding-left: 10upx;
		}

		.item-list {
			padding: 20upx 0 0;
			display: flex;
			flex-wrap: wrap;

			text {
				display: flex;
				align-items: center;
				justify-content: center;
				background: #eee;
				margin-right: 20upx;
				margin-bottom: 20upx;
				border-radius: 100upx;
				min-width: 60upx;
				height: 60upx;
				padding: 0 20upx;
				font-size: $font-base;
				color: $font-color-dark;
			}

			.selected {
				background: #fbebee;
				color: $uni-color-primary;
			}
		}
	}

	/*  弹出层 */
	.popup {
		position: fixed;
		left: 0;
		top: 0;
		right: 0;
		bottom: 0;
		z-index: 99;

		&.show {
			display: block;

			.mask {
				animation: showPopup 0.2s linear both;
			}

			.layer {
				animation: showLayer 0.2s linear both;
			}
		}

		&.hide {
			.mask {
				animation: hidePopup 0.2s linear both;
			}

			.layer {
				animation: hideLayer 0.2s linear both;
			}
		}

		&.none {
			display: none;
		}

		.mask {
			position: fixed;
			top: 0;
			width: 100%;
			height: 100%;
			z-index: 1;
			background-color: rgba(0, 0, 0, 0.4);
		}

		.layer {
			position: fixed;
			z-index: 99;
			bottom: 0;
			width: 100%;
			min-height: 40vh;
			border-radius: 10upx 10upx 0 0;
			background-color: #fff;

			.btn {
				height: 66upx;
				line-height: 66upx;
				border-radius: 100upx;
				background: $uni-color-primary;
				font-size: $font-base + 2upx;
				color: #fff;
				margin: 30upx auto 20upx;
			}
		}

		@keyframes showPopup {
			0% {
				opacity: 0;
			}

			100% {
				opacity: 1;
			}
		}

		@keyframes hidePopup {
			0% {
				opacity: 1;
			}

			100% {
				opacity: 0;
			}
		}

		@keyframes showLayer {
			0% {
				transform: translateY(120%);
			}

			100% {
				transform: translateY(0%);
			}
		}

		@keyframes hideLayer {
			0% {
				transform: translateY(0);
			}

			100% {
				transform: translateY(120%);
			}
		}
	}

	/* 底部操作菜单 */
	.page-bottom {
		position: fixed;
		left: 30upx;
		bottom: 30upx;
		z-index: 95;
		display: flex;
		justify-content: space-between;
		align-items: center;
		width: 690upx;
		height: 100upx;
		background: rgba(255, 255, 255, .9);
		box-shadow: 0 0 20upx 0 rgba(0, 0, 0, .5);
		border-radius: 16upx;

		.p-b-btn {
			display: flex;
			flex-direction: column;
			align-items: center;
			justify-content: center;
			font-size: $font-sm;
			color: $font-color-base;
			width: 140upx;
			height: 80upx;

			.yticon {
				font-size: 40upx;
				line-height: 48upx;
				color: $font-color-light;
			}

			&.active,
			&.active .yticon {
				color: $uni-color-primary;
			}

			.icon-fenxiang2 {
				font-size: 42upx;
				transform: translateY(-2upx);
			}

			.icon-shoucang {
				font-size: 46upx;
			}
		}

		.action-btn-group {
			display: flex;
			height: 76upx;
			border-radius: 100px;
			overflow: hidden;
			box-shadow: 0 20upx 40upx -16upx #fa436a;
			box-shadow: 1px 2px 5px rgba(219, 63, 96, 0.4);
			background: linear-gradient(to right, #ffac30, #fa436a, #F56C6C);
			margin-right: 20upx;
			position: relative;

			&:after {
				content: '';
				position: absolute;
				top: 50%;
				right: 50%;
				transform: translateY(-50%);
				height: 28upx;
				width: 0;
				border-right: 1px solid rgba(255, 255, 255, .5);
			}

			.action-btn {
				display: flex;
				align-items: center;
				justify-content: center;
				width: 180upx;
				height: 100%;
				font-size: $font-base;
				
				padding: 0;
				border-radius: 0;
				background: transparent;
			}
		}
	}
</style>

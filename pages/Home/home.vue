<template>
	 <view>
	<view id="home">
		<view class="flex align-end home-title-search bg-white">
			<view class="flex align-center justify-between home-search-all-one">
				<view class="home-search-all" @click="search">
					<view class="flex align-center  share-all-bg" >
						<view style="font-size: 40upx;" class="lg text-gray cuIcon-search margin-left margin-right-xs"></view>
						<view class="text-df text-jiujiujiu">搜索商品</view>
					</view>
				</view>
				<view class="redRodbox">
					<image @tap="goMessage" class="home-title-message-img" src="../../static/homeMeesage.png" ></image>
					<view v-if="showRed" class="redRod"></view>
				</view>
			</view>
		</view>
		
		<view style="height: 126upx;"></view>
		
		<view class="flex align-center solid-top bg-white">
			<view class="title-select-left">
				<QSTabs 
					:current="current_3" 
					animationMode="line2" 
					activeColor="#CD3233" 
					autoCenterMode="window" 
					defaultStyle="#525253"	
					:tabs="tabs_3" 
					width="150" 
					@change="change"
				/>
			</view>
			<view class="title-select-right" @click="moreDetail">
				<view style="font-size: 44upx;" class="lg text-black cuIcon-list padding-left"></view>
			</view>
		</view>
		<mescroll-uni ref='mescroll' @down="downCallback" @up="upCallback"  :up="upOption" :down="downOption" :top="204">
		<swiper 
			style="min-height: 255upx;" 
			class="screen-swiper square-dot" 
			:indicator-dots="true" 
			:circular="true"
			:autoplay="true" 
			interval="5000" 
			v-if="isShowIndex"
			duration="500">
				<swiper-item @click="swiperShow(item)" v-for="(item,index) in swiperList" :key="index">
					<image :src="item.pic" mode="aspectFill"></image>
				</swiper-item>
		</swiper>
		
		<!-- 八宫格 -->
		<view v-if="current_3 == 0" class="grid padding-bottom text-center col-4 bg-white">
			<view @tap="homeListClick(vo.id)" v-for="(vo,key) in homeTitle" :key="key" class="home-title-image">
				<image :src="vo.image"></image>
				<view>{{vo.name}}</view>
			</view>
		</view> 
		
		
		<!-- 其他八宫格 -->
			<view v-if="current_3 != 0" class="grid padding-bottom text-center col-4 bg-white bagong">
				<view @tap="classifyClick(item.id,item.cate_name)" v-for="(item,key) in homeTabs.child" :key="key" class="home-title-image">
					<image :src="item.pic"></image>
					<view>{{item.cate_name}}</view>
				</view>
			</view> 
		
		<!-- 新闻 -->
			
		<view   
			v-if="isShowIndex"
			class="bg-white margin-top-xs flex align-center home-new-all margin-bottom">
			
			<image class="home-new-image" src="../../static/newimg.png"></image>
			<swiper vertical autoplay circular interval="3000" class="tui-swiper margin-right">
				<swiper-item @click="goNews(item)" v-for="(item,index) in newsList" :key="item.id" class="tui-swiper-item">
					<view class="tui-news-item">{{item.info}}</view>
				</swiper-item>
			</swiper>
		</view>
		<!-- 新闻new -->
		
		<!-- 优惠券 -->
		<!-- <view
			v-if="isShowIndex"
		     class="flex align-center justify-between youhui-all-margin youhuiquan-color">
			<view class="home-youhui-all">
				<view style="font-size: 80upx;" class="text-price">10</view>
				<view class="flex flex-direction align-center margin-left-xs">
					<view class="text-xl text-bold">优惠券</view>
					<view class="text-xs">店铺红包</view>
				</view>
			</view>
			<view class="home-youhui-all">
				<view style="font-size: 80upx;" class="text-price">20</view>
				<view class="flex flex-direction align-center margin-left-xs">
					<view class="text-xl text-bold">优惠券</view>
					<view class="text-xs">新人专属</view>
				</view>
			</view>
		</view> -->
		
		<view class="tui-product-list margin-top" >
			<view class="tui-product-container">
				<block v-for="(item,index) in productList" :key="index" v-if="(index+1)%2!=0">
					<!-- <template is="productItem" data="{{item,index:index,isList:isList}}" /> -->
					<!--商品列表-->
					<view class="tui-pro-item" hover-class="hover" :hover-start-time="150" @tap="detailsClck(item.id)">
						<image :src="item.image" class="tui-pro-img" mode="widthFix" />
						<view class="tui-pro-content">
							<view class="tui-pro-tit"><text v-if="item.is_hot" class="text-xs padding-lr-xs margin-right-sm native-txt-red">{{dealTechan(item)}}特产</text>{{item.store_name}}</view>
							<view class="flex align-center justify-between">
								<view class="tui-sale-price">￥{{item.price}}</view>
								<view class="tui-pro-pay">销量{{item.sales}}</view>
							</view>	
						</view>
					</view>
					<!--商品列表-->
				</block>
			</view>
			<view class="tui-product-container" >
				<block v-for="(item,index) in productList" :key="index" v-if="(index+1)%2==0">
					<!-- <template is="productItem" data="{{item,index:index}}" /> -->
					<!--商品列表-->
					<view class="tui-pro-item" hover-class="hover" :hover-start-time="150" @tap="detailsClck(item.id)">
						<image :src="item.image" class="tui-pro-img" mode="widthFix" />
						<view class="tui-pro-content">
							<view class="tui-pro-tit"><text v-if="item.is_hot" class="text-xs padding-lr-xs margin-right-sm native-txt-red">{{dealTechan(item)}}特产</text>{{item.store_name}}</view>
							<view class="flex align-center justify-between">
								<view class="tui-sale-price">￥{{item.price}}</view>
								<view class="tui-pro-pay">销量{{item.sales}}</view>
							</view>
						</view>
					</view>
					<!--商品列表-->
				</block>
			</view>
		</view>
		</mescroll-uni>
		<view class="red-pack" @tap="qian" v-if="!isRemoveQian">
			<view class="r-box">
				<image src="../../static/redpack.png"></image>
				<text @tap.stop="removeQian" class="sm text-white cuIcon-roundclose"></text>
			</view>
		</view>
		<!-- <uni-load-more v-if="loadingimg" :loadingType="loadingType" ></uni-load-more> -->
		
		<!-- 顶部弹出 -->
		<uni-popup ref="popup" type="top" class="popModel">
			<view class="padding">
				<view class="grid text-center col-4 native-address-all">
					<view class="margin-top-sm">
						<view class="native-address flex align-center justify-center">123321</view>
					</view>
				</view>
			</view>
		</uni-popup>
	</view>
	 <x-loading text="加载中.." mask="true" click="true" ref="loading"></x-loading>
	</view>
</template>

<script>
	import uniLoadMore from "@/components/uni-load-more/uni-load-more.vue"
	import QSTabs from '@/components/QS-tabs/QS-tabs.vue';
	import {userMessages} from '@/network/getProfileData'
	
	// 网络处理
	import { getHomeData,getDetailData } from '@/network/Home'
	
	// 获取分类
	import { getCategory } from '@/network/category'
	// 变量
	import { HOST } from '@/common/const'
	// 工具类
	import { replaceImage } from '@/utils/dealUrl'
	
	// 下拉刷新
	import MescrollUni from "@/components/mescroll-uni/mescroll-uni.vue";
	
	// 弹出层
	import uniPopup  from "@/components/uni-popup/uni-popup"
	
	import {mapGetters} from 'vuex'
	export default{
		components: {
			QSTabs,
			uniLoadMore,
			uniPopup,
			MescrollUni
		},
		data(){
			return{
				loadingimg:false,//login加载
				loadingType:1,//login状态
				newsList: [], // 新闻
				tabs_3: [], //tabBar
				current_3: 0,
				swiperList: [], // 头部轮播
				homeTabs:[],//其他的八宫格
				homeTitle:[{//第一个tab的八宫格
					id:0,
					name:'老家特产',
					image:'../../static/homea.png'
				},{
					id:1,
					name:'秒杀',
					image:'../../static/homeb.png',
				},{
					id:2,
					name:'拼团',
					image:'../../static/homec.png'
				},{
					id:3,
					name:'积分商城',
					image:'../../static/homed.png'
				},{
					id:4,
					name:'政府新闻',
					image:'../../static/homed.png'
				},{
					id:5,
					name:'公益信息',
					image:'../../static/homee.png'
				},{
					id:6,
					name:'易物平台',
					image:'../../static/homef.png'
				},{
					id:7,
					name:'更多',
					image:'../../static/homeg.png'
				}],
				productList: [], // 商品列表
				page:1,
				isShowIndex:true,
				isLoading:true,
				// 下拉刷新的常用配置
				downOption: { 
					use: true, // 是否启用下拉刷新; 默认true
					auto: true, // 是否在初始化完毕之后自动执行下拉刷新的回调; 默认true
				},
				// 上拉加载的常用配置
				upOption: {
					use: true, // 是否启用上拉加载; 默认true
					auto: true, // 是否在初始化完毕之后自动执行上拉加载的回调; 默认true
					page: {
						num: 0, // 当前页码,默认0,回调之前会加1,即callback(page)会从1开始
						size: 10 // 每页数据的数量,默认10
					},
					noMoreSize: 5, // 配置列表的总数量要大于等于5条才显示'-- END --'的提示
					textNoMore:'-- 没有更多了 --',
					empty: {
						tip: '暂无相关数据'
					}
				},
				isLoading2:false,
				isRemoveQian:false ,//是否删除红包
				loadingOnce:false,
				showRed:false
			}
		},
		onLoad(){
			// 获取主页上面数据
			this._getHomeData()
		},
		onReady() {
			if(!this.loadingOnce){
				this.$refs.loading.open()
			}
		},
		onShow() {
			// this.userMessages()
			if(this.isToken){
				this.userMessages(this.isToken)
			}
			console.log(this.showRed)
		},
		computed:{
			...mapGetters(['isToken','isLookSettled','allUnabsorbed'])
		},
		methods:{			
			// 搜索事件
			search(){
				uni.navigateTo({
					url:'../HM-search/HM-search'
				})
			},
			// 切換tab事件
			change(index) {
				if(this.current_3 != index){
					const mes = this.$refs.mescroll.mescroll
					const obj = this.tabs_3[index]
					this.current_3 = index
					this.productList = []
					if(this.current_3 != 0){
						this.isShowIndex = false
						this.homeTabs = obj
					}else{
						this.isShowIndex = true
					}
					mes.resetUpScroll()
				}
			},
			//八宫格
			homeListClick(num){
				if(num == 0){
					//老家特产
					uni.navigateTo({
						url:'Native/native'
					})
				}else if(num == 1){
					uni.navigateTo({
						url:'Seckilltime/seckilltime'
					})
				}else if(num == 2){
					//拼团
					uni.navigateTo({
						url:'BookingList/bookingList'
					})
				}else if(num == 3){
					//积分商城
					uni.navigateTo({
						url:'Integral/integralShop'
					})
				}else if(num == 4){
					//政府新闻
					uni.navigateTo({
						url:'Government/government'
					})
				}else if(num == 5){
					//公益信息
					uni.navigateTo({
						url:'PublicMessage/publicmessage'
					})
				}else if(num == 6){
					//易物平台
					uni.navigateTo({
						url:'Barter/BaterClsaaify'
					})
				}else if(num == 7){
					//更多
				}
			},
			// 跳转到签到
			qian(){
				uni.navigateTo({
					url:'qiandao'
				})
			},
			userMessages(token){
				userMessages(token).then(res => {
					if(res.data.code == 200){
						const data = res.data.data
						console.log(data)
						if(data.orderNotice == 0 && data.payRefunNotice == 0 && data.shopNotice == false && data.systemNotice == 0 && this.allUnabsorbed == 0){
							this.showRed = false
						}else{
							this.showRed = true
						}
					}
				})
			},
			// 其余tab八宫格监听点击
			classifyClick(id,name){
				uni.navigateTo({
					url:`../HM-search/HM-searchList?cid=${id}&name=${name}`
				})
			},
			//商品详情页
			detailsClck(id){
				uni.navigateTo({
					url:'../ShopDetails/shopDetails?id=' + id
				})
			},
			// 获取上面数据
			_getHomeData(obj){
				uni.hideTabBar()
				getHomeData().then(res => {
					uni.showTabBar()
					this.$refs.loading.close()
					this.loadingOnce = true
					if(res.statusCode == 200){
						this.swiperList = res.data.data.banner
						this.newsList = res.data.data.roll
						
						const list  = res.data.data.cate
						list.unshift({
							cate_name:'推荐',
							child:[],
							id:0
						})
						list.forEach(x => {
							x.page = 1
						})
						this.tabs_3 = list
					}
				}).catch(err => {
					uni.showTabBar()
				})
			},
			// 获取下面商品数据.
			_getDetailData(pageNum,pageSize,mescroll,cid){
				getDetailData({
					page:pageNum,
					limit:pageSize,
					hideModel:true,
					cid:cid || ''
				}).then(res => {
					this.$refs.loading.close()
					this.loadingOnce = true
					if(res.statusCode == 200){
						
						const obj = res.data.data
						const arr = res.data.data.map(item => {
							return replaceImage(item.image)
						})
						if(obj.length){
							obj.forEach((item,i) => {
								item.image = arr[i]
								
							})
						}
						if(obj.length < pageSize){
							this.isLoading = false
						}else{
							this.isLoading = true
						}
						if(pageNum == 1) this.productList = [];
						this.productList = this.productList.concat(obj)
						mescroll.endSuccess(obj.length, this.isLoading);
					}
				}).catch(err => {
					mescroll.endErr()
				})
			},
			// 加载更多分类
			moreDetail(){
				// this.$refs.popup.open()
				uni.switchTab({

					url:'../Classify/classify'
				})
			},
			
			// 下拉刷新方法
			downCallback(mescroll) { 
				mescroll.resetUpScroll()
			},
			/*上拉加载的回调: mescroll携带page的参数, 其中num:当前页 从1开始, size:每页数据条数,默认10 */
			upCallback(mescroll) {
				const cid = this.current_3 != 0 ? this.tabs_3[this.current_3].id : ''
				// 此时mescroll会携带page的参数:
				let pageNum = mescroll.num; // 页码, 默认从1开始
				let pageSize = mescroll.size; // 页长, 默认每页10条
				this._getDetailData(pageNum,pageSize,mescroll,cid)
			},
			// 处理特产
			dealTechan(item){
				return item.province.replace(/(['省''市'('自治区')])/g,'')
			},
			// 查看新闻
			goNews(item){
				const id = item.url
				uni.navigateTo({
					url:`Government/articless?id=${id}`
				})
			},
			// 轮播图展示
			swiperShow(item){
				// let arr = this.swiperList
				// arr = arr.map(x => x.pic)
				// // #ifdef APP-PLUS
				// plus.nativeUI.previewImage(arr,index)
				// // #endif
				uni.navigateTo({
					url:`../ShopDetails/shopDetails?id=${item.url}`
				})
			},
			goMessage(){
				uni.navigateTo({
					url:'/pages/My/Inform/inform'
				})
			},
			removeQian(){
				this.isRemoveQian = true
			}
		}
	}
</script>

<style>
	.screen-my-swiper{
		height: 256upx;
	}
	.title-select-left{
		width: 85%;
	}
	.title-select-right{
		height: 15%;
	}
	.home-title-image{
		
	}
	.home-title-image image{
		height: 90upx;
		width: 90upx;
		margin-top: 36upx;
	}
	
	/**
	 * 滚动
	 */
	.tui-swiper {
		font-size: 28upx;
		height: 50upx;
		flex: 1;
	}
	
	.tui-swiper-item {
		display: flex;
		align-items: center
	}
	.tui-news-item {
		line-height: 28upx;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
	}
	/**
	 * 滚动end
	 */
	.home-new-image{
		width:128upx;
		height:30upx;
		margin-left: 30upx;
		margin-right: 20upx;
	}
	.home-new-all{
		height: 82upx;
	}
	.home-youhui-all{
		display: flex;
		align-items: center;
		justify-content: center;
		width:340upx;
		height:160upx;
		background: url(../../static/homej.png);
		background-size: 100% 100%;
	}
	.youhui-all-margin{
		margin: 14upx 24upx;
	}
	.text-price::before{
		font-size: 50%;
	}
	.youhuiquan-color{
		color: #E5150B;
	}
	.bagong{
		margin: 20upx 0;
	}
	/* 商品列表*/
	
	.tui-product-list {
		margin: 0 24upx;
		display: flex;
		justify-content: space-between;
		flex-direction: row;
		flex-wrap: wrap;
		box-sizing: border-box;
	}
	
	.tui-product-container {
		flex: 1;
		margin-right: 16rpx;
	}
	
	.tui-product-container:last-child {
		margin-right: 0;
	}
	
	.tui-pro-item {
		width: 100%;
		margin-bottom: 10rpx;
		background: #fff;
		box-sizing: border-box;
		border-radius: 12rpx;
		overflow: hidden;
		transition: all 0.15s ease-in-out;
	}
	
	.tui-flex-list {
		display: flex;
		margin-bottom: 1rpx !important;
	}
	
	.tui-pro-img {
		width: 100%;
		display: block;
	}
	.native-txt-red{
		height:40upx;
		background:rgba(205,50,51,1);
		border-radius:16upx;
		font-size: 20upx;
		display:inline-flex;
		align-items: center;
		color: #fff;
	}
	.tui-proimg-list {
		width: 260rpx;
		height: 260rpx !important;
		flex-shrink: 0;
		border-radius: 12rpx;
	}
	
	.tui-pro-content {
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		box-sizing: border-box;
		padding: 20rpx;
	}
	
	.tui-pro-tit {
		color: #2e2e2e;
		font-size: 26rpx;
		word-break: break-all;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-box-orient: vertical;
		-webkit-line-clamp: 2;
	}
	
	.tui-pro-price {
		padding-top: 18rpx;
	}
	
	.tui-sale-price {
		font-size: 34rpx;
		font-weight: 500;
		color: #e41f19;
	}
	
	.tui-factory-price {
		font-size: 24rpx;
		color: #a0a0a0;
		text-decoration: line-through;
		padding-left: 12rpx;
	}
	
	.tui-pro-pay {
		padding-top: 10rpx;
		font-size: 24rpx;
		color: #656565;
	}
	
	/* 商品列表*/
	
	.red-pack{
		position: fixed;
		z-index: 999;
		right: 30upx;
		bottom: 70upx;
	}
	.red-pack image{
		width:98upx;
		height:102upx;
	}
	.home-title-message-img{
		width: 40upx;
		height: 40upx;
		margin-right: 24upx;
	}
	.home-title-search{
		position: fixed;
		z-index: 999;
		width: 100%;
		height: 126upx;
		padding-bottom: 14upx;
		background: #FFFFFF;
	}
	.share-all-bg{
		width: 100%;	
		height: 62upx;
		background: #F2F2F2;
		border-radius: 10upx;
	}
	.home-search-all{
		width: 80%;
		margin-left: 30upx;
	}
	.home-search-all-one{
		width: 100%;
		box-sizing: border-box;
		padding: 0 20upx;
	}
	.popModel{
		position: fixed;
		top: 42px;
	}
	.r-box{
		position: relative;
	}
	.r-box text{
		position: absolute;
		right: 0;
		top: 0;
		font-size: 20upx;
	}
	.redRodbox{
		position: relative;
	}
	.redRod{
		position: absolute;
		width: 20upx;
		height: 20upx;
		border-radius: 50%;
		right: 0;
		top: 0;
		background-color: red;
	}
</style>

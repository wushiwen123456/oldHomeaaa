<template>
	<view>
		<view v-if="mytrue" class="my-title-background">
			
			<view  class="bg-white  margin-left margin-right my-one-all">
				<view class="flex align-end justify-between margin-left-lg">
					<view class="flex align-center" @click="mySet">
						<image class="my-head-image" :src="userData.avatar"></image>
						<view class="text-xl text-bold margin-left-sm nicket text-cut">{{userData.nickname}}</view>
					</view>
					<view @click="goQiandao" class="my-one-jifen flex align-center justify-center">
						<image src="../../static/jifenw.png"></image>
						<view class="margin-left-xs">红包{{userData.red_packet}}</view>
					</view>
				</view>
				<view class="flex align-center justify-around margin-top">
					<view @tap="collectClick" class="flex flex-direction align-center justify-center">
						<view class="margin-bottom-xs text-lg">{{userData.product_collect}}</view>
						<view class="text-sm">商品收藏</view>
					</view>
					<view @tap="attrntionClick" class="flex flex-direction align-center justify-center">
						<view class="margin-bottom-xs text-lg">{{userData.shop_collect}}</view>
						<view class="text-sm">店铺收藏</view>
					</view>
					<view @tap="integralShopCick" class="flex flex-direction align-center justify-center">
						<view class="margin-bottom-xs text-lg">{{userData.integral}}</view>
						<view class="text-sm">积分</view>
					</view>
					<view @tap="footprintClick" class="flex flex-direction align-center justify-center" @click="my_zuji">
						<view class="margin-bottom-xs text-lg">{{userData.visit}}</view>
						<view class="text-sm">我的足迹</view>
					</view>
				</view>
			</view>
		</view>
		
		<view v-else class="Nologin" >
			<view @tap="loginClick" class="Nologin-button">登录/注册</view>
		</view>
		
		
		<view class="my-two-three-all">
			<view class="bg-white border-all ">
				<view @tap="myOrderClick(0)" class="flex align-center justify-between solid-bottom height-all">
					<view class="text-bold text-lg">我的订单</view>
					<view class="flex align-center">
						<view class="text-jiujiujiu text-sm">查看全部</view>
						<view class="lg text-jiujiujiu cuIcon-right margin-left-xs"></view>
					</view>
				</view>
				<view class="flex align-center justify-around padding-top-lg my-two-bootom">
					<view @tap="myOrderClick(1)" class="flex align-center justify-center flex-direction">
						<image class="my-image-two-all" src="../../static/mytwoa.png"></image>
						<view class="text-sm bg-white">待付款</view>
					</view>
					<view @tap="myOrderClick(2)" class="flex align-center justify-center flex-direction">
						<image class="my-image-two-all" src="../../static/mytwob.png"></image>
						<view class="text-sm bg-white">待发货</view>
					</view>
					<view @tap="myOrderClick(3)" class="flex align-center justify-center flex-direction">
						<image class="my-image-two-all" src="../../static/mytwoc.png"></image>
						<view class="text-sm bg-white">待收货</view>
					</view>
					<view @tap="myOrderClick(4)" class="flex align-center justify-center flex-direction">
						<image class="my-image-two-all" src="../../static/mytwod.png"></image>
						<view class="text-sm bg-white">评价</view>
					</view>
					<view @tap="refundClick" class="flex align-center justify-center flex-direction">
						<image class="my-image-two-all" src="../../static/mytwoe.png"></image>
						<view class="text-sm bg-white">退款/售后</view>
					</view>
				</view>
			</view>
			<view class="bg-white border-all margin-top-xs">
				<view>
					<view class="flex align-center justify-between solid-bottom height-all">
						<view class="text-bold text-lg">我的工具</view>
						<view class="flex align-center">
							<!-- <view class="text-jiujiujiu">查看全部</view>
							<view class="lg text-jiujiujiu cuIcon-right margin-left-xs"></view> -->
						</view>
					</view>
				</view>
				<view class="grid margin-bottom text-center col-4 padding-top-lg">
					<view @tap="discountsClick" class="my-three-all">
						<image src="../../static/mythreea.png"></image>
						<view>优惠券</view>
					</view>
					<view @tap="enterClick" class="my-three-all">
						<image src="../../static/mythreeb.png"></image>
						<view>商家入驻</view>
					</view>
					<view @tap="MybookingClick" class="my-three-all">
						<image src="../../static/mythreec.png"></image>
						<view>我的拼团</view>
					</view>
					<view @tap="addressClick" class="my-three-all">
						<image src="../../static/mythreed.png"></image>
						<view>我的地址</view>
					</view>
					<view @tap="intrgralDetailClick" class="my-three-all">
						<image src="../../static/mythreee.png"></image>
						<view>积分明细</view>
					</view>
					<view @tap="discountscententClick()" class="my-three-all">
						<image src="../../static/mythreef.png"></image>
						<view>领券中心</view>
					</view>
					<view @tap="goQianbao()" class="my-three-all my-three-all2">
						<image src="/static/tixian/02.png"></image>
						<view>我的钱包</view>
					</view>
					<view @tap="erWeiShare()" class="my-three-all my-three-all2">
						<image src="../../static/fenxiang.png"></image>
						<view>推广好友</view>
					</view>
				</view>
			</view>
		</view>
		<x-modal  v-model="show1" title='前往登录' text='需要登录才能查看哦' @confirm="goLogin" />
		<x-loading text="加载中.." mask="true" click="true" ref="loading"></x-loading>
	</view>
</template>

<script>
	// 获取个人信息
	import { getProfileData,saveFiles } from '@/network/getProfileData'
	
	// 获取新消息提醒
	import {userMessages} from '@/network/getProfileData'
	
	// 导入vuex
	import { mapGetters,mapState } from 'vuex'
	
	// 导入工具类
	import { replaceImage } from '@/utils/dealUrl'
	
	// 导入公共类
	import { HOST } from '@/common/const'
	
	//保存头像到本地
	import { saveAvatar } from '@/utils/storage'
	
	// 导入分享方法
	import share from "@/common/share.js";
	
	export default{
		data(){
			return{
				mytrue:false,//是否登录
				username:"",
				avatar:"",
				integral:"",
				now_money:"",
				sex:"",
				product_collect:"",
				shop_collect:"",
				visit:"",
				uid:'',
				red_packet:'',
				show1:false,
				isLoading:false,
				showRed:true
			}
		},
		onShow() {
			if(!this.isToken){
				this.mytrue = false
			}else{
				this.mytrue = true
				// 刷新用户数据
				this.getProfileData()
				// 读取消息
				this.userMessages(this.isToken)
				console.log(this.showRed)
			}
		},
		// #ifndef MP
		onNavigationBarButtonTap(e){
			if(!this.mytrue){
				this.show1 = true
				return 
			}
			const index = e.index
			if(index == 0){
				//消息
				uni.navigateTo({
					url:'Inform/inform'
				})
			}else{
				//设置
				uni.navigateTo({
					url:'Seeting/seeting'
				})
			}
		},
		// #endif
		// #ifdef APP-PLUS
		watch:{
			showRed(newValue,oldValue){
				console.log(newValue)
				const pages = getCurrentPages()
				const page = pages[pages.length - 1]
				const currentWebView = page.$getAppWebview()
				if(newValue){
					// 隐藏navBar小红点
					this.showTabbar(currentWebView)
				}else{
					// 显示navBar小红点
					this.hideTabbar(currentWebView)
				}
			}
		},
		// #endif
		methods:{
			// 隐藏小红点
			showTabbar(currentWebView){
				console.log(currentWebView)
				currentWebView.showTitleNViewButtonRedDot({
					index:0
				})
			},
			// 显示小红点
			hideTabbar(currentWebView){
				console.log(currentWebView)
				currentWebView.hideTitleNViewButtonRedDot({
					index:0
				})
			},
			// 新消息提醒
			userMessages(token){
				userMessages(token).then(res => {
					if(res.data.code == 200){
						const data = res.data.data
						if(data.orderNotice == 0 && data.payRefunNotice == 0 && data.shopNotice == false && data.systemNotice == 0 && this.allUnabsorbed == 0){
							this.showRed = false
						}else{
							this.showRed = true
						}
					}
				})
			},
			// 登录网络方法
			getProfileData(userData){
				const token = this.isToken
				getProfileData(token).then(res => {
					if(res.data.code == 200){
						const obj = res.data.data
						// 获取网络头像
						obj.avatar = replaceImage(obj.avatar)
						
						// // 获取本地缓存的用户信息
						// uni.getStorageSync()
						
						
						// 更新vuex中的数据，并且异步存入缓存
						this.$store.commit('setUserData',obj)

						// 读取缓存
						uni.getStorage({
							key:obj.account,
							success:(res) => {
								const data = res.data
								data.Message_key = obj
								// 存入缓存
								uni.setStorage({
									key:obj.account,
									data:data,
									success:() =>{
										console.log('本地缓存更新成功')
									} 
								})
							},
							fail:(err) => {
								console.log(err)
							}
						})
					}
				})
			},
			//点击登录
			loginClick(){
				uni.navigateTo({
					url:'../login/login'
				})
			},
			//商品收藏
			collectClick(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'MyshopMessage/collect'
				})
			},
			// 判断用户头像是否相同
			dealUserAvatar(userData,obj){
				return userData.avatar == obj.avatar
			},
			//店铺收藏
			attrntionClick(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'MyshopMessage/attention'
				})
			},
			//积分商城
			integralShopCick(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'../Home/Integral/integralShop'
				})
			},
			//优惠券
			discountsClick(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'Discounts/mydiscounts'
				})
			},
			//足迹
						footprintClick(){
							if(!this.mytrue){
								this.show1 = true
								return 
							}
							uni.navigateTo({
								url:'Footprint/footprint'
							})
						},
			//我的订单
			myOrderClick(num){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'MyOrder/myorder?index=' + num
				})
			},
			//退款
			refundClick(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'MyBooking/refund/refund'
				})
			},
			//我的拼团
			MybookingClick(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'MyBooking/mybooking'
				})
			},
			//商家入驻
			enterClick(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'Enter/enter'
				})
			},
			//我的地址
			addressClick(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'address/AllAddress'
				})
			},
			//积分明细
			intrgralDetailClick(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'../Home/Integral/intrgralDetail'
				})
			},
			//领券中心
			discountscententClick(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'Discounts/DiscpuntsCentent'
				})
			},
			// 我的足迹
			my_zuji(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:''
				})
			},
			// 跳转用户设置
			mySet(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'Mydata/mydata'
				})
			},
			// 分享好友
			shareObj(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				const that = this
				const spread_uid = that.uid
				let shareInfo={
					href:`http://jn.51kdd.com/index.html#/?spread_uid=${spread_uid}`,
					title:'老家商城',
					desc:'快来玩呀',
					imgUrl:'/static/logo/logo.png'
				};
				let shareList=[
					{
						icon:"/static/sharemenu/wechatfriend.png",
						text:"微信好友",
					},
					{
						icon:"/static/sharemenu/wechatmoments.png",
						text:"朋友圈"
					},
					{
						icon:"/static/sharemenu/copyurl.png",
						text:"复制"
					},
					{
						icon:"/static/sharemenu/more.png",
						text:"更多"
					},
				];
				this.shareObj=share(shareInfo,shareList,function(index){
					console.log("点击按钮的序号: " + index);
					let shareObj={
						href:shareInfo.href||"",
						title:shareInfo.title||"",
						summary:shareInfo.desc||"",
						success:(res)=>{
							console.log("success:" + JSON.stringify(res));
						},
						fail:(err)=>{
							console.log("fail:" + JSON.stringify(err));
						}
					};
					switch (index) {
						case 0:
							shareObj.provider="weixin";
							shareObj.scene="WXSceneSession";
							shareObj.type=0;
							shareObj.imageUrl=shareInfo.imgUrl||"";
							uni.share(shareObj);
							break;
						case 1:
							shareObj.provider="weixin";
							shareObj.scene="WXSenceTimeline";
							shareObj.type=0;
							shareObj.imageUrl=shareInfo.imgUrl||"";
							uni.share(shareObj);
							break;
						case 2:
							uni.setClipboardData({
								data:shareInfo.href,
								complete() {
									uni.showToast({
										title: "已复制到剪贴板"
									})
								}
							})
							break;
						case 3:
							plus.share.sendWithSystem({
								type:"web",
								title:shareInfo.title||"",
								thumbs:[shareInfo.imgUrl||""],
								href:shareInfo.href||"",
								content: shareInfo.desc||"",
							})
							break;
					};
				});
				this.$nextTick(()=>{
					this.shareObj.alphaBg.show();
					this.shareObj.shareMenu.show();
				})
			},
			erWeiShare(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'erweimaShare/erweimaShare'
				})
			},
			// 进入我的钱包
			goQianbao(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'MyBalance/MyBalance'
				})
			},
			goQiandao(){
				if(!this.mytrue){
					this.show1 = true
					return 
				}
				uni.navigateTo({
					url:'../Home/qiandao'
				})
			},
			goLogin(){
				uni.navigateTo({
					url:'../login/login'
				})
			}
		},
		computed:{
			...mapGetters(['isToken','isLookSettled','allUnabsorbed']),
			...mapState(['userData']),
			
		}
		
	}
</script>

<style lang="scss">
	.my-title-background{
		margin-bottom: 20upx;
		background: url(../../static/nologin.png) no-repeat;
		background-size: 100% 50%;
		width: 100%;
	}
	.my-head-image{
		width: 124upx;
		height: 124upx;
		border-radius: 50%;
	}
	.my-one-all{
		border-radius: 14upx;
		padding: 34upx  0 28upx 0;
		color: #686868;
	}
	.my-one-jifen{
		width:169upx;
		height:51upx;
		background:#FBF2F2;
		border-radius:25upx 0upx 0upx 25upx;
		z-index: 1;
		color: #686868;
		font-size: 24upx;
	}
	.my-one-jifen image{
		width:40upx;
		height:40upx;
	}
	.my-two-three-all{
		padding: 10upx 30upx 0 30upx;
	}
	.border-all{
		border-radius: 14upx;
	}
	.height-all{
		height: 80upx;
		padding: 0 38upx;
	}
	.my-image-two-all{
		width: 70upx;
		height: 70upx;
	}
	.my-three-all{
		height: 120upx;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		image{
			width: 60upx;
			height: 60upx;
		}
	}
	.my-two-bootom{
		padding-bottom: 76upx;
	}
	.Nologin{
		height:280upx;
		width: 100%;
		background: url(../../static/nologin.png) no-repeat;
		background-size: 100% 100%;
		padding-top: 80upx;
		padding-left: 200upx;
	}
	.Nologin-button{
		display: flex;
		align-items: center;
		justify-content: center;
		width:238upx;
		height:70upx;
		background:rgba(255,255,255,1);
		border-radius:35upx;
		color: #CD3233;
		font-size: 32upx;
		font-weight: bold;
	}
	.tixian{
		width: 60upx;
		height: 60upx;
	}
	.nicket{
		width: 290upx;
	}
</style>

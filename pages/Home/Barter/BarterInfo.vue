<template>
	<view>
		<!-- head -->
		<swiper class="screen-swiper square-dot" :indicator-dots="true" :circular="true"
		 :autoplay="true" interval="5000" duration="500">
			<swiper-item v-for="(item,index) in swiperList" :key="index" @tap="lookDetail(item,index)">
				<image :src="item" mode="aspectFill"></image>
			</swiper-item> 
		</swiper>
		<view class="bg-white padding-left">
			<view class="flex align-center">
				<view class="text-red-my text-xxxl text-bold">换物价格：{{productItem.price}}</view>
			</view>
			<view>
				<view class="flex align-center margin-top-sm margin-bottom-sm">
					<view class="flex-four text-three shopDetails-title-name">换物主题：{{productItem.title}}</view>
				</view>
				<view class="flex align-center margin-top-sm margin-bottom-sm">
					<view class="flex-four text-three shopDetails-title-name">想换的物品：{{productItem.goods_name}}</view>
				</view>
			</view>
		</view>
		<!-- head end -->
		
		<!-- 选择 -->
		<view @tap="outloginShopClick" class="bg-white margin-top-xs padding-bottom-sm">
			<view class=" flex align-center justify-between shopDetails-baozhanng">
				<view class="flex item-center detail-category">
					<text class="text-jiujiujiu margin-right">分类</text>
					<text v-if="attrList.length">共有{{attrList.length}}种类别</text>
					<text v-else >暂无类别</text>
				</view>
				<view class="lg text-jiujiujiu cuIcon-right"></view>
			</view>
		</view>
		<!-- 选择 end -->
		
		<!-- 推荐商品 end-->
		<view style="height: 74upx;color: #525253;" class="flex align-center justify-center">———— 商品详情 ————</view>
		<!-- 文本分割线 -->
		<view class="parse_box"  v-if="productItem.content">
			<parser :html="productItem.content"></parser>
		</view>
		<!-- 商品介绍 -->
		<view class="articles-title-img" >  
			<view v-for="(vo,key) in atricleMain" :key="key" >
				<view class="article-textarea" >
					<textarea class="article-textarea-textarea" maxlength="-1" v-if="vo.type == 'textarea'" :value="vo.value" auto-height style="width: 100%;" disabled=true></textarea>
				</view>
				<view class="article-textarea-img" v-if="vo.type == 'img'">
					<image @tap="previewClick(vo.value)" :src="vo.value" mode="widthFix"></image>
				</view>
			</view>
		</view>
		<!-- 商品介绍end -->
		<!-- 占位div -->
		<view style="height: 100upx;display: flex;justify-content: center;align-items: center;" class="text-gray">
			{{productItem.content ? '' : '暂无详情'}}
		</view>
		<!-- 底部操作条 -->
		<view class="cu-bar bg-white tabbar border shop shopDetails-bottom-all">
			<!-- <view class="flex flex-sub align-center text-xs ">
				<view @tap="shopClick(1)" class="margin-left-lg flex flex-direction align-center justify-center">
					<image class="shop-bottom-kefu" src="../../../static/shop.png"></image>
					<view>店铺</view>
				</view>
				<view @tap="serviceClick(1)" class="margin-left-lg flex flex-direction align-center justify-center">
					<image class="shop-bottom-kefu" src="../../../static/kefu.png"></image>
					<view>客服</view>
				</view>
				<view @tap="collectClick(1)" class="margin-left-lg flex flex-direction align-center justify-center">
					<image class="shop-bottom-kefu" src="../../../static/collect.png"></image>
					<view>收藏</view>
				</view>
			</view> -->
			<view @tap="outloginShopClick"  class="flex flex-sub align-center justify-center barter-nowbuy">
				<view>立即换物</view>
			</view>
		</view>
		<!-- 底部操作条end -->
		
		<!-- 分享 -->
		<uni-popup ref="popup" type="bottom" >
			<view @tap="closePopupsSharClick" class="lg text-gray cuIcon-roundclose margin-top-sm margin-right shopDetails-bottom-popups-clos"></view>
			<view class="share-popup-all">
				<view @tap="shareShowClick(key)" v-for="(vo,key) in shareList" :key="key" class="share-popup-sx">
					<image :src="vo.img"></image>
					<view style="font-size: 26upx;">{{vo.name}}</view>
				</view>
			</view>
		</uni-popup>
		
		<uni-popup ref="popupcenter" type="center" >
			<view class="margin">
				<view>请留下您的联系方式，方便我们工作人员联系您</view>
				<view class="gray">为了保护平台安全规范，请填写您的真实信息</view>
				<view class="solid-bottom margin-top-sm">
					<view class="flex align-center shopDetails-num">
						<view>
							<!-- <image src="../../../static/nameuser.png" class="barter-image"></image> -->
						</view>
						<view class="margin-lr-sm">姓名</view>
						<view style="flex: 1;">
							<input v-model="nameuser" type="text" confirm-type="done" class="text-width text-sm-erliu" placeholder="请填写您的姓名" />
						</view>
					</view>
				</view>
				<view class="solid-bottom">
					<view class="flex align-center shopDetails-num">
						<view>
							<!-- <image src="../../../static/phoneuser.png" class="barter-image"></image> -->
						</view>
						<view class="margin-lr-sm">手机号</view>
						<view style="flex: 1;">
							<input v-model="phoneuser" type="number" confirm-type="done" class="text-width text-sm-erliu" placeholder="请填写您的手机号" />
						</view>
					</view>
				</view>
				<button @tap="confirmClick" class="barter-button">确定</button>
			</view>
			
		</uni-popup>
		
		<!-- 选择商品属性 -->
		<uni-popup ref="popupbottom" type="bottom" >
			<view class="popupbottom-all">
				<view class="flex justify-between solid-bottom">
					<view class="flex align-end margin-bottom-lg ">
						<image class="popupbottom-shop-img" :src="swiperList[0]"></image>
						<view class="margin-left-sm">
							<view class="text-price text-red text-bold text-xl">{{attrInfo.price}}</view>
							<view class="text-sm text-cut" style="width: 320upx;" v-if="attrList.length">选择:  {{attrInfo.name}}</view>
							<view class="text-sm" v-else>暂无种类可选</view>
						</view>
					</view>
					<view @tap="closePopupsShopClick"  class="lg text-gray cuIcon-roundclose shopDetails-bottom-popups-clos"></view>
				</view>
				<view class="padding-bottom-sm solid-bottom">
					<view class=" margin-top-lg margin-bottom">种类</view>
					<view class="flex flex-wrap">
						<block v-for="(vo,key) in attrList" :key="key">
							<view @tap="selectShopClick(vo,key)" :class="[vo.used?'popupbottom-shop-type-select':'']" class="popupbottom-shop-type-all">
								名称：{{vo.name}}  规格：{{vo.num}}
							</view>
						</block>
					</view>	
				</view>
				<view class="flex align-center justify-between solid-bottom shopDetails-num">
					<view>换物数量</view>
					<view>
						<tui-numberbox :value="value"  @change="change"></tui-numberbox>
					</view>
				</view>
				<view class="flex align-center popupbottom-all-top">
					<view @tap="nowBuyClick" class="shopDetails-bottom-button">立即换物</view>
				</view>
			</view>
		</uni-popup>
		<x-loading text="加载中.." mask="true" click="true" ref="loading"></x-loading>
	</view>
</template>

<script>
	import tuiNumberbox from "@/components/numberbox/numberbox"
	import uniPopup  from "@/components/uni-popup/uni-popup"
	import parser from "@/components/jyf-Parser/index"
	
	// 导入分享方法
	import share from "@/common/share.js";
	
	// 导入网络请求方法
	import {getDetailData,sendApply} from '@/network/yiwu'
	
	// 导入工具类
	import { replaceImage } from '@/utils/dealUrl'
	export default{
		components: {
			uniPopup,
			tuiNumberbox,
			parser
		},
		data(){
			return{
				hasData:false,
				atricleMain:'',//文章主要内容
				payimgType:true,//图片或者视频
				swiperNum:1,//当前所在滑块
				videoUrl:'',
				shareList:[{
					img:'../../static/weixn.png',
					name:'微信好友'
				},{
					img:'../../static/pengyouq.png',
					name:'朋友圈'
				}],
				list:[{
					id:'0',
					title:'成人款A750g*1盒',
					type:false,
				},{
					id:'0',
					title:'儿童款B750g*1盒',
					type:false,
				},{
					id:'0',
					title:'混合款（15A+15B）750g*1盒',
					type:false,
				}],
				value:1,//步进器
				nameuser:'',//姓名
				phoneuser:'',//手机号,
				productItem:{},
				token:'',
				swiperList:[],
				attrList:[],
				currentAttr:-1
			}
		},
		onLoad(e) {
			const id = e.id
			this.token = this.$store.getters.isToken
			// 请求数据
			this.getDetailData(id)
		},
		onReady() {
			if(this.hasData == false){
				this.$refs.loading.open()
			}
		},
		computed:{
			attrInfo(){
				if(this.attrList.length){
					const obj = this.attrList.find(x => x.used)
					if(obj){
						return obj
					}else{
						return {
							price:this.productItem.price || '',
							name:''
						}
					}
				}else{
					return {
						price:this.productItem.price || '',
						name:''
					}
				}
			}
		},
		methods:{
			// 发送请求网络数据
			getDetailData(id){
				getDetailData(id,this.token).then(res => {
					this.$refs.loading.close()
					this.hasData = true
					if(res.data.code == 200){
						let obj = res.data.data	
						this.swiperList = obj.images.map(x => replaceImage(x))
						if(obj.info.length){
							obj.info.forEach(x => {
								x.used = false
							})
						}
						this.attrList = obj.info || []
						this.productItem = obj
					}
				})
			},
			//选择商品属性
			selectShopClick(vo,key){
				if(vo.used){
					vo.used = false
				}else{
					this.attrList.forEach(x => {
						this.$set(x,'used',false)
					})
					vo.used = true
				}
			},
			//滑块的change
			swiperChange(e){
				this.swiperNum = e.target.current
			},
			//点击kefu
			serviceClick(shopname){
				uni.navigateTo({
					url:'informtion/informtion?shopname=' + shopname
				})
			},
			//步进起数值修改
			change: function(e) {
				this.value = e.value
			},
			//立即购买
			nowBuyClick(){
				var that = this
				if(this.attrList.length){
					if(!this.attrList.some(x => x.used)){
						uni.showToast({
							title:'请至少选择一个商品类别哦',
							icon:'none'
						})
						return 
					}
				}
				
				
				
				
				
				that.closePopupsShopClick()
				setTimeout(function(){
					that.centerpopup()
				},500)
			},
			//提交信息
			confirmClick(){
				var regName =/^[\u4e00-\u9fa5]{2,4}$/;
				if(!regName.test(this.nameuser)){
					uni.showToast({
						title:'请认真填写姓名哦',
						icon:'none'
					})
					return
				}
				if(!(/^1[3456789]\d{9}$/.test(this.phoneuser))){
					uni.showToast({
						title:'请输入正确的手机号',
						icon:'none'
					})
					return
				}
				const obj = {
					name:this.nameuser,
					phone:this.phoneuser,
					b_id:this.productItem.id
				}
				const token = this.token
				// 发送请求
				sendApply(obj,token).then((res) => {
					if(res.data.code == 200){
						// #ifdef APP-PLUS
						plus.nativeUI.toast(res.data.msg,{duration:'long'})
						// #endif
						uni.navigateBack()
					}else{
						// #ifdef APP-PLUS
						plus.nativeUI.toast(res.data.msg,{duration:'long'})
						// #endif
					}
				})
				
			},
			//点击收藏
			collectClick(id){
				
			},
			//中部弹出
			centerpopup(){
				this.$refs.popupcenter.open()
			},
			//中部弹窗关闭
			centerpopupclose(){
				this.$refs.popupcenter.close()
			},
			//点击店铺
			shopClick(id){
				uni.navigateTo({
					url:'StoreDetails/storedetails?id=' + id
				})
			},
			//转发弹出
			outloginSharClick(){
				const that = this
				let shareInfo={
					href:`http://jn.51kdd.com/index.html#/?href=oldHome://abc`,
					title:'老家商城',
					desc:that.itemInfo.name,
					imgUrl:that.itemInfo.image
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
			//转发关闭
			closePopupsSharClick(){
				this.$refs.popup.close()
			},
			//商品属性弹出
			outloginShopClick(){
				this.$refs.popupbottom.open()
			},
			//商品属性关闭
			closePopupsShopClick(){
				this.$refs.popupbottom.close()
			},
			lookDetail(item,index){
				let arr = [];
				arr.push(item);
				uni.previewImage({
					current: item,
					urls: arr
				})
			}
		}
	}
</script>

<style>
	.shopDetails-title{
		position: relative;
	}
	.shopDetails-title-select{
		display: flex;
		align-items: center;
		justify-content: space-between;
		position: absolute;
		bottom: 30upx;
		width: 100%;
	}
	.shopDetails-title-play{
		width:110upx;
		height:48upx;
		background:rgba(242,237,234,1);
		border:1px solid rgba(203,191,180,1);
		opacity:0.86;
		border-radius:22upx;
		font-size: 20upx;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.select-title-type{
		color: #FFFFFF;
		background: #CD3233;
		border:1px solid #CD3233;
	}
	.shopDetails-title-num{
		width:110upx;
		height:48upx;
		background:rgba(0,0,0,1);
		opacity:0.35;
		border-radius:26upx;
		display: flex;
		align-items: center;
		justify-content: center;
		color: #FFFFFF;
		margin-right: 26upx;
	}
	.shopDetails-title-left{
		width:110upx;
		height:48upx;
		margin-left: 26upx;
		visibility: hidden;
	}
	.shopDetails-title-package{
		width:160upx;
		height:36upx;
		border-radius: 24upx;
		display: flex;
		align-items: center;
		justify-content: center;
		margin-left: 24upx;
	}
	.shopDetails-title-original{
		color: #999;
		font-size: 26upx;
		text-decoration: line-through;
	}
	.shopDetails-title-height{
		height: 40upx;
	}
	.shopDetails-title-name{
		color: #525253;
		font-weight: bold;
	}
	.shopDetails-title-shar{
		width:80upx;
		height:52upx;
		background: #F8F8F8;
		border-top-left-radius: 100upx;
		border-bottom-left-radius: 100upx;
		color: #999999;
	}
	.shopDetails-baozhanng{
		height: 80upx;
		padding:0 30upx;
	}
	.shopDetails-image{
		height: 70upx;
		width: 70upx;
		margin-right: 10upx;
	}
	.shopDetails-image-select{
		width:230upx;
		height:60upx;
		background:rgba(246,246,246,1);
		border-radius:12upx;
		display: flex;
		align-items: center;
		justify-content: center;
		color: #999999;
		font-size: 24upx;
	}
	.shopDetails-select-image{
		margin-left: 104upx;
		margin-right: 56upx;
	}
	.flex-yidw{
		flex: 1.5;
	}
	.comment-image-all{
		height: 50upx;
		width: 50upx;
	}
	.shop-introduce-img{
		height: 88upx;
		width: 88upx;
	}
	.shop-deleat-all{
		padding: 36upx 20upx 24upx 30upx;
	}
	.shop-detal-name{
		color: #636362;
		font-size: 32upx;
		font-weight: bold;
	}
	.shop-experience{
		background:rgba(0,0,0,0.1);
		border-radius: 14upx;
		width: 220upx;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.shop-pingfen{
		display: flex;
		align-content: center;
		justify-content: center;
		background: #EEEEEE;
		width:30upx;
		height:30upx;
	}
	.tuijian-shop-three-image{
		width:228upx;
		height:218upx;
	}
	.cu-bar.tabbar.border .action::before{
		border: none !important;
	}
	.shopDetails-bottom-button{
		display: flex;
		align-items: center;
		justify-content: center;
		width: 100%;
		border-radius: 100upx;
		background:rgba(205,50,51,1);
		font-size: 32upx;
		color: #FFFFFF;
		border: none;
		height: 80upx;
		
	}
	.button-left{
		background: #F88029;
		border-top-left-radius: 100upx;
		border-bottom-left-radius: 100upx;
	}
	.button-right{
		background: #CD3233;
		border-top-right-radius: 100upx;
		border-bottom-right-radius: 100upx;
		margin-right: 18upx;
	}
	.shopDetails-bottom-all{
		position: fixed;
		bottom: 0;
		width: 100%;
		z-index: 99;
		height: 80upx;
	}
	.shop-bottom-kefu{
		width: 50upx;
		height: 50upx;
	}
	.shopDetails-bottom-popups{
		margin: 36upx 24upx 50upx 24upx;
	}
	.shopDetails-bottom-popups-clos{
		font-size: 50upx;
		text-align: right;
	}
	.shopDetails-bottom-popups-jifen{
		width:80upx;
		height:32upx;
		border-radius: 24upx;
		text-align: center;
		line-height: 32upx;
		margin-right: 20upx;
	}
	.share-popup-all{
		height: 200upx;
		color: #999999;
		font-size: 24upx;
		display: flex;
		align-items: center;
		justify-content: space-around;
	}
	.share-popup-all image{
		width: 78upx;
		height: 78upx;
		margin-bottom: 24upx;
	}
	.share-popup-sx{
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}
	.popupbottom-shop-img{
		width:224upx;
		height:224upx;
		border-radius:12upx;
		border: #F3F3F3 1px solid;
	}
	.popupbottom-all{
		padding: 12upx 20upx 16upx 20upx;
	}
	.popupbottom-shop-type-all{
		height: 60upx;
		background: #F5F5F5;
		color: #555555;
		padding: 0 26upx;
		display: flex;
		align-items: center;
		justify-content: center;
		border-radius: 12upx;
		font-size: 24upx;
		margin-left: 22upx;
		margin-bottom: 18upx;
		border: #F5F5F5 1px solid;
	}
	.popupbottom-shop-type-select{
		border: #CD3233 1px solid;
		background: #F8E0DF;
		color: #CD3233;
	}
	.shopDetails-num{
		height: 94upx;
		color: #333333;
		font-size: 30upx;
	}
	.popupbottom-all-top{
		margin-top: 120upx;
	}
	.barter-nowbuy{
		height: 100%;
		background:rgba(205,50,51,1);
		font-size:36upx;
		color:rgba(255,255,255,1);
	}
	.barter-image{
		height: 40upx;
		width: 40upx;
	}
	.barter-button{
		margin-top: 40upx;
		background:rgba(205,50,51,1);
		border-radius:46px;
		color: #FFFFFF;
		font-size: 36upx;
	}
	.detail-category{
		flex: 1;
	}
	.parse_box{
		box-sizing: border-box;
		padding: 0 20upx;
	}
</style>

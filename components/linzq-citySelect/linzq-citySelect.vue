<template>
	<div class="wrapper" :style="'top:'+statusBarHeight+'px'">
		<div class="header">
			<view class="back_div">
				<image class="back_img" @click="back_city()" src="/static/back_img.png" mode=""></image>
			</view>
			<input class="input" @input="onInput" placeholder="中文/拼音/首字母" v-model="searchValue" />
		</div>
		<scroll-view class="calendar-list" scroll-y="true" :scroll-into-view="scrollIntoId" v-if="isShow">
			<view v-if="disdingwei" id="hot">
				<!-- 定位模块 -->
				<view class="dingwei">
					<view class="dingwei_Tips">
						当前定位
					</view>
					<view class="dingwei_city">
						<view v-if="position" class="dingwei_city_one"  @click="showCity">
							{{position}}
						</view>
						<view v-else class="cu-tag radius">
							获取定位中...
						</view>
						<view class="dingweis_div" @click="getWarpweft">
							<image class="dingweis" src="/static/dingweis.png" mode=""></image>
							<text>{{po_tips}}</text>
						</view>
					</view>
				</view>

				<!-- 最近模块 -->
				<view class="dingwei" v-if="Visit.length>=0">
					<view class="dingwei_Tips">
						最近访问
					</view>
					<view class="dingwei_city dingwei_city_zuijin">
						<view class="dingwei_city_one toright" v-for="(item,index) in Visit" v-if="index<2" @click="back_city(item)">
							{{item.cityName}}
						</view>
					</view>
				</view>

			</view>


			<!-- 城市列表 -->
			<view v-if="searchValue == ''" v-for="(item, index) in list" :id="getId(index)" :key="index">
				<view class="letter-header">{{ getId(index) }}</view>
				<view class="city-div" v-for="(city, i) in item" :key="i" @click="back_city(city)">
					<text class="city">{{ city.cityName }}</text>
				</view>
			</view>
			<!-- 搜索结果 -->
			<view class="city-div" v-for="(item, index) in searchList" @click="back_city(item)">
				<text class="city">{{ item.cityName }}</text>
			</view>
		</scroll-view>

		<!-- 右侧字母 -->
		<view class="letters" v-if="searchValue == ''">
			<view class="letters-item" @click="scrollTo('hot')">最近</view>
			<view class="letters-item" v-for="item in letter" :key="item" @click="scrollTo(item)">{{ item }}</view>
		</view>

		<!-- 选中之后字母 -->
		<view class="mask" v-if="showMask">
			<view class="mask-r">{{selectLetter}}</view>
		</view>
	</div>
</template>

<script>
	import Citys from '@/js_sdk/city.js';
	import {uniqueArray} from '@/utils/dealData'
	export default {
		components: {},
		computed: {
			hotCity() {
				return Citys.hotCity;
			},

			citys() {
				return Citys.cities;
			}
		},
		data() {
			return {
				statusBarHeight: this.statusBarHeight,
				ImgUrl: this.ImgUrl,
				letter: [],
				selectLetter: '',
				searchValue: '',
				scrollIntoId: '',
				list: [],
				tId: null,
				searchList: [],
				showMask: false,
				disdingwei: true,
				Visit: [], //最近访问
				position: '',
				longitude: '', //经度
				latitude: '', //纬度
				seconds: 3,
				po_tips: '重新定位',
				addressList:[],
				isShow:true
			}
		},
		created() {
			//获取存储的最近访问
			var that = this
			uni.getStorage({
				key: 'Visit_key',
				success: function(res) {
					that.Visit = res.data
				}
			});
			// 从缓存里读取城市
			const city = uni.getStorageSync('localCity')
			if(city){
				this.position = city.city
			}else{
				this.getWarpweft()
			}
			//获取city.js 的程序字母
			var mu = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'j', 'k', 'l', 'm', 'n', 'p', 'q', 'r', 's', 't', 'w', 'x', 'y',
				'z'
			];
			var tmp = [];
			for (var i = 0; i < mu.length; i++) {
				var item = mu[i];
				for (var j = 0; j < this.citys.length; j++) {
					var py = this.citys[j].py;
					if (py.substring(0, 1) == item) {
						if (tmp.indexOf(item) == -1) {
							this.list[i] = [this.citys[j]];
							tmp.push(item);
							this.letter.push(item.toUpperCase());
						} else {
							this.list[i].push(this.citys[j]);
						}
					}
				}
			}
		},
		methods: {
			getId(index) {
				return this.letter[index];
			},

			scrollTo(letter) {
				this.isShow = false
				this.showMask = true
				this.selectLetter = letter == 'hot' ? '最' : letter
				setTimeout(() => {
					this.showMask = false
				}, 300);
				this.scrollIntoId = letter;
				this.$nextTick(() => {
					this.isShow = true
				})
			},


			query(source, text) {
				let res = [];
				var self = this;
				res = source.filter(item => {
					const arr = [];
					let isHave = false;
					Object.keys(item).forEach(prop => {
						const itemStr = item[prop];
						self.isString(itemStr) &&
							itemStr.split(',').forEach(val => {
								arr.push(val);
							});
					});
					arr.some(val => {
						isHave = new RegExp('^' + text).test(val);
						return isHave;
					});
					return isHave;
				});
				console.log(JSON.stringify(res));
				return res;
			},

			isString(obj) {
				return typeof obj === 'string';
			},

			onInput(e) {
				const value = e.target.value;
				console.log(value);
				if (value !== '' && this.citys && this.citys.length > 0) {
					const queryData = this.query(this.citys, String(value).trim());
					this.searchList = queryData;
					this.disdingwei = false
				} else {
					this.searchList = [];
					this.disdingwei = true
				}
			},

			back_city(item) {
				if (item) {
					this.$emit('back_city', item);
					//unshift 把数据插入到首位，与push相反
					this.Visit.unshift(item)
					this.searchValue = "";
					this.disdingwei = true
					var arr = uniqueArray(this.Visit,'cityName') 
					if(arr.length > 5){
						arr = arr.slice(0,5)
					}
					this.Visit = arr
					uni.setStorage({
						key: 'Visit_key',
						data: this.Visit
					});
				} else {
					this.$emit('back_city', 'no');
				}

			},
			showCity(){
				this.$emit('showCity',this.position)
			},
			getWarpweft() {
				var that = this
				if(that.po_tips == '定位中...') return 
				that.po_tips = '定位中...'
				this.position = ''
				this.$store.dispatch('getWarpweft').then(res => {
					const obj = res
					that.longitude = obj.point.longitude
					that.latitude = obj.point.latitude
					that.position = obj.city
					that.po_tips = '重新定位'
				}).catch(err => {
					if(err == 0){
						// #ifdef APP-PLUS
						plus.nativeUI.toast('获取位置信息失败，请检查您的网络和定位是否开启',{duration:'long'})
						// #endif
						this.position = '获取定位失败'
						that.po_tips = '重新定位'
					}
					
				})
			}
		}
	};
</script>

<style scoped>
	.wrapper {
		position: fixed;
		z-index: 999999;
		background: #ffffff;
		height: 100%;
		width: 100%;
		top: 0px;
		left: 0px;
	}

	.mask {
		position: absolute;
		bottom: 0upx;
		top: 83upx;
		left: 0upx;
		right: 0upx;
		width: 750upx;
		display: flex;
		justify-content: center;
		align-items: center;
		background: rgba(0, 0, 0, 0);
	}

	.mask-r {
		height: 120upx;
		width: 120upx;
		border-radius: 60upx;
		display: flex;
		background: rgba(0, 0, 0, 0.5);
		justify-content: center;
		align-items: center;
		font-size: 40upx;
		color: #FFFFFF
	}

	.content {
		height: 100%;
		width: 100%;
		background-color: #ffffff;
	}

	.header {
		height: 85upx;
		display: flex;
		justify-content: flex-start;
		align-items: center;
	}


	.back_div {
		width: 65upx;
		height: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.back_img {
		width: 35upx;
		height: 35upx;
	}

	.input {
		font-size: 28upx;
		width: 620upx;
		height: 55upx;
		border-radius: 40upx;
		background-color: #F5F5F5;
		padding-left: 20upx;
		padding-right: 20upx;
		box-sizing: border-box;
	}

	.title {
		font-size: 30upx;
		color: white;
	}

	.show {
		left: 0;
		width: 100%;
		transition: left 0.3s ease;
	}

	.hide {
		left: 100%;
		width: 100%;
		transition: left 0.3s ease;
	}


	.title {
		font-size: 30upx;
		color: white;
	}

	.calendar-list {
		position: absolute;
		top: 83upx;
		bottom: 0upx;
		width: 100%;
		background-color: #FFFFFF;
	}

	.letters {
		position: absolute;
		right: 30upx;
		bottom: 0px;
		width: 50upx;
		top: 260upx;
		color: #2f9bfe;
		text-align: center;
		font-size: 24upx;
	}

	.letters-item {
		margin-bottom: 5upx;
	}

	.letter-header {
		height: 45upx;
		font-size: 22upx;
		color: #333333;
		padding-left: 24upx;
		box-sizing: border-box;
		display: flex;
		align-items: center;
		background-color: #ebedef;

	}

	.city-div {
		width: 660upx;
		height: 85upx;
		margin-left: 24upx;
		border-bottom-width: 0.5upx;
		border-bottom-color: #ebedef;
		border-bottom-style: solid;
		display: flex;
		align-items: center;
		margin-right: 35upx;
	}

	.city {
		font-size: 28upx;
		color: #000000;
		padding-left: 30upx;
	}

	.dingwei {
		width: 100%;
		padding-top: 25upx;
		box-sizing: border-box;
		margin-bottom: 26upx;
	}

	.dingwei_Tips {
		margin-left: 24upx;
		margin-bottom: 24upx;
		font-size: 24upx;
		color: #A5A5A5;
	}

	.dingwei_city {
		width: 100%;
		height: 60upx;
		padding-left: 55upx;
		padding-right: 70upx;
		box-sizing: border-box;
		display: flex;
		justify-content: space-between;
	}

	.dingwei_city_one {
		width: 185upx;
		height: 60upx;
		background-color: #F5F5F5;
		border-radius: 10upx;
		font-size: 32upx;
		color: #333333;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.dingweis_div {
		display: flex;
		align-content: flex-end;
		align-items: center;
		font-size: 24upx;
		color: #FD5745;
	}

	.dingweis {
		width: 32upx;
		height: 32upx;
	}

	.dingwei_city_zuijin {
		display: flex;
		justify-content: flex-start;
	}

	.toright {
		margin-right: 25upx;
	}
	.get-location{
		
	}
</style>

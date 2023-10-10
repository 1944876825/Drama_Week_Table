<template>
	<view class="content">
		<view class="header" style="height: 120rpx;">
			<view style="position: fixed;top: 0;z-index: 1; background-color: #fff;width: 100%;">
				<view class="week" style="margin-left: 10rpx;margin-top: 20rpx;">
					<scroll-view scroll-x="true" 
						scroll-left="80"
						lower-threshold="30" 
						@scrolltolower="getWeekList('+')" 
						upper-threshold="30" 
						@scrolltoupper="getWeekList('-')">
						<view class="tabContent" style="display: flex; flex-direction: row;">
							<view v-for="week in weekList" :class="dqweek == week['day'] ? 'actweekbox' : 'weekbox'" @tap="chweek(week)">
								<view class="wdate">
									{{ week['day'] }}
								</view>
								<view class="wweek">
									{{ week['week'] }}
								</view>
							</view>
						</view>
					</scroll-view>
				</view>
			</view>
		</view>
		<view class="center">
			<view class="mvList">
				<view class="mvbox" v-for="(vod, index) in mvList" @tap="play(vod['vod_id'])">
					<view class="mvleft">
						<view class="imgbox">
							<img class="mvimg" :src="vod['vod_pic']" @error="imgErr(index)" alt="" >
						</view>
					</view>
					<view class="mvright">
						<view class="tipbox">
							<view class="name">
								{{ vod['vod_name'] }}
							</view>
							<view class="remarks">
								{{ vod['vod_remarks'] }}
							</view>
							<view class="weekday">
								{{ vod['vod_weekday'] }}
							</view>
							<view class="blurb">
								{{ vod['vod_blurb'] }}
							</view>
						</view>
					</view>
				</view>
			</view>
		</view>
		<view class="bottom">
			<view class="dtips">
				{{ tips }}
			</view>
			<view class="scrollTopBtn" @tap="scrollTop" v-show="showTopIcon">
				<image src="../../static/images/j.png" mode=""></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				weekList: [],
				mvList: [],
				tabScrollInto: '',
				lastdaytime: 0,
				olddaytime: 0,
				dqweek: '',
				pg: 1,
				dqweekday: '',
				tips: '正在加载...',
				showTopIcon: false
			}
		},
		onLoad() {
			this.getWeekList('-')
			this.getWeekList('+')
		},
		onReachBottom() {
			this.getVodList()
			this.showTopIcon = true
		},
		onPullDownRefresh() {
			this.showTopIcon = false
			this.mvList = []
			this.pg = 1
			this.tips = '正在加载...'
			this.getVodList()
			setTimeout(function() {
				uni.stopPullDownRefresh();
			},500);
		},
		methods: {
			imgErr(index) {
				this.mvList[index]['vod_pic'] = getApp().globalData.defineImg
			},
			chweek(week) {
				this.scrollTop()
				this.pg = 1
				this.tips = '正在加载...'
				this.showTopIcon = false
				this.dqweek = week['day']
				this.mvList = []
				this.dqweekday = week['week']
				this.getVodList()
			},
			scrollTop() {
				uni.pageScrollTo({
					scrollTop: 0,
					duration: 300
				});
				this.showTopIcon = false
			},
			play(id) {
				window.location = getApp().globalData.Scheme + '://' + getApp().globalData.Host + '/navigate/video?id=' + id
			},
			getVodList() {
				uni.request({
					url: getApp().globalData.cms + 'addons/apptov3/app.php/Aapi/dateList?weekday='+this.dqweekday.replace('周', '')+'&pg='+this.pg,
					dataType: 'json',
					success:res=>{
						this.mvList = [...this.mvList, ...res.data['list']]
						this.pg++
						if (res.data['total'] <= this.mvList.length) {
							this.tips = '我也是有底线的...'
						}
					}
				})
			},
			// 获取7天
			getWeekqq(dateString) {
				var weekDay = ["周日", "周一", "周二", "周三", "周四", "周五", "周六"];  
				var myDate = new Date(Date.parse(dateString));  
				return weekDay[myDate.getDay()]
			},
			getWeekList(sf) {
				var date = new Date()
				var oneDay = 24 * 3600 * 1000
				if (this.lastdaytime == 0) {
					this.lastdaytime = Date.parse(date)// 转换为时间戳
					this.olddaytime = this.lastdaytime - oneDay
				}
				for (var i = 0; i < 5; i++) { //今天以及后6天
					if (sf == '-') {
						var now = new Date(this.olddaytime);
					} else {
						var now = new Date(this.lastdaytime);
					}
					var myear = now.getFullYear();
					var month = now.getMonth() + 1;
					var mday = now.getDate()
					var mn = [myear, month >= 10 ? month : '0' + month, mday >= 10 ? mday : '0' + mday].join('-')
					if (this.dqweek == '' && sf != '-') {
						this.dqweek = [month, mday].join('/')
						this.dqweekday = this.getWeekqq(mn)
						this.getVodList()
					}
					if (sf == '-') {
						this.weekList.unshift({
							data: mn,
							week: this.getWeekqq(mn),
							day: [month, mday].join('/')
						})
						this.olddaytime -= oneDay
					} else {
						this.weekList.push({
							data: mn,
							week: this.getWeekqq(mn),
							day: [month, mday].join('/')
						})
						this.lastdaytime += oneDay
					}
				}
			},
		}
	}
</script>

<style scoped lang="scss">
	/deep/ ::-webkit-scrollbar {
		width: 4px !important;
		height: 1px !important;
		overflow: auto !important;
		background: transparent !important;
		-webkit-appearance: auto !important;
		display: block;
	}
	.header {
		margin-top: 10rpx;
		.weekbox {
			margin: 10rpx 7rpx;
			padding: 20rpx 12rpx;
			border: 1px solid $blbl;
			border-radius: 20rpx;
			color: $blbl;
			text-align: center;
			.wdate {
				font-size: 10px;
			}
			.wweek {
				width: 50rpx;
				margin: 0 auto;
				font-size: 12px;
				font-weight: 600;
			}
		}
		.actweekbox {
			margin: 10rpx 7rpx;
			padding: 12rpx;
			border-radius: 20rpx;
			background-color: $blbl;
			color: #fff;
			text-align: center;
			.wdate {
				font-size: 10px;
			}
			.wweek {
				width: 50rpx;
				margin: 0 auto;
				font-size: 12px;
				font-weight: 600;
			}
		}
	}
	.mvList {
		margin-top: 30rpx;
	}
	.mvbox {
		width: 90%;
		margin: 12rpx auto;
		border: 1px solid $blbl;
		border-radius: 30rpx;
		padding-top: 20rpx;
		padding-right: 20rpx;
		height: 290rpx;
		box-shadow: 2rpx 3rpx 3rpx $blbl;
		.mvleft {
			float: left;
			width: 40%;
			height: 300rpx;
		}
		.mvright {
			float: right;
			width: 60%;
			height: 300rpx;
			position: relative;
			.tipbox {
				padding: 10rpx 0;
				.name {
					font-size: 16px;
					font-weight: 600;
					overflow: hidden;
					white-space: nowrap;
					text-overflow: ellipsis;
				}
				.remarks {
					margin-top: 40rpx;
					font-size: 10px;
					color: $blbl;
					overflow: hidden;
					white-space: nowrap;
					text-overflow: ellipsis;
				}
				.weekday {
					font-size: 10px;
					color: $blbl;
					overflow: hidden;
					white-space: nowrap;
					text-overflow: ellipsis;
				}
				.blurb {
					bottom: 30rpx;
					position: absolute;
					font-size: 12px;
					overflow: hidden;
					text-overflow: ellipsis;
					width: 100%;
					display: -webkit-box;
					-webkit-box-orient: vertical;
					-webkit-line-clamp: 3;
					word-break: break-all;
				}
			}
		}
		.imgbox {
			text-align: center;
			.mvimg {
				width: 70%;
				border-radius: 10rpx;
			}
		}
	}
	.scrollTopBtn {
		width: 80rpx;
		height: 80rpx;
		position: fixed;
		right: 30rpx;
		bottom: 60rpx;
		border-radius: 50%;
		background-color: $blbl;
		
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		
		image {
			width: 50rpx;
			height: 50rpx;
			border-radius: 50%;
		}
	}
	.dtips {
		color: $blbl;
		font-size: 12px;
		text-align: center;
		height: 50rpx;
		margin-top: 20rpx;
	}
</style>

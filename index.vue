<template>
	<view>
		
		
		 <view class="" style="position: relative;z-index: 0;">
			<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000">
				<swiper-item v-for="(item,index) in swiperlist" :key="index">
					<view class="swiper-item">
						<image :src="item.img" mode="widthFix"></image>
					</view>
				</swiper-item>
				
			</swiper>
			<uni-grid :column="4" :showBorder="false" style="margin-top: 20rpx;background: #fff;">
				<uni-grid-item v-for="(item,index) in catelist" :key="index" style="height: 160rpx;">
					<view class="grid-box">
						<image :src="item.img" ></image>
						<view class="grid-txt">{{item.title}}</view>
					</view>
				</uni-grid-item>
			</uni-grid>
			
			<dian title='热门商品'></dian>
			<!-- 商品列表 -->
			<view class="goods-content">
				<view class="goods-item" v-for="(item,index) in goodsinfo" :key="index" @tap="godetail(item.id)">
					<image :src="item.img" ></image>
					<view class="goods-title">{{item.title}}</view>
					<view class="goods-info">
						<view class="goods-price">￥{{item.price}}</view>
						<view class="goods-salenum">已售{{item.sell_num}}件</view>
					</view>
				</view>
			</view>
			<view class="" style="text-align: center;padding-bottom: 20rpx;color: #999;">———— ~~{{loadtext}}~~ ————</view>
				
		</view>
		 
	</view>
</template>

<script>
	export default {
		data() {
			return {
				swiperlist:[],
				catelist:[],
				form: {
					pageNum: 1, 
					pageSize: 3
				},
				loadtext:'加载更多...',
				status: 'more',
				total: 0,
				goodsinfo:[]
			}
		},
		onLoad() {
			this.getswiper()
			this.getcate()
			this.goodslist()
		},
		onReachBottom() {
			
		// 加判断： 页码数 * 每一页获取数据的条数 >= 总条数，如果符合条件说明数据已经全部加载完毕
			if (this.form.pageNum*this.form.pageSize >= this.total) {
				this.loadtext = "到底了"
				return
			} else{
			  this.status = "加载中..."  
			  this.form.pageNum++   // 每触发一次触底事件，页码加1
			  this.goodslist() // 请求接口，获取数据  
			}
		},
		methods: {
			getswiper(){
				this.$http.request({
					url: 'swiperlist'
				}).then(res => {
					if(res.code==200){
						this.swiperlist = res.data
						uni.setStorageSync('swiperlist',res.data)
						
					}
				})
			},
			getcate(){
				this.$http.request({
					url: 'catetop'
				}).then(res => {
					if(res.code==200){
						
						this.catelist = res.data
						uni.setStorageSync('catelist',res.data)
						
					}
				})
			},
			goodslist(){
				uni.showLoading({
				  title: '加载中...',
				  mask: true, // 是否显示透明蒙层，防止触摸穿透
				})
				this.$http.request({
					url: 'goodslist',
					method:'post',
					data:this.form
				}).then(res => {
					if(res.code==200){
						if (this.form.pageNum == 1) {
							this.total = res.count;
							this.goodsinfo=res.data
							uni.setStorageSync('goodsinfo',res.data)
						} else {
							var list =res.data
							this.goodsinfo = [...this.goodsinfo, ...list]
						}
						
						
					}else{
						uni.showToast({ title: data.msg, icon: 'none' });
					}
				})
				uni.hideLoading()
			},
			godetail(id){
				uni.navigateTo({
					url:'/pages/goodsdetail/goodsdetail?id='+id
				})
			}
			
		
		}
	}
</script>

<style lang="scss" scoped>
	page{background-color: #f8f8f0;}
	swiper{
		height: 400rpx;
		image{
			width: 100%;
		}
	}
	.grid-box{
		display: flex;flex-direction: column;align-items: center;justify-content: center;padding: 20rpx;
		image{width: 80rpx;height: 80rpx; border-radius: 40rpx;}
		.grid-txt{color: #333;font-size: 28rpx;margin-top: 10rpx;}
	}
	.goods-content{
		display: flex;flex-wrap: wrap;
		
		.goods-item{width:345rpx;border: 1px solid #eee;margin-top:20rpx;padding: 8rpx;box-sizing: border-box;border-radius: 10rpx;
			background: #fff;
			image{width:325rpx;height: 325rpx; border-radius: 10rpx;}
			.goods-title{font-size: 30rpx;font-weight: bold;}
			.goods-info{display: flex;justify-content: space-between;font-size: 24rpx;margin: 10rpx 0;align-items: center;
				.goods-price{color: red;font-size: 28rpx;}
			}
			
		}
		.goods-item:nth-child(odd){margin: 0 20rpx;margin-top:20rpx ;}
		
	}
</style>

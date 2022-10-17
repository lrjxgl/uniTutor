<template>
	<view>
		<view v-if="flashList.length>0">
			<swiper :style="{height:swipeHeight+'px'}" :indicator-dots="true" :autoplay="true" :interval="3000"
				:duration="1000">
				<swiper-item v-for="(item,key) in flashList" :key="key">
					<view class="swiper-item">
						<image @click="gourl(item.link1)" :src="item.imgurl" class="imgWidth" mode="widthFix">
						</image>
					</view>
				</swiper-item>

			</swiper>
		</view>
		<view v-if="navList.length>0" class="m-navPic mgt-5 mgb-5">
			<navigator v-for="(item,key) in navList" :key="key" :url="item.link1" class="m-navPic-item">
				<image class="m-navPic-img" mode="widthFix" :src="item.imgurl"></image>
				<view class="m-navPic-title">{{item.title}}</view>
			</navigator>

		</view>
		<div v-if="adList.length>0" class="adBox">
			<div v-for="(item,index) in adList" :key="index" class="adBox-item">
				<image @click="gourl(item.link1)" :src="item.imgurl" mode="widthFix" class="adBox-img"></image>
			</div>

		</div>
		<div>
			<div class="pd-10 fw-600">课程列表</div>
			
			<div v-for="(item,index) in lessonList" :key="index" class="sglist-item ">
			
				<div class="flex flex-ai-center">
					<image @click="goLesson(item.lessonid)" :src="item.imgurl+'.100x100.jpg'" mode="widthFix"
						class="flexlist-img"></image>
					<div class="flex-1">
						<div @click="goLesson(item.lessonid)" class="flexlist-title">{{item.title}}</div>
						<div class="flex mgb-10">
							<div class="cl2 mgr-5">库存</div>
							<div class="cl-money mgr-10">{{item.total_num}}份</div>
			
							<div class="cl2 mgr-5">已售</div>
							<div class="cl-num mgr-10">{{item.sold_num}}份</div>
						</div>
			
						<div class="flexlist-desc">{{item.description}}</div>
					</div>
					<div>
						<div class="mgb-10 cl2">教师：{{item.shop.title}}</div>
						<div @click="goLesson(item.lessonid)" class="btn-small btn-outline-primary">详情</div>
					</div>
			
				</div>
			
			</div>
		</div>
		
		<div>
			<div class="pd-10 fw-600">老师列表</div>
			
			<div v-for="(item,index) in shopList" :key="index" class="flexlist-item">
				<image @click="goShop(item.shopid)" :src="item.imgurl+'.100x100.jpg'" mode="widthFix" class="flexlist-img">
				</image>
				<div class="flex-1">
					<div @click="goShop(item.shopid)" class="flexlist-title">{{item.title}}</div>
					<div class="flex mgb-5">
						<div class="cl2 f12 mgr-5">评价</div>
						<div class="cl-num f12 mgr-10">{{item.raty_grade}}</div>
						<div class="cl2 f12 mgr-5">订单</div>
						<div class="cl-num f12 mgr-10">{{item.order_num}}</div>
					</div>
					<div class="flexlist-desc">
						{{item.description}}
					</div>
				</div>
			</div>
		</div>
		
		<tutor-footer tab="index"></tutor-footer>
	</view>
</template>

<script>
	import tutorFooter from "../tutor-footer.vue";
	export default {
		components: {
			tutorFooter
		},
		data: function() {
			return {
				pageLoad: false,
				flashList: [],
				navList: [],
				adList: [],
				swipeHeight: 380,
				lessonList: [],
				shopList: []
			}
		},
		onLoad: function() {
			var sys = uni.getSystemInfoSync();
			this.swipeHeight = sys.windowWidth / 2;
			this.getPage();
		},

		onShareAppMessage: function() {

		},
		methods: {
			gourl: function(url) {
				uni.navigateTo({
					url: url
				})
			},
			goShop: function(shopid) {
				uni.navigateTo({
					url: "../tutor_shop/index?shopid=" + shopid
				})
			},
			goLesson: function(lessonid) {
				uni.navigateTo({
					url: "../tutor_lesson/show?lessonid=" + lessonid
				})
			},
			getPage() {
				var that = this;
				that.app.get({
					url: that.app.apiHost + "/module.php?m=tutor",
					success: function(res) {

						if (res.data.flashList) {

							that.flashList = res.data.flashList;
						}
						if (res.data.navList) {
							that.navList = res.data.navList;
						}
						if (res.data.adList) {
							that.adList = res.data.adList;
						}
						that.lessonList = res.data.lessonList;
						that.shopList = res.data.shopList;
						that.pageLoad = true;
					}
				})
			}
		},
	}
</script>

<style>
</style>

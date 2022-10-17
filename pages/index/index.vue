<template>
	<view>
		 
		<swiper class="mgb-5" v-if="flashList.length>0" :style="{height:swiperHeight+'px'}"  :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000">
			<swiper-item v-for="(item,key) in  flashList" :key="key">
				<view class="swiper-item">
					<image @click="gourl(item.link1)" :src="item.imgurl" style="width:100%" mode="widthFix"></image>
				</view>
			</swiper-item>
		
		</swiper>
		<view v-if="navList.length>0" class="m-navPic mgb-5">
		
			<navigator v-for="(item,index) in navList" :key="index" :url="item.link1" class="m-navPic-item">
				<image mode="widthFix" class="m-navPic-img" :src="item.logo"></image>
				<view class="m-navPic-title">{{item.title}}</view>
			</navigator>
		</view>
		<view v-if="adList && adList.length>0" class="adBox bg-white mgb-5">
			<navigator v-for="(item,index) in adList" :key="index" :url="item.link1" class="adBox-item">
				<image :src="item.imgurl" mode="widthFix" class="adBox-img"></image>
			</navigator>
		</view> 
		 
		
		<view class="sglist">
			<view class="emptyData" v-if="list==null || Object.keys(list).length==0" >暂无帖子</view> 
			<view v-for="(item,index) in  list" :key="index" @click="goDetail(item.id)" class="sglist-item">
				<image v-if="item.imgurl!=''" mode="widthFix" :src="item.imgurl+'.middle.jpg'" class="sglist-img"></image>
				<view class="sglist-title">{{item.title}}</view> 	
				<view class="sglist-desc">{{item.description}}</view>
				<view class="sglist-imglist">
					 
					<image mode="widthFix" v-for="(img,imgIndex) in item.imgslist" :key="imgIndex" :src="img+'.100x100.jpg'" class="sglist-imglist-img" />
					
				</view>
				<view class="sglist-ft">
					<view class="sglist-ft-love">{{item.love_num}}</view>
					<view class="sglist-ft-cm">{{item.comment_num}}</view>
					<view class="sglist-ft-view">{{item.view_num}}</view>
				</view> 
			</view>
			<view @click="getList" v-if="per_page>0" class="loadMore">点我加载更多...</view>
		</view>
		 
		<mt-footer tab="home"></mt-footer>
	</view>
</template>

<script>
import mtFooter from "../../components/footer.vue"; 
var winWidth=750; 
export default({
	components:{
		mtFooter
	},
	data:function(){
		return {
			 
			flashList:[],
			navList:[],
			adList:[],
			pageLoad:false,
			list:[],
			rscount:0,
			page:"blog",
			type:"",
			isFirst:true,
			per_page:0,
			tabClass:'',
			swiperHeight:100
		}
	},
	onLoad:function(){
		var stype=uni.getStorageSync("sblog-index-type");
		if(stype){
			this.type=stype;
		}
		var spage=uni.getStorageSync("sblog-index-page");
		if(spage){
			this.page=spage;
		}
		this.getPage();
		this.getList();
		const res = uni.getSystemInfoSync();
		winWidth=res.windowWidth;
		this.swiperHeight=Math.min(640,res.windowWidth)/2;
	},
	onPageScroll:function(e){
		if(e.scrollTop>this.swiperHeight){
			// #ifdef H5
			this.tabClass="position: fixed;left:0;right:0;top:44px;";
			// #endif
			//#ifndef H5
			this.tabClass="position: fixed;left:0;right:0;top:0px;";
			// #endif
			
		}else{
			this.tabClass="";
		}
		
	},
	onReachBottom:function(){
		this.getList();
	},
	onPullDownRefresh:function(){
		this.getPage();
	},
	onShareAppMessage:function(){
		
	},
	methods:{
		gourl:function(url){
			uni.navigateTo({
				url:url
			})
		},
		
		goDetail:function(id){
			uni.navigateTo({
				url:"../../pages/article/show?id="+id
			})
		},
		 
		getPage:function(){
			var that=this;
			this.app.get({
				url:that.app.apiHost+"/index/index",
				data:{
					type:that.type
				},
				dataType:"json",
				success:function(res){
					that.pageLoad=true;
					that.flashList=res.data.flashList;
					that.navList=res.data.navList;
					that.adList=res.data.adList;
					that.list=res.data.articleList;
					uni.stopPullDownRefresh();
				}
			})
		},
		getList:function(){
			var that=this;
			if(!that.isFirst && that.per_page==0) return false;
			this.app.get({
				url:that.app.apiHost+"/article/list?ajax=1",
				data:{
					type:that.type,
					per_page:that.per_page
				},
				dataType:"json",
				success:function(res){
					
					that.per_page=res.data.per_page;
					
					if(that.isFirst){
						that.isFirst=false;
						that.list=res.data.list;
					}else{
						 
						for(var i in res.data.list){
							that.list.push(res.data.list[i]);
						}
						 
					}
					
				}
			})
		}
	}
})
</script>

<style>
	 
	.mtt10 {
		margin-top: -10px;
	}
	
	.adBox {
		display: flex;
		flex-direction: row;
	
	}
	
	.adBox-item {
		flex: 1;
		margin: 5px;
	}
	
	.adBox-img {
		width: 100%;
		border-radius: 10px;
	}
	
	.m-navPic-img {
		border-radius: 50%;
	}
	
</style>

<template>
	<view>
		<div v-if="!list || list.length==0 " class="emptyData">暂无课程</div>
		<div v-for="(item,index) in list" :key="index"  class="sglist-item ">
			 
			<div class="flex flex-ai-center">
				<img class="flexlist-img" :src="item.imgurl+'.100x100.jpg'" />
				<div class="flex-1">
					<div class="flexlist-title">{{item.title}}</div>
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
					<div @click="goDetail(item.lessonid)" class="btn-small btn-outline-primary" >详情</div> 
				</div>
				
			</div>
			
		</div>
		<tutor-footer tab="lesson"></tutor-footer>
	</view>
</template>

<script>
	import tutorFooter from "../tutor-footer.vue";
	export default {
		components: {
			tutorFooter
		},
		data:function(){
			return {
				pageLoad:false,
				list:[],
				per_page:0,
				isFirst:true,
				catit:0
			}
		},
		onLoad:function(){
			this.getPage();
		},
		onReachBottom:function(){
			this.getList();
		},
		onPullDownRefresh:function(){
			this.getPage();
			uni.stopPullDownRefresh();
		},
		onShareAppMessage:function(){
			
		},
		onShareTimeline:function(){
			
		},
		methods: {
			gourl:function(url){
				uni.navigateTo({
					url:url
				})
			},
			goDetail:function(lessonid){
				uni.navigateTo({
					url:"show?lessonid="+lessonid
				})
			},
			getPage:function() {
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=tutor_lesson&a=list",
					data:{
						catid:this.catid
					},
					success:function(res){
						that.pageLoad=true;
						that.list=res.data.list;
						that.per_page=res.data.per_page;
					}
				})
			},
			getList:function() {
				var that=this;
				if(that.per_page==0 && !that.isFirst){
					return false;
				}
				that.app.get({
					url:that.app.apiHost+"/module.php?m=tutor_lesson&a=list",
					data:{
						per_page:that.per_page,
						catid:this.catid
					},
					success:function(res){						 
						that.per_page=res.data.per_page;
						if(that.isFirst){
							that.list=res.data.list;
							that.isFirst=false;
						}else{
							for(var i in res.data.list){
								that.list.push(res.data.list[i]);
							}							
						}
						
					}
				})
			}
		},
	}
</script>

<style>
</style>

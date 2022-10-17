<template>
	<view>
		<div v-if="item.kefu" @click="goDetail(item)" v-for="(item,index) in list" :key="index"  class="flexlist-item pointer">
			<img class="flexlist-img" :src="item.kefu.user_head+'.100x100.jpg'" />
			<div class="flex-1">
				<div class="flex mgb-5">
					<div class="flex-1 f16 cl2">{{item.kefu.title}}</div>
					<div class="cl3 f12">{{item.timeago}}</div>
				</div>
				<div class="flexlist-desc">{{item.content}}</div>
			</div>
		</div>
	</view>
</template>

<script>
	
	export default{
		data:function(){
			return {
				pageLoad:false,
				list:[],
				per_page:0,
				isFirst:true
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
			goDetail:function(item){
				uni.navigateTo({
					url:"../kefu_msg/index?kfid="+item.kfid
				})
			},
			getPage:function() {
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=kefu_msg_index",
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
					url:that.app.apiHost+"/module.php?m=kefu_msg_index",
					data:{
						per_page:that.per_page
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

<template>
	<view>
		<div class="tabs-border i5">
			<div @click="setType('all')" :class="type=='all'?'tabs-border-active':''" class="tabs-border-item ">全部
			</div>
			<div @click="setType('new')" :class="type=='new'?'tabs-border-active':''"
				class="tabs-border-item">新订单</div>
			<div @click="setType('confirm')" :class="type=='confirm'?'tabs-border-active':''"
				class="tabs-border-item">已确认</div>
			<div @click="setType('send')" :class="type=='send'?'tabs-border-active':''" class="tabs-border-item">配送中
			</div>
			<div @click="setType('finish')" :class="type=='finish'?'tabs-border-active':''"
				class="tabs-border-item">已完成</div>
			<div @click="setType('unraty')" :class="type=='unraty'?'tabs-border-active':''"
				class="tabs-border-item">待评价</div>
		</div>
		<div class="sglist">
			 
			<div v-for="(item,index) in list" :key="index" class="sglist-item js-item">
				<div class="flex bd-mp-10">
					<div class="cl-status mgr-5">{{item.status_name}}</div>
					<div class="cl3 f12">{{item.createtime}}</div>
					<div class="flex-1"></div>
					<div class="cl-money">{{item.money}}</div>
					
				</div>
				<div class="flex bd-mp-10 flex-ai-center">
					<img class="wh-40 bd-radius-50 mgr-5" :src="item.lesson.imgurl+'.100x100.jpg'" />
					<div class="flex-1">
						<div class="flex">
							<div>
								<div @click="goLession(item.lessonid)" class="cl-primary mgb-5 f14">课程：{{item.lesson.title}}</div>
								<div class="cl2">共{{item.lesson.lesson_num}}节</div>
							</div>
							
							<div class="flex-1"></div>
							<div @click="goShop(item.shopid)" class="cl-primary f14">教师：{{item.shop.title}}</div>
						</div>
						
					</div>
				</div>
				<div class="flex mgb-5">
					<div class="cl2 mgr-5">{{item.nickname}}</div>
					<div class="cl2 mgr-5">{{item.telephone}}</div>
				</div>
				<div class="cl2 f12 mgb-5">{{item.address}}</div>
				 
				<div  class="flex">
					<div class="flex-1"></div>
					<div @click="gourl('show?orderid='+item.orderid)" class="btn-small mgr-10">查看</div>
					
					<div v-if="item.status==0" @click="cancel(item)" class="btn-small btn-danger">取消订单</div>
				</div>
				 
			</div>
			<div @click="getList()" v-if="per_page>0" class="loadMore">加载更多</div> 
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
				isFirst:true,
				type:"all"
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
			setType:function(type){
				this.type=type;
				this.isFirst=true;
				this.per_page=0;
				this.getList();
			},
			gourl:function(url){
				uni.navigateTo({
					url:url
				})
			},
			goLession:function(lessonid){
				uni.navigateTo({
					url:"../tutor_lesson/show?lessonid="+lessonid
				})
				 
			},
			goShop:function(shopid){
				uni.navigateTo({
					url:"../tutor_shop/index?shopid="+shopid
				})
			},
			getPage:function() {
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=tutor_order&a=my",
					data:{
						type:this.type
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
					url:that.app.apiHost+"/module.php?m=tutor_order&a=my",
					data:{
						per_page:that.per_page,
						type:this.type
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
			},
			cancel:function(item){
				var that=this;
				uni.showModal({
					title:"确认提示",
					content:"确认取消回收吗？",
					success:function(e){
						if(!e.confirm) return false;
						that.app.get({
							url:that.app.apiHost+"/module.php?m=tutor_order&a=cancel&ajax=1",
							data:{
								orderid:item.orderid
							},
							dataType:"json",
							success:function(res){
								uni.showToast({
									title:res.message,
									icon:"none"
								})
								 
								if(!res.error){
									item.status=4;
									item.status_name="已取消"
								}
							}
						})
					}
				})
			}
		},
	}
</script>

<style>
</style>

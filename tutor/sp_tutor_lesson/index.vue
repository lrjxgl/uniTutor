<template>
	<view>
		<div class="tabs-border">
			<div @click="setTab('all')" :class="tab=='all'?'tabs-border-active':''" class="tabs-border-item">全部</div>
			<div @click="setTab('new')"  :class="tab=='new'?'tabs-border-active':''"  class="tabs-border-item">待审核</div>
			<div @click="setTab('pass')"  :class="tab=='pass'?'tabs-border-active':''"  class="tabs-border-item">已上架</div>
			<div @click="setTab('forbid')"  :class="tab=='forbid'?'tabs-border-active':''"  class="tabs-border-item">已下架</div>
		</div>
		<div v-for="(item,index) in list" :key="index" class="flexlist-item">
			<img class="flexlist-img" :src="item.imgurl+'.100x100.jpg'" />
			<div class="flex-1">
				<div class="flexlist-title">{{item.title}}</div>
				<div class="flex mgb-10">
					<div class="cl2 mgr-5">库存</div>
					<div class="cl-money">{{item.total_num}}份</div>
					<div class="flex-1"></div>
					<div class="cl2 mgr-5">已售</div>
					<div class="cl-num">{{item.sold_num}}份</div>
				</div>
				<div class="flexlist-desc">{{item.description}}</div>
				<div class="flex">
					<div class="flex-1"></div>
					<div v-if="item.status==1" class="btn-small mgr-5 btn-outline-primary">下架</div>
					<div v-if="item.status==2 && item.ispass==1 " class="btn-small  mgr-5 btn-outline-primary">上架</div>
					<div @click="goAdd(item.lessonid)"  class="btn-small btn-outline-primary">编辑</div>
				</div>
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
				isFirst:true,
				catid:0,
				tab:"all"
			}
		},
		onLoad:function(){
			 
			this.getPage();
		},
		methods:{
			setTab:function(t){
				this.tab=t;
				this.isFirst=true;
				this.per_page=0;
				this.getList();
			},
			getPage:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/moduleshop.php?m=tutor_lesson&ajax=1",
					data:{
						catid:this.catid
					},
					dataType:"json",
					success:function(res){
						if(res.error){
							skyToast(res.message);
							return false;
						}
						that.list=res.data.list;
						that.isFirst=false;
						that.per_page=res.data.per_page;
						that.pageLoad=true;
					}
				})
			},
			getList:function(){
				var that=this;
				if(that.per_page==0 && !that.isFirst){
					return false;
				}
				that.app.get({
					url:that.app.apiHost+"/moduleshop.php?m=tutor_lesson&ajax=1",
					data:{
						catid:this.catid,
						per_page:that.per_page
					},
					dataType:"json",
					success:function(res){
						if(res.error){
							skyToast(res.message);
							return false;
						}
						if(that.isFirst){
							that.list=res.data.list;
							that.isFirst=false;
						}else{
							for(var i in res.data.list){
								that.list.push(res.data.list[i]);
							}
						}
						
						
						that.per_page=res.data.per_page;
						that.pageLoad=true;
					}
				})
			},
			goAdd:function(lessonid){
				uni.navigateTo({
					url:"add?lessonid="+lessonid
				})
				 
			}
		}
	}
	
</script>

<style>
</style>

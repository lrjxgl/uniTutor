<template>
	<view>
		<div class="flex">
			<div class="side">
				<div @click="setCat(0)" :class="catid==0?'side-menu-active':''" class="side-menu">全部</div>
				<div  @click="setCat(item.catid)" :class="catid==item.catid?'side-menu-active':''"  class="side-menu" v-for="(item,index) in catList" :key="index">
					{{item.title}}
				</div>
			</div>
			<div class="main">
				<div class="tabs-border">
					<div @click="setTab('')" :class="orderby==''?'tabs-border-active':''" class="tabs-border-item">综合</div>
					<div @click="setTab('raty_grade')" :class="orderby=='raty_grade'?'tabs-border-active':''"  class="tabs-border-item">评价</div>
					<div @click="setTab('sold_num')" :class="orderby=='sold_num'?'tabs-border-active':''"  class="tabs-border-item">销量</div>
				</div>
				<div class="flexlist">
					 
					<div v-for="(item,index) in list" :key="index" class="flexlist-item">
						<img @click="goShop(item.shopid)"  :src="item.imgurl+'.100x100.jpg'" class="flexlist-img" />
						<div class="flex-1">
							<div  @click="goShop(item.shopid)"  class="flexlist-title">{{item.title}}</div>
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
			</div>
		</div>
		<tutor-footer tab="shoplist"></tutor-footer>
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
				catid:0,
				catList:[],
				orderby:""
			}
		},
		onLoad:function(){
			 
			this.getPage();
		},
		methods:{
			setTab:function(t){
				this.orderby=t;
				this.per_page=0;
				this.isFirst=true;
				this.getList();
			},
			setCat:function(catid){
				this.catid=catid;
				this.per_page=0;
				this.isFirst=true;
				this.getList();
			},
			getPage:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=tutor_shoplist&a=list&ajax=1",
					data:{
						catid:this.catid,
						orderby:this.orderby
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
						that.catList=res.data.catList;
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
					url:that.app.apiHost+"/module.php?m=tutor_shoplist&a=list&ajax=1",
					data:{
						catid:this.catid,
						per_page:that.per_page,
						orderby:this.orderby
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
			goShop:function(shopid){
				uni.navigateTo({
					url:"../tutor_shop/index?shopid="+shopid
				})
				 
			}
		}
	}
	
</script>

<style>
	.side{
		position: absolute;
		bottom: 50px;
		top:0px;
		left: 0px;
		width: 80px;
		background-color: #fff;
		padding: 0px;
		box-sizing: border-box;
	}
	.side-menu{
		padding: 6px 8px 6px 10px;
		cursor: pointer;
	}
	.side-menu-active{
		color: #F60;
	}
	.main{
		flex: 1;
		margin-left: 82px;
	}
</style>

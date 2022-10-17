<template>
	<view>
		<div @click="go2Index()" class="header-back-fixed" style="z-index: 999; color: #fff;"></div>
		<div class="main-body none" :class="'flex-col'" v-if="pageLoad" id="App">
			
			<div class="uBox">
				<div @click="toggleFollow()" v-if="isFollow" class="fixFollow fixFollow-active">已关注</div>
				<div @click="toggleFollow()" v-else class="fixFollow fixFollow-active">+关注</div>
				<div class="flex flex-col flex-center">
					<img class="uBox-head" :src="shop.imgurl+'.100x100.jpg'" />
					<div class="uBox-nick">{{shop.title}}</div>
					<div class="flex mgb-5 flex-ai-center mgr-10">
						 
						<div class="cl-white  mgr-5">月销</div>
						<div class="cl-white  mgr-10">{{shop.order_num}}笔</div>
						 
						<div class="cl-white  mgr-5">评价</div>
						<div class="cl-white  mgr-10">{{shop.raty_grade}}分</div>
						<div class="cl-white   mgr-5">粉丝</div>
						<div class="cl-white  ">{{shop.follow_num}}</div>
					</div>
				</div>
				<div class="uBox-desc">{{shop.description}}</div>	
				
				
			</div>
			 
			<div class="tabs-border ">
				<div @click="setTab('all')" :class="tab=='all'?'tabs-border-active':''" class="tabs-border-item">介绍</div>
				<div @click="setTab('lesson')" :class="tab=='lesson'?'tabs-border-active':''"  class="tabs-border-item">课程</div>
				<div @click="setTab('raty')" :class="tab=='raty'?'tabs-border-active':''"  class="tabs-border-item">评价</div>
				 
			</div> 
			<div v-if="tab=='all'">
				<div class="row-box mgb-5">
					<div class="flex">
						<div class="flex-col bd pd-10 bd-radius-10 flex-center">
							
							<div class="cl-money mgb-5">{{shop.new_order}}人</div>
							<div class="cl3">新学员</div>
						</div>
						<div class="flex-1"></div>
						<div class="flex-col bd pd-10 bd-radius-10 flex-center">
							
							<div class="cl-money mgb-5">{{shop.order_num}}人</div>
							<div class="cl3">总学员</div>
						</div>
						<div class="flex-1"></div>
						<div class="flex-col bd pd-10 bd-radius-10 flex-center">
							
							<div class="cl-money mgb-5">{{shop.raty_grade}}分</div>
							<div class="cl3">评价</div>
						</div>
						<div class="flex-1"></div>
						<div class="flex-col bd pd-10 bd-radius-10 flex-center">
							
							<div class="cl-money mgb-5">￥{{shop.fee}}/时</div>
							<div class="cl3">收费</div>
						</div>
					</div>
					
				</div>
				<div class="row-box mgb-5">
					<div class="fw-600 mgb-10">证书认证</div>
					<div>
						
						<div @click="goCert(item)" v-for="(item,index) in certList" :key="index" class="row-item ">
							<div  class="row-item-title">{{item.title}}</div>
						</div>
					</div>
				</div>
				<div class="row-box mgb-5">
					<div class="fw-600 mgb-10">老师介绍</div>
					<rich-text class="d-content" :nodes="shop.content"></rich-text>
				</div>
			</div>
			<div v-if="tab=='lesson' || tab=='all'" class="row-box mgb-5">
				<div class="fw-600 mgb-10">课程服务</div> 
				<div v-if="!list || Object.keys(list).length==0 " class="emptyData">暂无课程</div>
				<div v-for="(item,index) in list" :key="index"  class="flexlist-item flex-ai-center">
					<img class="flexlist-img" :src="item.imgurl+'.100x100.jpg'" />
					<div class="flex-1">
						<div class="flexlist-title">{{item.title}}</div>
						<div class="flexlist-desc">{{item.description}}</div>
					</div>
					<div @click="goDetail(item.lessonid)" class="btn-small btn-outline-primary" >查看详情</div> 
				</div>
			</div>
			<div v-if="tab=='all'|| tab=='raty'" >
				<div class="emptyData" v-if="!ratyList || ratyList.length==0">暂无评价</div>
				<div  v-else>
					<div class="row-box mgb-5" v-for="(item,index) in ratyList" :key="index">
						<div class="flex  bd-mp-5">
							<div class="mgr-5">{{item.nickname}}</div>
							
							<sky-raty class="flex-1" label="" grade="10" len="10"></sky-raty>
							<div class="cl3 f12">{{item.time}}</div>
						</div>
						<div class="cl2">{{item.content}}</div>
					</div>
				</div>
			</div>
			<div class="footer-row"></div>
			<div class="footerFix ">
				<div class="row-box flex-ai-center flex">
					<div class="mgr-10">{{shop.title}}</div>
					<div class="cl-num">{{shop.raty_grade}}分</div>
					<div class="flex-1"></div>
					<div @click="gourl('../../kefu/kefu_msg/index?tablename=tutor&objectid='+shop.shopid)"  class="btn-small btn-outline-primary">咨询一下</div>
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
				shopid:0,
				list:[],
				certList:[],
				ratyList:[],
				shop:{},
				isFollow:0,
				per_page:0,
				isFirst:false,
				tab:"all"
			}
		},
		onLoad:function(ops){
			this.shopid=ops.shopid;
			this.getPage();
			this.getRatyList();
		},
		methods:{
			go2Index:function(){
				var pages=getCurrentPages();
				console.log(pages.length);
				if(pages.length==1){
					uni.reLaunch({
						url:"../tutor/index"
					})
				}else{
					uni.navigateBack();
				}
				 
				
			},
			gourl:function(url){
				uni.navigateTo({
					url:url
				})
			},
			goCert:function(item){
				 
			},
			setTab:function(t){
				this.tab=t;
			} ,
			goDetail:function(lessonid){
				uni.navigateTo({
					url:"../tutor_lesson/show?lessonid="+lessonid
				})
				 
			},
			 
			getPage:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=tutor_shop&ajax=1",
					data:{
						shopid:this.shopid
					},
					dataType:"json",
					success:function(res){
						that.isFollow=res.data.isFollow;
						that.per_page=res.data.per_page;
						that.isFirst=false;
						that.list=res.data.list;
						if(!res.data.shop.content){
							res.data.shop.content=res.data.shop.description;
						}
						that.shop=res.data.shop;
						if(res.data.certList){
							that.certList=res.data.certList;
						}
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
					url:that.app.apiHost+"/module.php?m=tutor_shop&ajax=1",
					data:{
						shopid:this.shopid,
						per_page:that.per_page
					},
					dataType:"json",
					success:function(res){
						if(res.error){
							return false;
						}
						that.per_page=res.data.per_page;
						 
						if(that.isFirst){
							that.list=res.data.list;
						}else{
							for(var i in res.data.list){
								that.list.push(res.data.list[i])
							}
						}
					}
				})
			},
			toggleFollow:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=tutor_shop&a=togglefollow&ajax=1",
					data:{
						shopid:this.shopid
					},
					dataType:"json",
					success:function(res){
						if(res.error){
							return false;
						}
						that.isFollow=res.data.isFollow;
					}
				})
			},
			getRatyList:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=tutor_shop&a=raty&ajax=1",
					data:{
						shopid:this.shopid
					},
					dataType:"json",
					success:function(res){
						if(res.error){
							return false;
						}
						that.ratyList=res.data.list;
					}
				})
			}
	  
		}
	}
</script>

<style>
	.uBox {
		position: relative;
		width: 100%;
		height: 200px;
		background-color: #2b8b97;
		padding: 10px;
		color: #fff;
	}
	
	.uBox-nick {
		margin-bottom: 10px;
		color: #fff;
	}
	
	.uBox-head {
	
		width: 50px;
		height: 50px;
		border-radius: 10px;
		margin-bottom: 10px;
	}
	
	.uBox-desc {
		text-align: center;
		padding-top: 20px;
		padding-bottom: 20px;
		padding-right: 10px;
		color: #fff;
		font-size: 14px;
	}
	
	.fixFollow {
		position: absolute;
		right: 10px;
		top: 10px;
		color: #eee;
		border: 1px solid #eee;
		border-radius: 5px;
		padding: 5px 10px;
		font-size: 12px;
		cursor: pointer;
	}
	
	.fixFollow-active {
		color: #fff;
		border: 1px solid #fff;
	}
	
</style>

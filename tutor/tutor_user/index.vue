<template>
	<view>
		<div class="uhead">
			<image @click="gourl('../../pages/user/user_head')"  class="uhead-img" :src="user.user_head+'.100x100.jpg'"></image>
			<div class="flex-1">
				<div class="uhead-nick">{{user.nickname}}</div>
				<div class="flex mgb-5">
					<div class="mgr-5">余额</div>
					<div  class="cl-money">￥{{user.money}}</div>
					<div class="flex-1"></div>
					 
				</div>
				 
			</div>
			<div  @click="gourl('../../pages/user/set')"   class="iconfont icon-settings cl-u"></div>
		</div>
		 
		<div v-if="shop && Object.keys(shop).length>0">
			<div class="fw-600 pd-10">老师面板</div>
			<div class="row-box mgb-5">
				
				<div @click="gourl('../sp_tutor_order/index')" class="row-item">
					<div class="row-item-icon icon-order  cl-u"></div>
					<div class="row-item-title">课程订单</div>
				</div>
				<div @click="gourl('../sp_tutor_lesson/index')"  class="row-item">
					<div class="row-item-icon icon-book  cl-u"></div>
					<div class="row-item-title">课程列表</div>
				</div>
				<div @click="gourl('../sp_tutor_lesson/add')"  class="row-item">
					<div class="row-item-icon icon-add  cl-u"></div>
					<div class="row-item-title">发布课程</div>
				</div>
				
				<div gourl="/moduleshop.php?m=tutor_guest&a=msg" class="row-item">
					<div class="row-item-icon icon-service_light cl-u"></div>
					<div class="row-item-title">客户咨询</div>
				</div>
				<div gourl="/moduleshop.php?m=tutor_guest" class="row-item">
					<div class="row-item-icon icon-group  cl-u"></div>
					<div class="row-item-title">客服管理</div>
				</div> 
				<div gourl="/moduleshop.php?m=tutor_shop" class="row-item">
					<div class="row-item-icon icon-shop  cl-u"></div>
					<div class="row-item-title">教师信息</div>
				</div>
			</div>
			<div class="fw-600 pd-10">用户面板</div>	
		</div>
		 
		<div class="row-box mgb-5">
			 
			<div  @click="gourl('../tutor_order/my')"   class="row-item">
				<div class="row-item-icon icon-order  cl-u"></div>
				<div class="row-item-title">课程订单</div>
			</div>
			 
			<div  v-if="!shop || Object.keys(shop).length==0" @click="gourl('../tutor_shop_apply/index')"  class="row-item">
				<div class="row-item-icon icon-notice  cl-u"></div>
				<div class="row-item-title">老师申请入驻</div>
			</div>
			 
			<div  @click="gourl('../../pages/notice/my')"  class="row-item">
				<div class="row-item-icon icon-notice  cl-u"></div>
				<div class="row-item-title">消息通知</div>
			</div> 
			 
		</div>
		
		 
			
		<div class="row-box mgb-5">	
			
			<div @click="gourl('../../pages/pay_log/my')" class="row-item">
				<div class="row-item-icon icon-vipcard  cl-u"></div>
				<div class="row-item-title">消费记录</div>
			</div>
			<div  @click="gourl('../../pages/kefu/index')"  class="row-item">
				<div class="row-item-icon icon-service_light  cl-u"></div>
				<div class="row-item-title">平台客服</div>
			</div>
			
		</div>
			<tutor-footer tab="user"></tutor-footer>
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
				user:{},
				shop:{}
			}
		},
		onLoad:function(){
			this.getPage();
		},
		 
		onShareAppMessage:function(){
			
		},
		methods: {
			gourl:function(url){
				uni.navigateTo({
					url:url
				})
			},
			getPage:function() {
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=tutor_user",
					success:function(res){
						
						that.user=res.data.user;
						if(res.data.shop){
							
							that.shop=res.data.shop;
							
						}
						
						that.pageLoad=true;
					}
				})
			}
		},
	}
</script>

<style>
	
</style>

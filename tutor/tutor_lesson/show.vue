<template>
	<view>
		<div class="flexlist-item mgb-5">
				<image  :src="shop.imgurl+'.100x100.jpg'" class="flexlist-img bd-radius-50"></image>
				<div class="flex-1">
					<div class="flexlist-title">{{shop.title}}</div>
					<div class="flex mgb-5">
						
						<div class="cl2 f12 mgr-5">新订单</div>
						<div class="cl-num f12 mgr-10">{{shop.new_order}}</div>
						<div class="cl2 f12 mgr-5">总订单</div>
						<div class="cl-num f12 mgr-10">{{shop.order_num}}</div>
						<div class="cl2 f12 mgr-5">评价</div>
						<div class="cl-num f12 mgr-10">{{shop.raty_grade}}</div>
					</div>
					<div class="flexlist-desc">{{shop.description}}</div>
				</div>
			</div>
			<div class="row-box mgb-5">
				<div class="d-title mgb-10">{{lesson.title}}</div>
				<div class="flex mgb-10">
					<div class="cl2 mgr-5">价格</div>
					<div class="cl-money">￥{{lesson.money}}</div>
					<div class="flex-1"></div>
					<div class="cl2 mgr-5">课程数</div>
					<div class="cl-num">{{lesson.lesson_num}}节</div>
				</div>
				<div class="flex mgb-10">
					<div class="cl2 mgr-5">库存</div>
					<div class="cl-money">{{lesson.total_num}}份</div>
					<div class="flex-1"></div>
					<div class="cl2 mgr-5">已售</div>
					<div class="cl-num">{{lesson.sold_num}}份</div>
				</div>
				
			</div>
			<div class="row-box">
				<rich-text :nodes="lesson.content"  class="d-content"></rich-text>
			</div>
		</div>
		<div class="footer-row"></div>
		<div class="footerFix pd-10 bg-white">
			<div class="flex flex-ai-center">
				<div class="cl-money">￥{{lesson.money}}</div>
				<div class="flex-1"></div>
				<div @click="modalOrder=true" class="btn-small btn-outline-primary">立即下单</div>
			</div>
		</div>
		<div v-if="modalOrder">
			<div @click="modalOrder=false" class="modal-mask"></div>
			<div class="modal">
				<div class="modal-header">
					<div class="modal-title">我要下单</div>
					<div  @click="modalOrder=false" class="modal-close icon-close"></div>
				</div>
				<form @submit="orderSubmit" class="modal-body">
					<input class="none" type="text" name="lessonid"  v-model="lesson.lessonid" />
					<div class="input-flex">
						<div class="input-flex-label">联系人</div>
						<input class="input-flex-text" type="text" name="nickname" v-model="addr.nickname" />
					</div>
					<div class="input-flex">
						<div class="input-flex-label">电话</div>
						<input class="input-flex-text"  type="text" name="telephone" v-model="addr.telephone" />
					</div>
					<div class="input-flex">
						<div class="input-flex-label">地址</div>
						<input class="input-flex-text"  type="text" name="address" v-model="addr.address" />
					</div>
					<button form-type="submit" class="btn-row-submit">确认下单</button>
				</form>
			</div>
		</div>	
	</view>
</template>

<script>
	import dtPay from "../../common/dtpay.js";
	export default{
		data:function(){
			return {
				pageLoad:false,
				lessonid:0,
				lesson:{},
				shop:{},
				addr:{},
				modalOrder:false,
			}
		},
		onLoad:function(ops){
			this.lessonid=ops.lessonid;
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
			goShop:function(shopid){
				uni.navigateTo({
					url:"../tutor_shop/index?shopid="+shopid
				})
			},
			getPage:function() {
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=tutor_lesson&a=show",
					data:{
						lessonid:this.lessonid
					},
					success:function(res){
						
						that.lesson=res.data.lesson;
						that.shop=res.data.shop;
						that.addr=res.data.addr;
						that.pageLoad=true;
					}
				})
			},
			orderSubmit:function(e){
				var that=this;
				if(!that.app.canPost()){
					return false;
				}
				that.app.post({
					url:that.app.apiHost+"/module.php?m=tutor_order&a=save&ajax=1",
					 
					data:e.detail.value,
					success:function(res){
						uni.showToast({
							title:res.message,
							icon:"none"
						})
						if(res.error){
							return false;
						}
						if(res.data.action=='pay'){
							dtPay.urlSuccess="../../tutor/tutor_lesson/success";
							dtPay.urlFail="../../tutor/tutor_lesson/fail";
							dtPay.pay(res.data);
							window.location=res.data.payurl;
						}
						$('#modalOrder').hide()
					}
				})
			}
		},
	}
</script>

<style>
</style>

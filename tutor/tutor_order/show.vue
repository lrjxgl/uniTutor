<template>
	<view>
		<div class="row-box mgb-10">
			<div class="flex bd-mp-10">
				<div class="cl-status mgr-5">{{item.status_name}}</div>
				<div class="cl3 f12">{{item.createtime}}</div>
				<div class="flex-1"></div>
				<div class="cl-money">{{item.money}}</div>
				
			</div>
			<div class="flex bd-mp-10 flex-ai-center">
				<img class="wh-40 bd-radius-50 mgr-10" :src="lesson.imgurl+'.100x100.jpg'" />
				<div class="flex-1">
					<div class="flex">
						<div>
							<div @click="goLession(item.lessonid)" class="cl-primary mgb-5 f14">课程：{{lesson.title}}</div>
							<div class="cl2">共{{lesson.lesson_num}}节</div>
						</div>
						
						<div class="flex-1"></div>
						<div @click="goShop(item.shopid)" class="cl-primary f14">教师：{{shop.title}}</div>
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
				 
				<div v-if="item.status==0" @click="cancel(item)" class="btn-small btn-danger">取消订单</div>
			</div>
		</div>
		<div v-if="item.status==3 && item.israty==0" class="row-box mgb-5">
			<sky-raty @call-parent="setRatyGrade" fields="raty_grade" label="评价" grade="8" len="10" ></sky-raty>
			<textarea v-model="raty_content" class="textarea-flex-text h60"></textarea>
			<div class="flex">
				<div class="flex-1"></div>
				<div @click="ratySubmit()" class="btn">确认评分</div>
			</div>
		</div>
		<div class="row-box mgb-5" v-if="item.israty">
			<sky-raty readyonly="true" fields="raty_grade" label="评价" :grade="item.raty_grade" len="10" ></sky-raty>
			<div  class="textarea-flex-text h60" v-html="raty_content"></div>
		</div>
		<div v-if="logList && logList.length>0" class="row-box">
			<div class="row-box-hd mgb-10">订单日志</div>
			 
			<div v-for="(it,ii) in logList" :key="ii" class="flex bd-mp-5">
				<div class="cl3 f12 mgr-10">{{it.createtime}}</div>
				<div class="cl2 flex-1">
					{{it.content}}
				</div>
				
			</div>
			 
		</div>
	</view>
</template>

<script>
	import skyRaty from "../../components/skyraty.vue"
	export default{
		components:{
			skyRaty
		},
	 	data:function(){
			return {
				item:{},
				logList:[],
				raty_content:"",
				raty_grade:8,
				shop:{},
				lesson:{}
			}
		},
	 	onLoad:function(ops){
	 		this.orderid=ops.orderid;
			this.getPage();
	 	},
	 	methods:{
	 		setRatyGrade:function(e){
	 			this.raty_grade=e;
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
	 		getPage:function(){
	 			var that=this;
	 			that.app.get({
	 				url:that.app.apiHost+ "/module.php?m=tutor_order&a=show&ajax=1",
	 				data:{
	 					orderid:this.orderid
	 				},
	 				dataType:"json",
	 				success:function(res){
	 					that.item=res.data.data;
	 					that.logList=res.data.logList;
	 					that.lesson=res.data.lesson;
	 					that.shop=res.data.shop;
	 					if(res.data.raty){
	 						that.raty_grade=res.data.raty.raty_grade;
	 						that.raty_content=res.data.raty.raty_content;
	 					}
	 				}
	 			})
	 		},
	 		
	 		cancel:function(item){
	 			var that=this;
	 			skyJs.confirm({
	 				title:"确认提示",
	 				content:"确认取消回收吗？",
	 				success:function(){
	 					$.ajax({
	 						url:"/module.php?m=tutor_order&a=cancel&ajax=1",
	 						data:{
	 							id:item.orderid
	 						},
	 						dataType:"json",
	 						success:function(res){
	 							
	 							skyJs.toast(res.message);
	 							if(!res.error){
	 								that.getPage();
	 							}
	 						}
	 					})
	 				}
	 			})
	 		},
	 		ratySubmit:function(){
	 			var that=this;
	 			skyJs.confirm({
	 				title:"确认提示",
	 				content:"确认评价吗？",
	 				success:function(){
	 					$.ajax({
	 						url:"/module.php?m=tutor_order_raty&a=save&ajax=1",
	 						data:{
	 							orderid:that.item.orderid,
	 							raty_grade:that.raty_grade,
	 							raty_content:that.raty_content
	 						},
	 						type:"POST",
	 						dataType:"json",
	 						success:function(res){
	 							
	 							skyJs.toast(res.message);
	 							if(!res.error){
	 								that.getPage();
	 							}
	 						}
	 					})
	 				}
	 			})
	 		}
	 	}
	 }
</script>

<style>
</style>

<template>
	<view >
		<div v-if="errMsg!=''" class="emptyData ">{{errMsg}}</div>
		<form v-else @submit="submit" >
			<div class="input-flex ">
				<div class="input-flex-label input-flex-require">老师名称</div>
				<input type="text" name="title" class="input-flex-text" />
			</div>
			
			<div class="input-flex ">
				<div class="input-flex-label input-flex-require">联系人</div>
				<input type="text" name="nickname" class="input-flex-text" />
			</div>
			<div class="input-flex">
				<div class="input-flex-label input-flex-require">手机</div>
				<input type="text" v-model="telephone" name="telephone" class="input-flex-text" />
			</div>
			<div class="input-flex">					
				<div class="input-flex-label">验证码</div>					 
				<input type="text" name="yzm" class="input-flex-text">				 
				<div class="input-flex-btn" @click="sendSms">获取验证码</div>
			</div>
			<div class="input-flex ">
				<div class="input-flex-label input-flex-require">身份证号码</div>
				<input type="text" name="userno" class="input-flex-text" />
			</div> 
			<div class="input-flex flex-ai-center">
				<div class="input-flex-label input-flex-require">身份证照片</div>
				<div class="flex-1">
					<input v-model="usercard" type="text" name="usercard" class="none" />
					<sky-upimg @call-parent="setImgurl" imgurl="" trueimgurl=""></sky-upimg> 
				</div>
			</div> 
			<div class="input-flex">
				<div class="input-flex-label input-flex-require">所在地址</div>
				<input type="text" name="address" class="input-flex-text" />
			</div>
			<div class="textarea-flex">
				<div class="textarea-flex-label">自我介绍</div>
				<textarea class="textarea-flex-text" name="description"></textarea>
			</div>
			
			<button form-type="submit" class="btn-row-submit">保存</button>
		</form>
	</view>
</template>

<script>
	 
	import skyUpimg from "../../components/skyupimg.vue";
	export default{
		components:{
			skyUpimg
		},
		data:function(){
			return {
				pageLoad:false,
				data:{},
				telephone:"",
				usercard:"",
				errMsg:""
			}
		},
		onLoad:function(ops){
			if(ops.id!=undefined){
				id=ops.id;
			}
			console.log(this.$data)
			this.getPage();
		},
		methods:{
			setImgurl:function(e){
				this.usercard=e;
			},
			getPage:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/module.php?m=tutor_shop_apply",
					success:function(res){
						if(res.error){
							that.errMsg=res.message;
							return false;
						}
						that.pageLoad=true;
					}
				})
			},
			sendSms:function(){
				var that=this;
				if(!that.app.canPost()){
					return false;
				}
				that.app.get({
					url:that.app.apiHost+"/module.php?m=tutor_shop_apply&a=sendsms",
					data:{
						telephone:this.telephone
					},
					success:function(res){
						uni.showToast({
							title:res.message,
							icon:"none"
						})
					}
				})
			},
			submit:function(e){
				var that=this;
				that.app.post({
					url:that.app.apiHost+"/module.php?m=tutor_shop_apply&a=save",
					data:e.detail.value,
					success:function(res){
						uni.showToast({
							title:res.message,
							icon:"none"
						})
					}
				})
				
			}
		}
	}
</script>

<style>
</style>

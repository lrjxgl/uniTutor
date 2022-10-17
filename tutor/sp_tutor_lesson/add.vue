<template>
	<view>
		<form @submit="submit">
			<input type="hidden" name="lessonid" class="none" v-model="data.lessonid">
			<div class="input-flex">
				<div class="input-flex-label">名称</div>
				<input class="input-flex-text" type="text" name="title"  v-model="data.title">
			</div>
			<div class="input-flex">
				<div class="input-flex-label">分类</div>
				<input type="text" name="catid" class="none" v-model="data.catid" />
				<dtui-select @call-parent="setCat" class="flex-1" :val="data.catid" :dlist="catList"></dtui-select>
			</div>
			<div class="input-flex">
				<div class="input-flex-label">图片</div>
				<div class="flex-1">
					<input class="none" type="text" name="imgurl" v-model="data.imgurl" />
					<sky-upimg @call-parent="setImgurl" :imgurl="data.imgurl" :trueimgurl="data.trueimgurl"></sky-upimg>
				</div>
			</div>
			<div class="textarea-flex">
				<div class="textarea-flex-label">简介</div>
				<textarea class="textarea-flex-text h60" name="description" v-model="data.description"></textarea>
			</div>
			<div class="input-flex flex-ai-center">
				<div class="mgr-5">价格</div>
				<input type="text" name="money" v-model="data.money" class="input-flex-text w100" />
				<div class="mgl-5 mgr-5">元</div>
				<div class="flex-1"></div>
				<div class="mgr-5">课时</div>
				<input type="text" name="lesson_num" v-model="data.lesson_num" class="input-flex-text w100" />
				<div class="mgl-5">节</div>
			</div>
			<div class="input-flex flex-ai-center">
				<div class="input-flex-label">库存</div>
				<input type="text" v-model="data.total_num" name="total_num" class="input-flex-text" /> 
				<div class="mgl-5">份</div>
			</div> 
			<div class="">
				 <textarea class="none" name="content" v-model="data.content"></textarea>
				<lei-editor @call-parent="setContent" class="sky-editor-content"  :dcontent="data.content"></lei-editor>
			</div>	 
			 
			<button form-type="submit" class="btn-row-submit">保存</button>
		</form>
	</view>
</template>

<script>
	import skyUpimg from "../../components/skyupimg.vue"
	import dtuiSelect from "../../components/dtui-select.vue"
	import leiEditor from "../../components/leiEditor/leiEditor.vue";
	export default{
		components:{
			skyUpimg,
			dtuiSelect,
			leiEditor
		},
		data:function(){
			return {
				data:{},
				lessonid:0,
				catList:[]
			}
		},
		onLoad:function(ops){
			if(ops.lessonid!=undefined){
				this.lessonid=ops.lessonid;
			}
			this.getPage();
		},
		methods:{
			setImgurl:function(e){
				this.data.imgurl=e;
			},
			setContent:function(e){
				this.data.content=e;
			},
			setCat:function(e){
				this.data.catid=e;
			},
			getPage:function(){
				var that=this;
				that.app.get({
					url:that.app.apiHost+"/moduleshop.php?m=tutor_lesson&a=add",
					data:{
						lessonid:this.lessonid
					},
					success:function(res){
						if(res.data.data){
							that.data=res.data.data;
							
						}
						var c=JSON.stringify(res.data.catlist);
						c=c.replace("catid","id")
						console.log(c);
						that.catList=JSON.parse(c);
					}
				})
			},
			submit:function(e){
				var that=this;
				that.app.post({
					url:that.app.apiHost+"/moduleshop.php?m=tutor_lesson&a=save",
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

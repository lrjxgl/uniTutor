<template>
	<view>
		<div class="footer-row"></div>
		<div class="footer">
			<div @click="goL('/pages/tutor')" :class="tab=='index'?'footer-active':''" class="footer-item icon-home" >首页</div>


			<div @click="goL('/tutor/tutor_lesson/index')" :class="tab=='lesson'?'footer-active':''"  class="footer-item icon-cascades ">找课程</div>
			<div @click="goL('/tutor/tutor_shoplist/index')" :class="tab=='shoplist'?'footer-active':''"  class="footer-item icon-group ">找老师</div>
			<div @click="goL('/tutor/tutor_guest/index?tablename=tutor')" :class="tab=='guest'?'footer-active':''"class="footer-item icon-message_light ">消息</div>
			<div @click="goL('/tutor/tutor_user/index')" :class="tab=='user'?'footer-active':''"  class="footer-item icon-my_light ">我的</div>
		</div>
	</view>
</template>

<script>
	export default{
		props:{
			tab:""
		},
		methods:{
			goL:function(url){
				uni.reLaunch({
					url:url
				})
			}
		}
	}
</script>

<style>
</style>

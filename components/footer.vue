<template>
	<view>
		<view class="footer-row"></view>
		<view class="footer">
			<view class="footer-item icon-home" v-bind:class="{'footer-active':tab=='home'}" @click="goHome()">้ฆ้กต</view>
			 
			<view class="footer-item icon-my_light" v-bind:class="{'footer-active':tab=='user'}"  @click="goUser()">ๆ็</view>
		</view>
	</view>
</template>

<script>
	export default{
		props:{
			tab:""
		},
		data:function(){
			return {
				
			}
		},
		methods:{
			goHome:function(){
				uni.reLaunch({
					url:"../../pages/index/index"
				})
			},
			goPeople:function(){
				uni.reLaunch({
					url:"../../pagesblog/sblog_people/index"
				})
			},
			goChat:function(){
				uni.navigateTo({
					url:"../../pagesblog/sblog_chat/index"
				})
			},
			goUser:function(){
				uni.reLaunch({
					url:"../../pages/user/index"
				})
			} 
		}
	}
</script>

<style>
</style>

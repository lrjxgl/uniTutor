<template>
	<view>
		<div>
			<div class="none">xxx{{dlist.length}}</div>
			<div v-for="(item,index) in dlist" :key="index" >
			
				<div class="chatbox" v-if="item.ukey=='kefu'">
					<img :src="user.user_head+'.100x100.jpg'" class="wh-40 mgr-10" />
			
					<div class="flex-1">
						<div class="flex flex-ai-center mgb-5">
							<div class="cl2 flex-1">{{kefu.title}}</div>
							<div class="cl3 f12">{{item.timeago}}</div>
						</div>
						<div class="chatbox-desc-a">
							<div class="flex" v-if="item.filetype==''" v-html="item.content"></div>
							<img v-else-if="item.filetype=='img'"  @click="showBig(item.fileurl)" class="wh-60 pointer" :src="item.fileurl+'.100x100.jpg'"  />
							<div  @click="downFile(item.fileurl)"  class="iconfont flex pointer icon-file" v-else-if="item.filetype=='file'">
								点击下载文件
							</div>
							
							
							<div @click="showVideo(item.fileurl)" class="flex pointer iconfont icon-video_light" v-else-if="item.filetype=='video'">
								点击查看视频
							</div>
						</div>
					</div>
				</div>
				<div class="chatbox" v-else>
					<div class="flex-1"></div>
					<div class="w200">
			
						<div class="mgb-5 flex-ai-center flex">
							<div class="flex-1"></div>
							<div class="cl2 mgr-5">{{user.nickname}}</div>
							<div class="cl3 f12">{{item.timeago}}</div>
						</div>
						<div class="chatbox-desc-b">
							<div class="flex" v-if="item.filetype==''" v-html="item.content"></div>
							<img v-else-if="item.filetype=='img'"  @click="showBig(item.fileurl)" class="wh-60 pointer" :src="item.fileurl+'.100x100.jpg'"  />
							<div  @click="downFile(item.fileurl)"  class="iconfont flex pointer icon-file" v-else-if="item.filetype=='file'">
								点击下载文件
							</div>
							
							
							<div @click="showVideo(item.fileurl)" class="flex pointer iconfont icon-video_light" v-else-if="item.filetype=='video'">
								点击查看视频
							</div>
						</div>
					</div>
			
					<img :src="user.user_head+'.100x100.jpg'" class="wh-40 mgl-10" />
				</div>
			</div>
			<div v-if="bigModal">
				<div @click="bigModal=false" class="modal-mask"></div>
				<div class="modal">
					<img @click="bigModal=false" :src="bigImg" style="width:100%;height:auto" />
				</div>
			</div>
			<div v-if="videoModal">
				<div @click="videoModal=false" class="modal-mask"></div>
				<div class="modal">
					<video controls="controls"  :src="videourl" style="width:100%;height:200px"  ></video>
				</div>
			</div>
			 
		</div>	
	</view>
</template>

<script>
	export default{
		props:{
			list:{},
			user:{},
			kefu:{},
			
		},
		data:function(){
			return {
				dlist:[],
				bigModal:false,
				bigImg:"",
				videoModal:false,
				videourl:""
			}
		},
		created:function(){
			this.dlist=this.list;
		},
		watch:{
			list:function(n,o){
				this.dlist=n;
				console.log(n)
			}
		},
		methods:{
			showBig:function(imgurl){
				this.bigModal=true;
				this.bigImg=imgurl
			},
			showVideo:function(url){
				this.videoModal=true;
				this.videourl=url
			},
			downFile:function(url){
				var a=document.createElement("a");
				a.setAttribute("target","_blank");
				a.setAttribute('download', '');// download属性
				a.setAttribute('href', url);
				a.click();
			},
		}
	}
</script>

<style>
	.chatbox {
		display: flex;
		flex-direction: row;
		margin-bottom: 10px;
	
	}
	 
	.chatbox-nick-a,
	.chatbox-nick-b {
		color: #666;
	}
	
	.chatbox-nick-a {
		margin-right: 10px;
	}
	
	.chatbox-nick-b {
		margin-left: 10px;
	}
	
	.chatbox-desc-a,
	.chatbox-desc-b {
	
		padding: 10px 20px;
		background-color: #efefef;
		border-radius: 20px;
		position: relative;
		flex-direction: row;
	}
	
	.chatbox-desc-b {
		margin-left: 30px;
		text-align: right;
	}
	
	.chatbox-desc-a:after,
	.chatbox-desc-b:after {
		font-family: iconfont;
		position: absolute;
		color: #efefef;
	
	}
	
	.chatbox-desc-a:after {
		content: "\e601";
		left: -6px;
		top: 3px;
	}
	
	.chatbox-desc-b:after {
		content: "\e635";
		right: -6px;
		top: 10px;
	}
</style>

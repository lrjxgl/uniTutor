<template>
	<view>
		<div v-if="pageLoad" class="bg-white pd-10" >
			<div @click="getPage" v-if="hasNew" class="newMsg">新</div>
			<div class="old">
				<div :id="'oitem'+lsi"  v-for="(lsa,lsi) in listArray" :key="lsi" >
					<div class="oitem"  v-if="lsa">
					<com-list  :kefu="kefu" :user="user" :list="lsa.list"></com-list>
					</div>
				</div>
				
			</div>
			<div class="new">
				<com-list :kefu="kefu" :user="user" :list="list"></com-list>
			</div>
			
			<div style="height: 90px;"></div>
			<div style="position: fixed; z-index: 10; background-color: #fff; bottom: 0;left: 0;right: 0;">
				<div class="pdl-5 pdr-5 flex">
					<input type="text" v-model="content" class="input-flex-text" />
					<div @click="submit()" class="input-flex-btn" id="submit">发送</div>
				</div>
				<div style="height: 40px;" class="flex flex-center">
					
					<div @click="choiceImg('pic')" class="flex-1 iconfont icon-pic f20"></div>
					<div @click="choiceVideo('video')" class="flex-1 iconfont icon-video_light f20"></div>
					<div @click="emoClass='flex-col'" class="flex-1 iconfont icon-emoji f20"></div>
					<div @click="choiceFile('file')" class="flex-1 iconfont  icon-file f20 "></div>
					 
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
					<video controls="controls"  :src="videourl" style="width:100%;height:auto"  ></video>
				</div>
			</div>
			<div id="emoModal" :class="emoClass" class="modal-group">
				<div class="modal-mask" @click="emoClass=''"></div>
				<div class="emoFixbox">
			
					<div class="pd-10">
						<div class="flex flex-wrap">
							<div @click="addEmo(item)" class="imEmo" :class="'imEmo-'+index" v-for="(item,index) in emoList" :key="index"></div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</view>
</template>

<script>
	var gApp=getApp().globalData;
	var wsclient_to="";
 
	import comList from "./com-list.vue";
	import EMO from "../../common/emo.js"
	export default{
		components:{
			comList
		},
		data: function() {
			return {
				pageLoad: false,
				kfid:0,
				tablename:"",
				objectid:0,
				user:{},
				kefu:{},
				list:[],
				olist:[],
				listMaxId:30,
				listArrayIndex:30,
				listArray:[],
				isFirst: true,
				per_page: 0,
				hasNew: false,
				content:"",
				showGift: false,
				emoClass: "none",
				emoList:[],
				bigModal:false,
				bigImg:"",
				fileurl:"",
				filetype:"",
				videoModal:false,
				videourl:""
			}
		},
		onLoad: function(ops) {
			var that=this;
			if(ops.kfid!=undefined){
				this.kfid=ops.kfid;
			}
			if(ops.tablename!=undefined){
				this.tablename=ops.tablename;
			}
			if(ops.objectid!=undefined){
				this.objectid=ops.objectid;
			}
			
			this.getPage();
			this.getNew();
			//wss
			wsclient_to="kefuID"+this.kfid;
			gApp.getWsKey();
			gApp.wsMessage=function(e){
				if(e.page!=undefined && e.page=="kefu/kefu_msg"){
					if(e.wsclient_to!=wsApp.wsKey){
						return false;
					}
					if(e.type=="say"){
						that.list.push(e);
						//that.getNew();
					}
				}
			}
			this.emoList=EMO.emoList();
			console.log(this.emoList)
		},
		watch:{
			list:function(n,o){
				this.$nextTick(function(){
				 
					setTimeout(function(){
						window.scrollTo(0,10000);
					},100)
					 
				})
			},
			listArray:function(n,o){
				this.$nextTick(function(){
					var i=this.listArrayIndex+1;
					if(i==30){
						var top=$(".new").offset().top;
					}else{
						var top=$("#oitem"+i).offset().top;
					}
					
					window.scrollTo(0,top+50);
					 
				})
			}
		},
		methods: {
			showBig:function(imgurl){
				this.bigModal=true;
				this.bigImg=imgurl
			},
			showVideo:function(url){
				this.videoModal=true;
				this.videourl=url
			},  
			getPage: function() {
				var that = this;
				that.app.get({
					url: that.app.apiHost+"/module.php?m=kefu_msg&ajax=1",
					data: {
						kfid: this.kfid,
						tablename:this.tablename,
						objectid:this.objectid
					},
					dataType: "json",
					success: function(res) {
						that.user=res.data.user;
						that.kefu=res.data.kefu;				
						that.pageLoad = true;
					}
				})
			},
			getList: function() {
				var that = this;
				if (!that.isFirst && that.per_page == 0) return false;
				that.app.get({
					 
					url: that.app.apiHost+ "/module.php?m=kefu_msg&a=list&ajax=1",
					data: {
						per_page: that.per_page,
						kfid: this.kfid,
						tablename:this.tablename,
						objectid:this.objectid
					},
					success: function(res) {
						if (that.isFirst) {
							var list=[];
							for(var i in res.data.list){
								var item=res.data.list[i];
								item.content=EMO.decodeEmo(item.content);
								list.push(item);
							}
							that.list = res.data.list;
							
							that.isFirst = false;
						} else {
							
							for (var i in res.data.list) {
								var item=res.data.list[i];
								item.content=EMO.decodeEmo(item.content);
								that.olist.push(item);
							}
						}
						that.per_page = res.data.per_page;
						
					}
				})
			},
			getOldList: function() {
				var that = this;
				if (this.per_page == 0) return false;
				that.loadIng = true;
				that.app.get({
					dataType: "json",
					url:that.app.apiHost+"/module.php?m=kefu_msg&a=list&ajax=1",
					data: {
						per_page: that.per_page,
						kfid: this.kfid,
						tablename:this.tablename,
						objectid:this.objectid
					},
					success: function(res) {
						that.per_page = res.data.per_page;
						var list=[];
						
						for(var i in that.listArray){
							list.push(that.listArray[i]);
						}
						list[that.listArrayIndex] = {list: res.data.list};
						that.listArray=list;
						that.listArrayIndex--;
					}
				})
			},
			getNew:function(){
				this.isFirst=true;
				this.per_page=0;
				this.getList();
			},
			submit: function() {
				var that=this;
				if(!that.app.canPost()){
					return false;
				}
				that.app.post({
					url:that.app.apiHost+"/module.php?m=kefu_msg&a=save&ajax=1",
					dataType:"json",
					type:"POST",
					data:{
						content: this.content,
						fileurl:this.fileurl,
						filetype:this.filetype,
						kfid:that.kefu.kfid,
						userid:that.user.userid,
					},
					success:function(res){
						var msg=JSON.stringify({
							wsclient_to: wsclient_to,
							type: "say",
							page:"kefu/kefu_msg",
							content: that.content,
							fileurl:that.app.images_site+that.fileurl,
							filetype:that.filetype,
							kfid:that.kefu.kfid,
							userid:that.user.userid,
							ukey:"user"
						})
						var msgItem={
							content:that.content,
							fileurl:that.app.images_site+that.fileurl,
							filetype:that.filetype,
							kfid:that.kefu.kfid,
							userid:that.user.userid,
							ukey:"user"
						}
						console.log(msgItem);
						that.list.push(msgItem);
						gApp.ws.send(msg);
						that.content="";
						that.fileurl="";
						that.filetype="";
						//that.getNew();
					}
				}) 
				
			},
			addEmo: function(s) {
				s = "\\" + s + " ";
				this.content += s;
			},
			downFile:function(url){
				uni.downloadFile({
					url:url,
					success:function(e){
						uni.showToast({
							title:"下载成功",
							icon:"none"
						})
					}
				})
			},
			choiceImg: function() {
				var that = this;
				this.skyUpload("upimg","/index.php?m=upload&a=img",function(e){
					var res=JSON.parse(e);
					if(!res.error){					
						that.content='';
						that.fileurl=res.data.imgurl;
						that.filetype="img";
						that.submit()
					}
					
				},function(e){
					console.log(e)
				},function(e){
					console.log(e)
				})
			},
			choiceVideo:function(){
				var that=this;
				uni.chooseVideo({
					count:1,
					sourceType: ['camera', 'album'],
					success: (ress) => {
						that.videoProgress=1;
						var tempFilePath = ress.tempFilePath;
						uni.request({
							url:that.app.apiHost+"/index.php?m=ossupload&a=auth&ajax=1",
							method:"GET",
							data:{
								authcode:that.app.getAuthCode(),
								fromapp:that.app.fromapp(),
								ajax:1
							},
							success:(rs)=>{
								var oos=rs.data;
								
								var fdata={
									OSSAccessKeyId:oos.accessid,
									policy:oos.policy,
									Signature:oos.sign,
									key:oos.key + ".mp4",
									callback:oos.callback
								};
								 
								var uploadTask=uni.uploadFile({
									url: oos.url, 
									filePath: tempFilePath,
									name: 'file',
									formData:fdata,
									success: (res)=>{
										var data=JSON.parse(res.data);
										 
										that.content='';
										that.fileurl=data.filename;
										that.filetype="video";
										that.submit() 
										 
									}
								});
								uploadTask.onProgressUpdate((res) => {
									that.videoProgress=res.progress;
								  
								});
								 
							}
						})	
						
					}
				});
				
			},
			choiceFile: function() {
				var that=this;
				this.skyUpload("upimg","/index.php?m=upload&a=uploadfile",function(e){
					var res=JSON.parse(e);
					if(!res.error){					
						that.content='';
						that.fileurl=res.imgurl;
						that.filetype="file";			
						that.submit()
					}
				},function(e){
					console.log(e)
				},function(e){
					console.log(e)
				})
			},
			error:function(e){},
			uploadProgress:function(e){},
			skyUpload: function(field,url,success,fail) {
				var that=this;
				uni.chooseImage({
					success:function(chooseImageRes){
						let tempFilePaths = chooseImageRes.tempFilePaths;
						uni.uploadFile({
							url: that.app.apiHost+url+"&ajax=1&authcode="+that.app.getAuthCode(), 
							filePath: tempFilePaths[0],
							name: field,
							 
							success: (uploadFileRes) => {
								success(uploadFileRes.data);
							}
						});
					}
					
				});
			}
		}
	}
	 
</script>

<style>
	@import url("../../common/emo.css");
	
</style>

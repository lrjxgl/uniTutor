<script>
	import comm from "./common/common.js"
	export default {
		onLaunch: function() {
		 
			
			// #ifdef APP-PLUS
			/*
			// 锁定屏幕方向
			setTimeout(function(){
				plus.screen.lockOrientation('portrait-primary'); //锁定
				console.log(plus.runtime.version);
				// 检测升级
				uni.request({
					url: 'https://www.fd175.com/index.php?m=app&a=checkupdate', //检查更新的服务器地址
					data: {
						appid: plus.runtime.appid,
						version: plus.runtime.version,
						imei: plus.device.imei
					},
					success: (res) => {
						console.log('success', res);
						if (res.statusCode == 200 && res.data.isUpdate) {
							let openUrl = plus.os.name === 'iOS' ? res.data.iOS : res.data.Android;
							// 提醒用户更新
							uni.showModal({
								title: '更新提示',
								content: res.data.note ? res.data.note : '是否选择更新',
								success: (showResult) => {
									if (showResult.confirm) {
										plus.runtime.openURL(openUrl);
									}
								}
							})
						}
					}
				})
				//推送
				uni.getProvider({
				service: 'push',
				success: function (res) {
					uni.onPush({
						provider: 'igexin',
						success: function () {
							console.log('监听透传成功');
						},
						callback: function (data) {
							uni.showToast({
								title: "接收到透传数据：" + JSON.stringify(data)
							});
							 
						}
					});
				}
			});
			},300)
			*/
			// #endif
		},
		onShow: function() {
			if(!this.globalData.ws){
				this.globalData.wsInit() 
				
			} 
			
	
		},
		onHide: function() {
			
		},
		
		globalData:{
			ws:null,
			wsHost:comm.wsHost,
			wsKey:"",
			wsGid:"",
			wsMessage:function(){},
			wsInit:function(){
				if(this.wsKey==''){
					return false;
				}
				var that=this;
				if(that.ws!=null){
					that.ws.close();
					that.ws=null;
				}
				that.ws=uni.connectSocket({
					url:this.wsHost,
					complete: (e)=> {
						console.log(e)
						
					}
				});
				
				that.ws.onOpen(function(e){
					 
					var msg = JSON.stringify({
						type: "login",
						k: that.wsKey,
						gid: that.wsGid,
						content: "login"
					});
					
					try{
						that.ws.send({
							data: msg
						});
					}catch(e){
						that.wsInit();
					}
					 
				});
				that.ws.onError(function(e){
					
					that.wsInit();
				})
				that.ws.onClose(function(e){
					that.ws=null;
					
				})
				that.ws.onMessage(function(e){
					var res = JSON.parse(e.data);
					that.wsMessage(res);
				});
			},
			getWsKey:function(){
				var that=this;
				comm.get({
					url:comm.apiHost+"/index.php?m=websocket&a=getWsKey&ajax=1",
					dataType:"json",
					success:function(res){
						
						if(!res.error){
							that.wsKey=res.data.key;
							that.wsInit(); 
						} 
						
					}
				})
			},
			getUserClient: async function(userid){
				var that=this;
				var [error,res]=await uni.request({
					url:comm.apiHost+"/index.php?m=websocket&a=getWsKey&ajax=1&userid="+userid
				});
				var data=res.data;
				if(data.error){
					return "";
				}else{
					
					return data.data.key;
				}
			 
				
			}
		},
		 
		
		watch: {
			'$route'(to, from) {

				const toDepth = to.path.split('/').length
				const fromDepth = from.path.split('/').length


			}
		}
	}
</script>

<style>
	@import "./common/iconfont.css";
	@import "./common/dt-ui-uni.css";

	/*修复uniapp flex*/
	.uni-page-head {
		display: flex;
		flex-direction: row;
	}

	.uni-tabbar,
	.uni-picker-view-wrapper {
		flex-direction: row !important;
	}

	.uni-swiper-dots {
		flex-direction: row;
	}

	.uni-scroll-view>div {
		flex-direction: row;
	}

	.uni-modal__ft {
		flex-direction: row;
	}

	/**/
	.imgWidth {
		width: 100%;
	}

	.fixToHome {
		position: fixed;
		top: 10px;
		left: 10px;
		background-color: #efefef;
		border-radius: 5px;
		cursor: pointer;
		padding: 5px 10px;
		color: #333;
		font-size: 14px;
		z-index: 999;
	}

	.badge {
		right: 6px;
		top: 3px;
		font-size: 12px;
		background-color: #dd524d;
		line-height: 1;
		border-radius: 50%;
		height: 14px;
		line-height: 1;
		padding-top: 1px;
		width: 14px;
		overflow: hidden;
		box-sizing: border-box;
		color: #fff;
	}

	.badge-abs {
		position: absolute;
	}

	.badge-mini {
		width: 6px;
		height: 6px;
		;
	}

	.input-flex-btn {
		z-index: 2;
	}

	.header,
	.header-row {
		display: none;
	}

	.fixedAdd {
		position: fixed;
		bottom: 200upx;
		right: 7upx;
		width: 92upx;
		height: 92upx;
		text-align: center;
		box-sizing: border-box;
		background-color: rgba(240, 85, 75, .82);
		color: #fff;
		font-family: iconfont;
		font-size: 32upx;
		padding-top: 11upx;
		border-radius: 22upx;

	}

	.fixedAdd:before {
		content: "\e7e8";
		display: block;
		font-size: 32upx;
	}

	.back-top {
		position: fixed;
		right: 10px;
		bottom: 240px;
		background-color: #009688;
		width: 40px;
		height: 40px;
		border-radius: 50%;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.back-top-icon {
		font-family: iconfont;
		line-height: 1;
	}

	.back-top-icon:after {
		content: "\e603";
		font-size: 12px;
		color: #fff;
	}

	.back-top-text {
		font-size: 12px;
		line-height: 1;
		font-weight: 300;
		color: #fff;
	}

	.buy-btn {
		display: inline-block;
		height: 30px;
		line-height: 30px;
		text-align: center;
		width: 30px;
		border: 1px solid #83c44e;
		color: #83c44e;
		border-radius: 50%;
		float: right;
		cursor: pointer;
	}

	.buy-btn-active {
		background-color: #83c44e;
		color: #fff;
	}

	.btn-mini-cycle {
		line-height: 12px;
		font-size: 12px;
		padding: 2px 5px;
		border-radius: 5px;
		border: 1px solid #f60;
		color: #f60;
	}

	.btn-type,
	.btn-type-wm {
		line-height: 12px;
		font-size: 12px;
		padding: 2px 5px;
		border-radius: 5px;
		border: 1px solid #007BFF;
		color: #0000FF;
	}

	.btn-type {
		width: 22px;
	}

	.unLoginBox {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-color: #fff;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
	.adBox{
		display: flex;
		flex-direction: row;
	}
	.adBox-item{
		flex: 1;
	}
	.adBox-img{
		width: 100%;
	}
	/*个人中心*/
	.uhead {
		display: flex;
		flex-direction: row;
		align-items: center;
		
		padding: 10px;
		background-color: #fff;
		margin-bottom: 5px;
	}
		
	.uhead-img {
		width: 80px;
		height: 80px;
		margin-right: 10px;
		display: block;
		border-radius: 50%;
	}
		
	.uhead-box {
		flex: 1;
	}
		
	.uhead-nick {
		margin-bottom: 5px;
		font-size: 16px;
	}
		
	.uhead-rnum {
		color: #999;
		margin-bottom: 10px;
		line-height: 14px;
		display: flex;
		font-size: 14px;
	}
	 
	.cl-u{
		color: #F30;
	}
</style>

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
							<div @click="goLession(item.lessonid)" class="cl-primary mgb-5 f14">课程：{{lesson.title}}
							</div>
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


		</div>

		<div class="row-box">
			<div class="flex">
				<div class="flex-1"></div>
				<div v-if="item.status==0" @click="accept(item)" class="btn-small mgr-10">确认接单</div>
				<div v-if="item.status==1" @click="send(item)" class="btn-small mgr-10">到家对接</div>
				<div v-if="item.status==2" @click="finish(item)" class="btn-small mgr-10">订单完成</div>
				<div v-if="item.status==0" @click="cancel(item)" class="btn-small btn-danger">取消订单</div>
			</div>
		</div>
		<div class="row-box mgb-5" v-if="item.israty">
			<sky-raty readyonly="true" fields="raty_grade" label="评价" :grade="item.raty_grade" len="10"></sky-raty>
			<div class="textarea-flex-text h60" v-html="raty_content"></div>
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
	export default {

		data:function() {
			return {
				orderid:0,
				item: {},
				logList: [],
				raty_content: "",
				raty_grade: 8,
				shop: {},
				lesson: {}
			}
		},
		onLoad: function(ops) {
			this.orderid=ops.orderid;
			this.getPage();
		},
		methods: {
			setRatyGrade: function(e) {
				this.raty_grade = e;
			},
			goLession: function(lessonid) {
				uni.navigateTo({
					url:"../tutor_lesson/show?lessonid="+lessonid
				})
			},
			goShop: function(shopid) {
				uni.navigateTo({
					url:"../tutor_shop/index?shopid="+shopid
				})
			},
			getPage: function() {
				var that = this;
				that.app.get({
					url: that.app.apiHost+"/moduleshop.php?m=tutor_order&a=show&ajax=1",
					data: {
						orderid: this.orderid
					},
					dataType: "json",
					success: function(res) {
						that.item = res.data.data;
						that.logList = res.data.logList;
						that.lesson = res.data.lesson;
						that.shop = res.data.shop;
						if (res.data.raty) {
							that.raty_grade = res.data.raty.raty_grade;
							that.raty_content = res.data.raty.raty_content;
						}
					}
				})
			},

			accept: function(item) {
				var that = this;
				that.app.get({
					url: that.app.apiHost+"/moduleshop.php?m=tutor_order&a=accept&ajax=1",
					data: {
						orderid: item.orderid
					},
					dataType: "json",
					success: function(res) {

						uni.showToast({
							title:res.message,
							icon:"none"
						})
						if (!res.error) {
							that.getPage();
						}
					}
				})
			},
			send: function(item) {
				var that = this;
				that.app.get({
					url: that.app.apiHost+"/moduleshop.php?m=tutor_order&a=send&ajax=1",
					data: {
						orderid: item.orderid
					},
					dataType: "json",
					success: function(res) {

						uni.showToast({
							title:res.message,
							icon:"none"
						})
						if (!res.error) {
							that.getPage();
						}
					}
				})
			},
			finish: function(item) {
				var that = this;
				uni.showModal({
					title: "确认提示",
					content: "确认回收完成吗？",
					success: function(e) {
						if(!e.confirm){
							return false;
						}
						that.app.get({
							url: that.app.apiHost+"/moduleshop.php?m=tutor_order&a=finish&ajax=1",
							data: {
								orderid: item.orderid
							},
							dataType: "json",
							success: function(res) {

								uni.showToast({
									title:res.message,
									icon:"none"
								})
								if (!res.error) {
									that.getPage();
								}
							}
						})
					}
				})
			},
			cancel: function(item) {
				var that = this;
				uni.showModal({
					title: "确认提示",
					content: "确认取消回收吗？",
					success: function(e) {
						if(e.confirm){
							return false;
						}
						that.app.get({
							url: that.app.apiHost+"/moduleshop.php?m=tutor_order&a=cancel&ajax=1",
							data: {
								orderid: item.orderid
							},
							dataType: "json",
							success: function(res) {
								uni.showToast({
									title:res.message,
									icon:"none"
								})
								 
								if (!res.error) {
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

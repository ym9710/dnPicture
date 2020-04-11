<template>
	<scroll-view
	scroll-y
	enable-flex
	class="video_main"
	@scrolltolower="handleScrolltoLower"
	>
		<view class="video_item"
		v-for="item in videowp"
		:key="item.id"
		@click="handleGoVideo(item)"
		>
		<image mode="widthFix" :src="item.img"></image>
		</view>
	</scroll-view>
</template>

<script>
	export default {
		props:{
			urlobj:Object
		},
		data(){
		  return{
			  videowp:[],
			  hasMore:true
		  }	
		},
		watch:{
		urlobj(){
			console.log("参数变化");
			console.log(this.urlobj);
			this.videowp=[];
			this.getList();
		}	
		},
		mounted(){
			
			console.log(this.urlobj);
			this.getList();
		},
		methods:{
			getList(){
				this.request({
					url:this.urlobj.url,
					data:this.urlobj.params
				}).then(result=>{
					console.log(result);
					if(result.res.videowp.length===0){
						this.hasMore=false;
						uni.showToast({
							title:"没有了",
							icon:"none"
						})
						return;
					}
					
					this.videowp=[...this.videowp,...result.res.videowp];
				})
			},
			handleScrolltoLower(){
				if(this.hasMore){
					this.urlobj.params.skip+=this.urlobj.params.limit;
					this.getList();
				}else{
					uni.showToast({
						title:"没有了",
						icon:"none"
					})
				}
			},
			handleGoVideo(item){
				getApp().globalData.video=item;
				uni.navigateTo({
					url:"/pages/videoPlay/videoPlay"
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
.video_main{
	display: flex;
	flex-wrap: wrap;
	justify-content: center;
	height: calc(100vh - 36px);
	.video_item{
		width: 30%;
		border: 1px solid #fff;
		image{
			width: 100%;
		}
	}
}
</style>

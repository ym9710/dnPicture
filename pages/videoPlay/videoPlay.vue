<template>
	<view class="video_play">
		<image :src="videObj.img"></image>
			<!-- 工具栏 开始 -->
		<view class="video_tool">
			<view class="soundIcon">
				<view class="img1" @click="handleMuted">声音</view>
			</view>
			<view class="shareIcon">
				<view class="img2" >
					<button open-type="share">转发</button>	
					转发
				</view>
			</view>
		</view>
		<!-- 工具栏 结束 -->
		<!-- 视频 开始 -->
		<view class="video_wrap">
			<video :muted="muted" :src="videObj.video" objectFit="fill"></video>
		</view>
		<!-- 视频 结束 -->
		
		<!-- 下载 开始 -->
		<view class="download">
			<view class="download_btn" @click="handleDownload">下载高清</view>
		</view>
		<!-- 下载 结束 -->
	</view>
</template>

<script>
	export default {
		data(){
		return{
			videObj:{},
			muted:false
		}	
		},
		onLoad() {
			console.log(getApp().globalData);
			this.videObj=getApp().globalData.video;
		},
		methods:{
			handleMuted(){
				this.muted=!this.muted;
			},
			async handleDownload(){
				await uni.showLoading({
					title:"下载中"
				})
			const {tempFilePath}=(await uni.downloadFile({url:this.videObj.video}))[1];
			await uni.saveVideoToPhotosAlbum({
				filePath:tempFilePath
			});
			uni.hideLoading();
			await uni.showToast({
				title:"下载成功"
			})
			}
		},
			
	}
</script>

<style lang="scss" scoped>
.video_play{
	image{
		position: absolute;
		width: 100vw;
		height: 100vh;
		z-index: -1;
		filter: blur(20px);
	}
	.video_tool{
		height: 80rpx;
		display: flex;
		justify-content: flex-end;
		.soundIcon{
			width: 80rpx;
			
			.img1{
				width: 100%;
			}
		}
		.shareIcon{
			width: 80rpx;
			position: relative;
			button{
				position: absolute;
				width: 100%;
				height: 100%;
				opacity: 0;
			}
		}
	}
	.video_wrap{
		display: flex;
		justify-content: center;
		video{
			width: 360rpx;
			height: 600rpx;
		}
	}
	.download{
		display: flex;
		justify-content: center;
		margin-top: 30rpx;
		.download_btn{
			width: 360rpx;
			height: 80rpx;
			border-radius: 40rpx;
			display: flex;
			justify-content: center;
			align-items: center;
			color: #fff;
			border: 1rpx solid #fff;
			background-color: rgba(0,0,0,.6);
		}
	}
}
</style>

<template>
	<view>
		<!-- 用户信息 开始 -->
		<view class="user_info">
			<view class="user_icon"><image :src="imgDetail.user.avatar" mode="widthFix"></image></view>
			<view class="user_desc">
				<view class="user_name">{{imgDetail.user.name}}</view>
				<view class="user_time">{{imgDetail.cnTime}}</view>
				
			</view>
		</view>
		<!-- 用户信息 结束 -->
		
		<!-- 高清大图 开始 -->
		<view class="high_img">
			<swiper-action @swiperAction="handleSwiperAction">
			<image mode="widthFix" :src="imgDetail.thumb"></image>
			</swiper-action>
		</view>
		<!-- 高清大图 结束 -->
		
		<!-- 点赞 开始 -->
		<view class="user_rank">
			<view class="rank">
				<text class="iconfont icondianzan">{{imgDetail.rank}}</text>
			</view>
			<view class="user_collect">
				<text class="iconfont iconshoucang">收藏</text>
			</view>
		</view>
		<!-- 点赞 结束 -->
		
		<!-- 专辑 开始 -->
		<view class="album_wrap" v-if="album.length">
			<view class="album_title">相关</view>
			<view class="album_list">
				<view class="album_item"
				v-for="item in album"
				:key="item.id"
				>
				<view class="album_cover">
					<image :src="item.cover" mode="aspectFill"></image>
				</view>
				<view class="album_info">
					<view class="album_info_text">专辑</view>
					<view class="album_name">{{item.name}}</view>
					<text class="iconfont iconiconfontjiantou4"></text>
				</view>
				</view>
			</view>
		</view>
		<!-- 专辑 结束 -->
		
		<!-- 最热评论 开始 -->
		<view class="comment hot" v-if="hot.length">
			<view class="comment_title">
				<text class="iconfont iconhot1"></text>
				<text class="comment_text">最热评论</text>
			</view>
			<view class="comment_list">
				<view class="comment_item"
				v-for="item in hot"
				:key="item.id"
				>
				<!-- 用户 -->
				<view class="comment_user">
					<view class="user_icon">
						<image :src="item.user.avatar" mode="widthFix"></image>
					</view>
					<view class="user_name">
						<view class="user_nickname">{{item.user.name}}</view>
						<view class="user_time">{{item.cnTime}}</view>
					</view>
					<view class="user_badge">
						<image
						v-for="item2 in item.user.title"
						:key="item2.icon"
						:src="item2.icon"
						></image>
					</view>
				</view>
				<!-- 评论数据 -->
				<view class="comment_desc">
					<view class="comment_content">{{item.content}}</view>
					<view class="comment_like">
						<text class="iconfont icondianzan">{{item.size}}</text>
					</view>
				</view>
				</view>
			</view>
		</view>
		<!-- 最热评论 结束 -->
		
		<!-- 最新评论 开始 -->
		<view class="comment new" v-if="comment.length">
			<view class="comment_title">
				<text class="iconfont iconpinglun"></text>
				<text class="comment_text">最新评论</text>
			</view>
			<view class="comment_list">
				<view class="comment_item"
				v-for="item in comment"
				:key="item.id"
				>
				<!-- 用户 -->
				<view class="comment_user">
					<view class="user_icon">
						<image :src="item.user.avatar" mode="widthFix"></image>
					</view>
					<view class="user_name">
						<view class="user_nickname">{{item.user.name}}</view>
						<view class="user_time">{{item.cnTime}}</view>
					</view>
					<view class="user_badge">
						<image
						v-for="item2 in item.user.title"
						:key="item2.icon"
						:src="item2.icon"
						></image>
					</view>
				</view>
				<!-- 评论数据 -->
				<view class="comment_desc">
					<view class="comment_content">{{item.content}}</view>
					<view class="comment_like">
						<text class="iconfont icondianzan">{{item.size}}</text>
					</view>
				</view>
				</view>
			</view>
		</view>
		<!-- 最新评论 结束 -->
		
		<!-- 下载 开始 -->
		<view class="download">
			<view class="download_btn" @click="handleDownload">下载图片</view>
		</view>
		<!-- 下载 结束 -->
	</view>
</template>

<script>
import moment from "moment";
import swiperAction from "../../components/swiperAction.vue"
moment.locale("zh-cn");
	export default {
		components:{
			swiperAction
		},
		data(){
			return{
				/* 图片信息对象 */
				imgDetail:{},
				album:[],
				hot:[],
				comment:[],
				imgIndex:0
			};
		},
		onLoad() {
			console.log(getApp().globalData);
			const {imgIndex}=getApp().globalData;
			
			this.imgIndex=imgIndex;
			this.getData();
		},
		methods:{
			getData(){
				const { imgList } =getApp().globalData;
				this.imgDetail=imgList[this.imgIndex];
				/* this.imgDetail.newThumb=this.imgDetail.thumb+this.imgDetail.rule.replace('$<Height>',360); */
						
				this.imgDetail.cnTime=moment(this.imgDetail.atime*1000).fromNow();
				
				/* 获取图片详情id */
				this.getComments(this.imgDetail.id);
			},
			getComments(id){
				this.request({
					url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`
				}).then(result=>{ 
					console.log(result);
					this.album=result.res.album;
					
					/* 给hot数组中对象添加时间属性xxx月/年前 */
					result.res.hot.forEach(v=>v.cnTime=moment(v.atime*1000).fromNow());
					result.res.comment.forEach(v=>v.cnTime=moment(v.atime*1000).fromNow());
					
					this.hot=result.res.hot;
					this.comment=result.res.comment;
				})
			},
			handleSwiperAction(e){
				console.log(e);
				const { imgList } =getApp().globalData;
				if(e.direction==="left"&&this.imgIndex<imgList.length-1){
					//可以左滑
					this.imgIndex++;
					this.getData();
				}else if(e.direction==="right"&&this.imgIndex>0){
					this.imgIndex--;
					this.getData();
				}else{
					uni.showToast({
						title:"没有数据了",
						icon:"none"
					})
				}
			},
			async handleDownload(){
				await uni.showLoading({
					title:"下载中"
				})
				
				const  result1=await uni.downloadFile({url:this.imgDetail.img});
				const {tempFilePath}=result1[1];
				
				const result2=await uni.saveImageToPhotosAlbum({filePath:tempFilePath});
				console.log(result2);
				console.log("下载成功");
				uni.hideLoading();
				await uni.showToast({
					title:"下载成功"
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
.user_info{
	display: flex;
	padding: 20rpx;
	.user_icon{
		padding: 0 20rpx;
		image{
			width: 88rpx;
			border-radius: 50%;
		}
	}
	
	.user_desc{
		.user_name{
			color: #000;
			font-weight: 600;
		}
		.user-time{
			color: #ccc;
			font-size: 24rpx;
			padding: 10rpx 0;
		}
	}
}

.high_img{
	image{
		width: 100%;
		border-bottom:1rpx solid #eee ;
	}
}

.user_rank{
	display: flex;
	height: 80rpx;
	border-bottom:5rpx solid #eee;
	.rank{
		display: flex;
		justify-content: center;
		align-items: center;
		flex: 1;
		.iconfont{
			
		}
	}
	
	.user_collect{
		flex:1;
		display: flex;
		justify-content: center;
		align-items: center;
		.iconfont{
			
		}
	}
}

.album_wrap{
	padding: 20rpx;
	.album_title{
		padding: 10rpx 0;
	}
	.album_list{
		.album_item{
			display: flex;
			padding: 10rpx 0;
			border-bottom: 10rpx solid #000;
			.album_cover{
				flex:1;
				image{
					width: 180rpx;
					height: 180rpx;
				}
			}
			
			.album_info{
				flex: 3;
				padding-left: 20rpx;
				.album_info_text{
					width: 100rpx;
					height: 50rpx;
					background-color: #4CD964;
					color: #fff;
					justify-content: center;
					align-items: center;
				}
				
				.album_name{
					padding: 10rpx 0;
					color: #888;
				}
				.iconfont{
					font-size: 40rpx;
					position: absolute;
					top:50%;
					transform: translateY(-50%);
					right: 10%;
					color:#000;
				}
			}
		}
	}
}

.comment{
	.comment_title{
		padding: 15rpx;
		.iconfont{
			color: red;
			font-size: 40rpx;
		}
		.comment_text{
			font-weight: 600;
			font-size: 34rpx;
			color: #666;
			margin-left: 10rpx;
		}
	}
	.comment_list{
		
		.comment_item{
			border-bottom: 15rpx solid #eee;
			.comment_user{
				display: flex;
				padding: 20rpx 0;
				.user_icon{
					width: 15%;
					display: flex;
					justify-content: center;
					align-items: center;
					image{
						width: 90%;
					}
				}
				
				.user_name{
					flex: 1;
					.user_nickname{
						color: #777;
					}
					.user_time{
						color: #ccc;
						font-size: 24rpx;
						padding: 5rpx;
					}
				}
				.user_badge{
					image{
						width: 40rpx;
						height: 40rpx;
					}
				}
			}
			.comment_desc{
				display: flex;
				padding: 30rpx 0;
				.comment_content{
					flex: 1;
					padding-left: 15%;
					color: #000;
				}
				.comment_like{
					text-align: right;
					.icondianzan{
						
					}
				}
			}
		}
	}
}

.download{
	height: 120rpx;
	display: flex;
	align-items: center;
	justify-content: center;
	.download_btn{
		width: 90%;
		height: 80%;
		background-color: #00FFFF;
		color: #fff;
		font-size: 50rpx;
		font-weight: 600;
		display: flex;
		justify-content: center;
		align-items: center;
	}
}

</style>

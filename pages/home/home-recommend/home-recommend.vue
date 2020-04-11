<template>
	<scroll-view @scrolltolower="handleToLower" class="recommend_view" scroll-y v-if="recommends.length>0">
		
			<view class="recommend_wrap">
				<navigator
				class="recommend_item"
				v-for="item in recommends"
				:key="item.id"
				:url="`/pages/album/album?id=${item.target}`"
				>
					<image mode="widthFix" :src="item.thumb"></image>
				</navigator>
			</view>
			
			<view class="month_wrap">
				<view class="month_title">
					<view class="month_title_info">
						<view class="month_info">
							<text>{{months.DD}}/</text>
							{{months.MM}} 月
						</view>
						<view class="month_text">
							{{months.title}}
						</view>
					</view>
					<view class="month_title_more">更多＞</view>
				</view>
				<view class="month_content">
					<view class="month_item"
					v-for="(item,index) in months.items"
					:key="item.id"
					>
					<go-detail :list="months.items" :index="index">
					<image  
					class="image_con" 
					mode="widthFix" 
					:src="item.thumb+item.rule.replace('$<Height>',360)"
					></image>
					</go-detail>
					</view>
				</view>
			</view>
			 
			<view class="hots_wrap">
				<view class="hots_title">
					<text>热门</text>
				</view>
				<view class="hot_content">
					<view class="hot_item" 
					v-for="(item,index) in hots"
					:key="item.id"
					>
					<go-detail :list="hots" :index="index">
					<image mode="widthFix" :src="item.thumb"></image>
					</go-detail>
					</view>
				</view>
			</view>
	</scroll-view>
</template>

<script>
	import moment from "moment";
	import goDetail from "@/components/goDetail.vue";
	export default {
		components:{
			goDetail
		},
		data(){
			return{
				recommends:[],
				months:{},
				hots:[],
				params:{
					limit:30,
					order:"hot",
					skip:0
				},
				hasMore:true
			};
		},
		mounted(){
		uni.setNavigationBarTitle({
					title:"推荐"
				})
	    this.getList();
		},
		methods:{
			getList(){
				this.request({
					url:"http://157.122.54.189:9088/image/v3/homepage/vertical",
					data:this.params
				})
				.then(result=>{
					console.log(result);
					if(result.res.vertical.length===0)
					{
						this.hasMore=false;
						uni.showToast({
							title:"没有更多了",
							icon:"none"
						});
						return; 
					}
					if(this.recommends.length==0){
						this.recommends=result.res.homepage[1].items;
						this.months=result.res.homepage[2];
						this.months.MM=moment(this.months.stime).format("MM");
						this.months.DD=moment(this.months.stime).format("DD");
					}

					/* 数组拼接es6 */
					this.hots=[...this.hots,...result.res.vertical];
				});
			},
			handleToLower(){
				console.log("触底");
				/*
				1.修改参数skip+=limit; 
				2.请求新的
				3.请求成功，hots数据叠加
				*/
			   if(this.hasMore){
				   this.params.skip+=this.params.limit;
				   this.getList();
			   }else{
				   uni.showToast({
				   	title:"没有数据了",
					icon:"none"
				   })
			   }	
			}
		}
	}
</script>

<style lang="scss" scoped>
.recommend_view{
	height: calc(100vh - 36px);
}
.recommend_wrap{
	display: flex;
	flex-wrap: wrap;
	justify-content: center;
}
.recommend_item{	
		width:45%;
		border: 5rpx solid #FFFFFF;
		display: flex;
	}

.month_wrap{
	.month_title{
		display: flex;
		justify-content: space-between;
		padding: 20rpx;
		.month_title_info{
		color:#d52a7e;
		font-size: 30rpx;
		font-weight: 600;
		display: flex;
			.month_info{
				text{
					font-size: 36rpx;
					
				}
			}
			.month_text{
				font-size: 34rpx;
				color: #666;
				margin-left: 30rpx;
			}
		}
		.month_title_more{
			font-size: 24rpx;
			color: #d52a7e;
		}
	}
	
}

.month_content{
		display: flex;
		flex-wrap: wrap;
		
		.month_item{
			width: 30%;
			border: 5rpx solid #FFFFFF;
			image{
				width: 100%;
			}
					}
		}

.hots_wrap{
	.hots_title{
		padding: 20rpx;
		text{
			border-left: 20rpx solid #d52a7e;
			font-size: 34rpx;
			font-weight: 600;
		}
	}
	
	.hot_content{
		display: flex;
		flex-wrap: wrap;
		justify-content: center;
		.hot_item{
			width: 30%;
			border: 5rpx solid #FFFFFF;
			/* display: flex; */
			image{	
				width: 100%;
			}
		}
	}
}

</style>

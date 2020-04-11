<template>
	<view
	@touchstart="handleTouchstart"
	@touchend="handleTouchend"
	>
		学习触屏事件
	</view>
</template>

<script>
/* 
 1.给容器绑定触屏事件
 2.用户按下屏幕事件  
     记录按下的时间，Data.now时间戳，1970-1-1到现在毫秒数
	 记录按下的坐标
3.用户离开屏幕时间
	记录离开的时间，Data.now
	记录离开的坐标
	根据两个时间运算判断用户按下屏幕的时长是否合法
	根据两对坐标判断距离是否合法判断滑动方向
 */
	export default {
		data(){
			return{
				startTime:0,
				startX:0,
				startY:0
			}
		},
		methods:{
			handleTouchstart(envent){
				console.log("手指按下");
				console.log("按下："+envent.changedTouches[0].clientX);
				console.log("按下："+envent.changedTouches[0].clientY);
				this.startTime=Date.now()
				this.startX=envent.changedTouches[0].clientX;
				this.startY=envent.changedTouches[0].clientY;
			},
			handleTouchend(envent){
				console.log("手指离开");
				console.log("离开："+envent.changedTouches[0].clientX);
				console.log("离开："+envent.changedTouches[0].clientY);
				const endTime=Date.now();
				const endX=envent.changedTouches[0].clientX;
				const endY=envent.changedTouches[0].clientY;
				//判断时长
				if(endTime-this.startTime >2000){
					return;
				}
				//方向
				let direction="";
				//判断距离合法否，判断方向
				if(Math.abs(endX-this.startX)>10){
				direction=endX-this.startX>0?"right":"left";	
				}else{
					return;
				}
				console.log(direction);
			}
		}
	}
</script>

<style>
view{
	width: 100%;
	height: 500rpx;
	background-color: aqua;
}
</style>

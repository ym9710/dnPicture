<template>
	<view
	@touchstart="handleTouchstart"
	@touchend="handleTouchend"
	>
		<slot></slot>
	</view>
</template>

<script>
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
				if(Math.abs(endX-this.startX)>10&&Math.abs(endY-this.startY)<10){
				direction=endX-this.startX>0?"right":"left";	
				}else{
					return;
				}
				console.log(direction);
				this.$emit("swiperAction",{direction});
			}
		}
	}
</script>

<style>

</style>

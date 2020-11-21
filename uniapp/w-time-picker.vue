<template>
	<view class="w-time-picker" :class="{'show':showTimePicker}">
		<view class="top-picker">
			<view class="w-time-days">
				<view class="w-time-day" :style="{'color':tabIndex==index?theme:'#333','border-color':tabIndex==index?theme:'#ddd'}" v-for="(day,index) in dayList" :key="index" @tap="toggleIndex(day,index)">
					<view class="w-time-week">{{day.week}}</view>
					<view class="w-time-date">{{day.month}}/{{day.day}}</view>
				</view>
			</view>
			<view class="w-time-body">
				<view class="w-time-list">
					<view class="w-time-item" :style="{'color':itemIndex==index?theme:'#333','border-color':itemIndex==index?theme:'#ddd'}" :class="{'w-time-item-active':!item.disabled}" v-for="(item,index) in timeList" :key="index" @tap="toggleItem(item,index)">{{item.label}}</view>
				</view>
			</view>
		</view>
		<view class="w-time-footer">
			<view class="w-time-cancel w-time-btn" @tap="hide">取消</view>
			<view class="w-time-sure w-time-btn" :style="{'background-color':theme}" @tap="submit">确定</view>
		</view>
	</view>
</template>

<script>
	let _this=null;
	export default {
		props:{
			afterDays:{
				type:[String,Number],
				default:7
			},
			startHour:{
				type:[String,Number],
				default:8
			},
			endHour:{
				type:[String,Number],
				default:18
			},
			step:{
				type:[String,Number],
				default:60
			},
			afterHours:{
				type:[String,Number],
				default:2
			},
			theme:{
				type:String,
				default:"#FCCE78"
			}
		},
		data() {
			return {
				dayList:[],
				timeList:[],
				tabIndex:0,
				itemIndex:-1,
				showTimePicker:false,
				time:""
			}
		},
		created(){
			_this=this;
			_this.initPicker();
			_this.initHours();
		},
		methods:{
			hide(){
				_this.showTimePicker=false;
				_this.$emit("cancel");
			},
			show(){
				_this.showTimePicker=true;
			},
			submit(){
				if(_this.time!=""){
					let tabItem=_this.dayList[_this.tabIndex];
					let result=tabItem.year+"-"+tabItem.month+"-"+tabItem.day+" "+_this.time.label;
					_this.$emit("confirm",result);
					_this.showTimePicker=false;
				}
			},
			toggleIndex(item,index){
				_this.tabIndex=index;
				_this.itemIndex=-1;
				_this.initHours(!item.isToday);
			},
			toggleItem(item,index){
				if(!item.disabled){
					_this.itemIndex=index;
					_this.time=item;
				}
			},
			forMatNumber(n){
				return n<10?'0'+n:n
			},
			initHours(flag){
				let aDate=new Date();
				let curHour=aDate.getHours();
				_this.timeList=[];
				for(let j=_this.startHour*1;j<_this.endHour*1;j++){
					if(j%2 === 0){
						if(flag){
							_this.timeList.push({
								label:_this.forMatNumber(j)+":00"+"-"+_this.forMatNumber(j+2)+":00",
								disabled:false
							});
						}else{
							_this.timeList.push({
								label:_this.forMatNumber(j)+":00"+"-"+_this.forMatNumber(j+2)+":00",
								disabled:curHour+_this.afterHours<j?false:true
							});
						}
					}
					// for(let k=0;k<60;k+=_this.step){
					// 	console.log(_this.forMatNumber(j),j,k,'jjjj')
					// 	if(k != 30){
					// 		if(flag){
					// 			_this.timeList.push({
					// 				label:_this.forMatNumber(j)+":"+_this.forMatNumber(k)+"-"_this.forMatNumber(j)+":"+_this.forMatNumber(k),
					// 				disabled:false
					// 			});
					// 		}else{
					// 			_this.timeList.push({
					// 				label:_this.forMatNumber(j)+":"+_this.forMatNumber(k),
					// 				disabled:curHour+_this.afterHours<j?false:true
					// 			});
					// 		}
					// 	}
					// }
				}
			},
			initPicker(){
				let aDate=new Date();
				let weekList=["周日","周一","周二","周三","周四","周五","周六"];
				_this.dayList.push({
					year:aDate.getFullYear(),
					month:_this.forMatNumber(aDate.getMonth()+1),
					day:_this.forMatNumber(aDate.getDate()),
					week:weekList[aDate.getDay()],
					isToday:true
				})
				for(let i=1;i<_this.afterDays*1;i++){
					aDate.setDate(aDate.getDate()+1);
					_this.dayList.push({
						year:aDate.getFullYear(),
						month:_this.forMatNumber(aDate.getMonth()+1),
						day:_this.forMatNumber(aDate.getDate()),
						week:weekList[aDate.getDay()],
						isToday:false
					})
				};
			}
		}
	}
</script>

<style lang="scss">
	.w-time-picker.show{
		transform: translate3d(0,0,0);
	}
	.top-picker{
		display: flex;
	}
	.w-time-picker{
		position: fixed;
		left:0;
		bottom:0;
		width: 100%;
		height: 65%;
		z-index: 9999;
		background-color:#f5f5f5;
		display: flex;
		flex-direction: column;
		transform: translate3d(100%,0,0);
		transition: all 0.3s ease;
		.w-time-days{
			overflow: hidden;
			padding-top:20upx;
			background-color: #fff;
			.w-time-day{
				width: 220upx;
				text-align: center;
				border:solid 1px #ddd;
				margin:20upx 20upx;
				border-radius: 6upx;
				padding:10upx 0;
				color:#333;
				.w-time-week{
					font-size: 28upx;
					line-height: 1;
				}
				.w-time-date{
					font-size: 24upx;
					line-height: 1;
					margin-top: 10upx;
				}
			}
			.w-time-day-active{
				color:#f00;
				border-color:#f00;
			}
		}
		.w-time-body{
			flex:1;
			overflow: hidden;
			padding-top: 20upx;
			.w-time-list-scroll{
				height: 100%;
			}
			.w-time-list{
				display: flex;
				flex-wrap: wrap;
				padding:20upx 10upx;
				justify-content: center;
			}
			.w-time-item{
				width: 360upx;
				height: 64upx;
				line-height: 64upx;
				margin:0 10upx 20upx;
				text-align: center;
				border:solid 1px #ddd;
				border-radius: 6upx;
				font-size: 28upx;
				transition: all 0.3s ease;
			}
			.w-time-item-active{
				background-color: #fff;
			}
		}
		.w-time-footer{
			height: 88upx;
			display: flex;
			flex: 1;
			background-color: #fff;
			.w-time-btn{
				flex:1;
				text-align: center;
				line-height: 88upx;
				font-size: 30upx;
			}
			.w-time-sure{
				background-color: #f00;
				color:#fff;
			}
		}
	}
</style>

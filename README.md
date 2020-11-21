＃时间选择器
#两个版本
#具体用法（props可以看情况增减）
##h5    <new-time ref="picker" @confirm="onConfirm" :afterDays="7" :step="30"></new-time>
##打开方法：open(){
       this.$refs.picker.show();
    },
##点击事件：onConfirm(e){
        console.log(e,'时间')
    },

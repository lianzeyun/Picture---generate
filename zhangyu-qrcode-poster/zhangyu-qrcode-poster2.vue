<template>
	<view class="content" v-if="isShow" @click.stop="isShow=false">
		<canvas @click.stop="" :style="{ width: canvasW + 'px', height: canvasH + 'px' }" canvas-id="my-canvas"></canvas>
		<view class="save-btn" @click.stop="saveImage">保存图片</view>
	</view>
</template>

<script>
	export default{
		props:{
			banner:{
				type: String,
				default: 'https://oss.zhangyubk.com/%E8%8D%89%E8%8E%93%E5%8D%83%E5%B1%82.png'
			},
			headerImg:{
				type: String,
				default: 'https://oss.zhangyubk.com/%E8%8D%89%E8%8E%93%E5%8D%83%E5%B1%82.png'
			},
			title:{
				type: String,
				default: '莫小白'
			},
			subTitle:{
				type: String,
				default: '北京慧聪云信大数据科技有限公阿三大苏打实打实大苏打实打实的司'
			},
			phone:{
				type: String,
				default: '17600121378'
			},
			abImg:{
				type: String,
				default: 'http://style.iambuyer.com/img/temp-steel-imgs/temp-steel-imgs-banner-01.png'
			}
		},
		data(){
			return {
				canvasW: 0,
				canvasH: 0,
				ctx: null,
				isShow: false,
				qrcode: 'https://oss.zhangyubk.com/cmqrcode.jpg'
			}
		},
		methods:{
			//显示
			showCanvas(){
				this.isShow = true
				this.__init()
			},
			//初始化画布
			async __init(){
				uni.showLoading({
				    title: '加载中...',
					mask: true
				})
				this.ctx = uni.createCanvasContext('my-canvas',this)
				this.canvasW = uni.upx2px(550);
				this.canvasH = uni.upx2px(830);
				//设置画布背景透明
				this.ctx.setFillStyle('rgba(255, 255, 255, 0)')
				//设置画布大小
				this.ctx.fillRect(0,0,this.canvasW,this.canvasH)
				//绘制圆角背景
				this.drawRoundRect(this.ctx, 0, 0, this.canvasW, this.canvasH,uni.upx2px(18),'#FFFFFF')
				//获取标题图片
				let banner = await this.getImageInfo(this.banner)
				let headerImg = await this.getImageInfo(this.headerImg)
				let hW = uni.upx2px(500);
				let hH = uni.upx2px(320);
				let hWs = uni.upx2px(100);
				let hHs = uni.upx2px(100);
				let hwws = uni.upx2px(300);
				//绘制标题图
				this.drawRoundImg(this.ctx,banner.path,(uni.upx2px(0)),(uni.upx2px(0)),uni.upx2px(550),uni.upx2px(600),10)
				//绘制头像
				this.drawRoundImg(this.ctx,headerImg.path,((uni.upx2px(80)) / 2),((uni.upx2px(1100)) / 2),hWs,hHs,26)
				//绘制标题
				this.ctx.setFontSize(20); //设置标题字体大小
				this.ctx.setFillStyle('#2E2E30'); //设置标题文本颜色
				this.ctx.fillText(this.title,((uni.upx2px(80)) / 2),(((uni.upx2px(650)) / 2) + hH + 30))
				//绘制公司
				this.ctx.setFontSize(10);
				this.ctx.setFillStyle('#5D5D5D');
				let sWidth = this.ctx.measureText(this.subTitle).width
				if(sWidth > hwws){
					this.ctx.fillText(this.subTitle.slice(0,16) + '...',((uni.upx2px(80)) / 2),(((uni.upx2px(640)) / 2) + hH + 55))
				}else{
					this.ctx.fillText(this.subTitle,((uni.upx2px(80)) / 2),(((uni.upx2px(640)) / 2) + hH + 55))
				}
				//绘制电话
				this.ctx.setFontSize(10);
				this.ctx.setFillStyle('#5D5D5D');
				this.ctx.fillText(this.phone,((uni.upx2px(80)) / 2),(((uni.upx2px(710)) / 2) + hH + 55))
				//小程序码
				let abImg = await this.getImageInfo(this.abImg)
				// this.ctx.drawImage(abImg.path,uni.upx2px(384),(((this.canvasW-hW) / 2) + hH + uni.upx2px(264)), uni.upx2px(156), uni.upx2px(156))
				this.drawRoundImg(this.ctx,abImg.path,((uni.upx2px(850)) / 2),((uni.upx2px(1380)) / 2),hWs,hHs,26)
				//延迟渲染
				setTimeout(()=>{
					this.ctx.draw(true,()=>{
						uni.hideLoading()
					})
				},500)
			},
			//绘制实心圆
			drawEmptyRound(ctx,x,y,radius){
				ctx.save()
				ctx.beginPath();
				ctx.arc(x, y, radius, 0, 2 * Math.PI,true);
				ctx.setFillStyle('rgba(0, 0, 0, .4)')
				ctx.fill();
				ctx.restore()
				ctx.closePath()
			},
			//绘制虚线
			drawDashLine(ctx,x1,y1,x2,y2,dashLength){
				ctx.setStrokeStyle("#cccccc")//设置线条的颜色
				ctx.setLineWidth(1)//设置线条宽度
				var dashLen = dashLength,
				xpos = x2 - x1, //得到横向的宽度;
				ypos = y2 - y1, //得到纵向的高度;
				numDashes = Math.floor(Math.sqrt(xpos * xpos + ypos * ypos) / dashLen); 
				//利用正切获取斜边的长度除以虚线长度，得到要分为多少段;
				for(var i=0; i<numDashes; i++){
				 if(i % 2 === 0){
					 ctx.moveTo(x1 + (xpos/numDashes) * i, y1 + (ypos/numDashes) * i); 
					 //有了横向宽度和多少段，得出每一段是多长，起点 + 每段长度 * i = 要绘制的起点；
				  }else{
					  ctx.lineTo(x1 + (xpos/numDashes) * i, y1 + (ypos/numDashes) * i);
				  }
				}
				ctx.stroke();
			},
			//带圆角图片
			drawRoundImg(ctx, img, x, y, width, height, radius){
				ctx.beginPath()
				ctx.save()
				// 左上角
				ctx.arc(x + radius, y + radius, radius, Math.PI, Math.PI * 1.5)
				// 右上角
				ctx.arc(x + width - radius, y + radius, radius, Math.PI * 1.5, Math.PI * 2)
				// 右下角
				ctx.arc(x + width - radius, y + height - radius, radius, 0, Math.PI * 0.5)
				// 左下角
				ctx.arc(x + radius, y + height - radius, radius, Math.PI * 0.5, Math.PI)
				ctx.stroke()
				ctx.clip()
				ctx.drawImage(img, x, y, width, height);
				ctx.restore()
				ctx.closePath()
			},
			//圆角矩形
			drawRoundRect(ctx, x, y, width, height, radius, color){
				ctx.save();
				ctx.beginPath();
				ctx.setFillStyle(color); 
				ctx.setStrokeStyle(color)
				ctx.setLineJoin('round');  //交点设置成圆角
				ctx.setLineWidth(radius);
				ctx.strokeRect(x + radius/2, y + radius/2, width - radius , height - radius );
				ctx.fillRect(x + radius, y + radius, width - radius * 2, height - radius * 2);
				ctx.stroke();
				ctx.closePath();
			},
			//获取图片
			getImageInfo(imgSrc){
				return new Promise((resolve, reject) => {
					uni.getImageInfo({
						src: imgSrc,
						success: (image) => {
							resolve(image);
							console.log('获取图片成功',image)
						},
						fail: (err) => {
							reject(err);
							console.log('获取图片失败',err)
						}
					});
				});
			},
			//保存图片到相册
			saveImage(){
				//判断用户授权
				uni.getSetting({
				   success(res) {
				      console.log('获取用户权限',res.authSetting)
					  if(Object.keys(res.authSetting).length>0){
						  //判断是否有相册权限
						  if(res.authSetting['scope.writePhotosAlbum']==undefined){
							  //打开设置权限
							  uni.openSetting({
							    success(res) {
							      console.log('设置权限',res.authSetting)
							    }
							  })
						  }else{
							  if(!res.authSetting['scope.writePhotosAlbum']){
								  //打开设置权限
								  uni.openSetting({
								    success(res) {
								      console.log('设置权限',res.authSetting)
								    }
								  })
							  }
						  }
					  }else{
						  return
					  }
				   }
				})
				var that = this
				uni.canvasToTempFilePath({
					canvasId: 'my-canvas',
					quality: 1,
					complete: (res) => {
						console.log('保存到相册',res);
						uni.saveImageToPhotosAlbum({
							filePath: res.tempFilePath,
							success(res) {
								uni.showToast({
									title: '已保存到相册',
									icon: 'success',
									duration: 2000
								})
								setTimeout(()=>{
									that.isShow = false
								},2000)
							}
						})
					}
				},this);
			}
		}
	}
</script>

<style scoped lang="scss">
.content{
	position: fixed;
	top: 0;
	left: 0;
	right: 0;
	bottom: 0;
	background: rgba(0,0,0,.4);
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	z-index: 999999;
	.save-btn{
		margin-top: 35rpx;
		color: #FFFFFF;
		background: #02C2A2;
		padding: 15rpx 40rpx;
		border-radius: 50rpx;
	}
}
</style>

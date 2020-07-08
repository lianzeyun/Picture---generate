<template>
	<view>
		<view class="card">
			<Card :card_type="card_type" :bgColor="bgColor" :background="background" :textColor="textColor" :content="content"></Card>
			<view class="card-soma">
				<image src="../../static/anliku.png" class="card-soma-ma"></image>
				<view class="card-soma-changan">
					<image src="../../static/changan.png"></image>
					<view>扫描或长按识别 收下名片</view>
				</view>
			</view>
		</view>
		<view class="mbutton" @click="sharePoster()"><Mbutton :text="'保存到手机'"></Mbutton></view>
		<QrcodePoster 
		ref="poster"
		:headerImg="content.avatar"
		:title="content.name"
		:position="content.job"
		:subTitle="content.company_name"
		:phone="content.telephone"
		:mailbox="content.email"
		></QrcodePoster>
	</view>
</template>

<script>
import { getHomeList } from 'pages/home/api/communal.js';
import QrcodePoster from '../../../../components/zhangyu-qrcode-poster/zhangyu-qrcode-poster.vue';
export default {
	name: 'sendCard',
	components: {
		QrcodePoster
	},
	data() {
		return {
			card_type: '',
			bg_color: '',
			background: '',
			text_color: '',
			content: {},
			is_show_model: false
		};
	},
	onLoad() {
		this.getUserCard(getHomeList);
	},
	methods: {
		getUserCard() {
			let userid = getApp().globalData.userInfo.user_id;
			getHomeList({ to_uid: userid, user_id: userid }).then(res => {
				this.card_type = res.data.card_type;
				this.bg_color = res.data.bg_color;
				this.text_color = res.data.text_color;
				this.background = res.data.background;
				this.content = {
					avatar: res.data.avatar,
					name: res.data.name,
					job: res.data.job,
					telephone: res.data.telephone,
					email: res.data.email,
					company_name: res.data.company_name,
					address: res.data.address
				};
				console.log(this.content)
			});
			
		},
		//分享海报
		sharePoster() {
			//获取带参数二维码并传递
			this.is_show_model = false;
			this.$refs.poster.showCanvas('http://style.iambuyer.com/img/temp-steel-imgs/temp-steel-imgs-banner-01.png');
		}
	}
};
</script>

<style lang="scss" scoped>
.mbutton {
	margin-top: 60rpx;
}
.card {
	margin: 20rpx;
	background-color: #ffffff;
	padding: 30rpx;
	border-radius: 8rpx;
	box-shadow: 0px 0px 24rpx 0px rgba(0, 0, 0, 0.1);
	&-soma {
		margin-top: 50rpx;
		border-top: 1px solid #f5f5f5;
		padding: 50rpx 0;
		display: flex;
		justify-content: space-around;
		align-items: center;
		width: 100%;
		&-ma {
			width: 172rpx;
			height: 172rpx;
		}
		&-changan {
			text-align: center;
			image {
				width: 124rpx;
				height: 124rpx;
			}
			view {
				color: #9b9b9b;
				font-size: 24rpx;
			}
		}
	}
}
</style>

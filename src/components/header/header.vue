<template>
	<div class="header">
		<div class="content-wrapper">
			<div class="avatar">
				<img :src="seller.avatar" width="64" height="64">
			</div>
			<div class="content">
				<div class="title">
					<span class="brand"></span>
					<span class="name">{{ seller.name }}</span>
				</div>
				<div class="description">
					{{seller.description}}/{{seller.deliveryTime}}分钟送达
				</div>
				<div v-if="seller.supports" class="support">
					<span class="icon" :class="classMap[seller.supports[0].type]"></span>
					<span class="text">{{seller.supports[0].description}}</span>
				</div>
			</div>
			<div class="support-count" v-if="seller.supports" @click="showDetail">
				<span class="count">{{seller.supports.length}}个</span>
				<i class="icon-keyboard_arrow_right"></i>
			</div>
		</div>
		<div class="bulletin-wrapper" @click="showDetail">
			<span class="bulletin-title"></span>
			<span class="bulletin-text">{{seller.bulletin}}</span>
			<i class="icon-keyboard_arrow_right"></i>
		</div>
		<div class="background">
			<img :src="seller.avatar" width="100%" height="100%">
		</div>

		<!--过渡-->
		<transition name="fade">
			<!--弹出阴影层-->
			<div class="detail" v-show="detailShow">
				<div class="detail-wrapper clearfix">
					<div class="detail-main">
						<!--商家-->
						<div class="name">{{seller.name}}</div>
						<!--星星评分-->
						<star :size="48" :score="seller.score" class="star"></star>
						<!--优惠信息-->
						<div class="title">
							<div class="line"></div>
							<div class="text">优惠信息</div>
							<div class="line"></div>
						</div>
						<!--具体优惠-->
						<ul v-if="seller.supports" class="support">
							<li v-for="(item,index) in seller.supports" class="support-item">
								<span class="icon" :class="classMap[item.type]"></span>
								<span class="text">{{item.description}}</span>
							</li>
						</ul>
						<!--商家公告-->
						<div class="title">
							<div class="line"></div>
							<div class="text">商家公告</div>
							<div class="line"></div>
						</div>
						<!--具体公告-->
						<div class="bulletin">
							<p class="text">{{seller.bulletin}}</p>
						</div>
					</div>
				</div>
				<div class="detail-close" @click="detailShow = !detailShow">
					<i class="icon-close"></i>
				</div>
			</div>
		</transition>
	</div>
</template>

<script type="text/javascript">
	import star from 'components/star/star'

	export default {
		props: {
			seller: {
			}
		},
		data() {
			return {
				detailShow: false
			}
		},
		methods: {
			showDetail() {
				this.detailShow = true
			}
		},
		created() {
			this.classMap = ['decrease','discount','special','invoice','guarantee']
		},
		components: {
			star
		}
	}
</script>

<style lang="stylus">
	@import '../../common/stylus/mixin'
	
	.header
		position: relative
		color: #fff
		background: rgba(7,17,27,0.5)
		overflow: hidden
		.content-wrapper
			padding: 24px 12px 18px 24px
			font-size: 0
			position: relative
			.avatar
				display: inline-block
				vertical-align: top
				img
					border-radius: 2px
			.content
				display: inline-block
				margin-left: 16px
				.title
					margin: 2px 0 8px 0
					.brand
						width: 30px
						height: 18px
						display: inline-block
						margin-right: 6px
						vertical-align: top
						bg-image: ('img/brand')
						background-repeat: no-repeat
						background-size: 30px 18px
					.name
						font-size: 16px
						font-weight: bold
						line-height: 18px
				.description
					margin-bottom: 10px
					font-size: 12px
					line-height: 12px
				.support
					margin-bottom: 2px
					.icon
						display: inline-block
						vertical-align: top
						width: 12px
						height: 12px
						margin-right: 4px
						background-size: 12px 12px
						background-repeat: no-repeat
						&.decrease
							bg-image('img/decrease_1')
						&.discount
							bg-image('img/discount_1')
						&.guarantee
							bg-image('img/guarantee_1')
						&.invoice
							bg-image('img/invoice_1')
						&.special
							bg-image('img/special_1')
					.text
						font-size: 10px
						line-height: 12px
			.support-count
				font-size: 10px
				line-height: 12px
				position: absolute
				right: 12px
				bottom: 18px
				height: 12px
				background-color: rgba(0,0,0,0.2)
				padding: 7px 8px
				border-radius: 14px
		.bulletin-wrapper
			position: relative
			height: 28px
			line-height: 28px
			padding: 0 22px 0 12px
			background-color: rgba(7,17,27,0.2)
			white-space: nowrap
			overflow: hidden
			text-overflow: ellipsis
			.bulletin-title
				width: 22px
				height: 12px
				display: inline-block
				vertical-align: top
				margin-top: 9px
				bg-image: ('img/bulletin')
				background-repeat: no-repeat
				background-size: 22px 12px
			.bulletin-text
				font-size: 10px
			.icon-keyboard_arrow_right
				position: absolute
				right: 12px
				bottom: 5px
		.background
			position: absolute
			left: 0
			top: 0
			width: 100%
			height: 100%
			z-index: -1
			filter: blur(10px)
		.fade-enter-active, .fade-leave-active {
		  transition: opacity .8s
		}
		.fade-enter, .fade-leave-active {
		  opacity: 0
		}
		.detail
			position: fixed
			z-index: 100
			top: 0
			left: 0
			width: 100%
			height: 100%
			overflow: auto
			background: rgba(7,17,27,0.9)
			//只支持IOS
			backdrop-filter: blur(10px)
			.detail-wrapper
				min-height: 100%
				width: 100%
				.detail-main
					margin-top: 64px
					padding-bottom: 32px
					.name
						font-size: 16px
						font-weight: 700
						line-height: 16px
						text-align: center
					.star
						text-align: center
						margin-top: 16px
					.title
						display: flex
						width: 80%
						margin: 28px auto 24px
						.line
							flex: 1
							position: relative
							top: -7px
							border-bottom: 1px solid rgba(255,255,255,0.2)
						.text
							font-size: 14px
							font-weight: 700
							padding: 0 12px
					.support
						display: block
						width: 80%
						margin: 0 auto
						.support-item
							margin: 0 12px 12px
							&:last-child
								margin-bottom: 0
							.icon
								width: 16px
								height: 16px
								display: inline-block
								padding-right: 6px
								vertical-align: top
								background-repeat: no-repeat
								background-size: 16px 16px
								&.decrease
									bg-image('img/decrease_2')
								&.discount
									bg-image('img/discount_2')
								&.guarantee
									bg-image('img/guarantee_2')
								&.invoice
									bg-image('img/invoice_2')
								&.special
									bg-image('img/special_2')
							.text
								font-size: 12px
								line-height: 12px
					.bulletin
						width: 80%
						margin: 0 auto
						.text
							padding: 0 12px
							font-size: 12px
							line-height: 24px
							font-weight: 200
			.detail-close
				display: inline-block
				position: relative
				left: 50% - 8px
				bottom: 20px
				font-size: 32px

</style>
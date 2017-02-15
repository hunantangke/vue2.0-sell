<template>
	<div class="seller" ref="seller">
		<div class="seller-content">
			<div class="overview">
				<div class="seller-header">
					<div class="name">{{seller.name}}</div>
					<star class="star" :size="36" :score="seller.score"></star>
					<div class="ratingcount">({{seller.ratingCount}})</div>
					<div class="sellcount">月售{{seller.sellCount}}单</div>
				</div>
				<div class="seller-middle">
					<div class="same">
						<div class="word">起送价</div>
						<div class="price">{{seller.minPrice}}</div>
						<div class="yuan">元</div>
					</div>
					<div class="same">
						<div class="word">商家配送</div>
						<div class="price">{{seller.deliveryPrice}}</div>
						<div class="yuan">元</div>
					</div>
					<div class="same">
						<div class="word">平均配送时间</div>
						<div class="price">{{seller.deliveryTime}}</div>
						<div class="yuan">分钟</div>
					</div>
				</div>
				<div class="favorite" @click="toggleFavorite">
					<div class="icon-favorite" :class="{'active':favorite}"></div>
					<div class="text">{{favoriteText}}</div>
				</div>
			</div>
			<div class="line"></div>
			<div class="and">
				<div class="bulletin">
					<div class="title">公告与活动</div>
					<div class="text">{{seller.bulletin}}</div>
				</div>
				<ul class="supports">
					<li v-for="item in seller.supports" class="supports-item">
						<div class="type" :class="classMap[item.type]"></div>
						<div class="description">{{item.description}}</div>
					</li>
				</ul>
			</div>
			<div class="line"></div>
			<div class="scene">
				<div class="title">商家实景</div>
				<div class="pics-wrapper" ref="picswrapper">
					<ul class="pics" ref="pics">
						<li class="item" v-for="item in seller.pics">
							<img :src="item" width="120px" height="90px">
						</li>
					</ul>
				</div>
			</div>
			<div class="line"></div>
			<div class="infos">
				<div class="title">商家信息</div>
				<ul>
					<li class="infos-item" v-for="item in seller.infos">{{item}}</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script type="text/javascript">
	import star from 'components/star/star'
	import BScroll from 'better-scroll'
	import {saveToLocal,loadFromLocal} from 'common/js/store'

	export default {
		props: {
			seller: {}
		},
		data() {
			return {
				favorite: (() => {
        			return loadFromLocal(this.seller.id, 'favorite', false);
        		})()
			}
		},
		components: {
			star
		},
		created() {
			this.classMap = ['decrease','discount','special','invoice','guarantee']
		},
		mounted() {
			this.$nextTick(() => {
				this._initScroll(),
				this._picsScroll()
			})
		},
		computed: {
			favoriteText() {
				return this.favorite ? '已收藏' : '收藏'
			}
		},
		watch: {
			seller() {
				this.$nextTick(function() {
					this._initScroll(),
					this._picsScroll()
				})
			}
		},
		methods: {
			_initScroll() {
				if (!this.scroll) {
					this.scroll = new BScroll(this.$refs.seller,{
						click: true
					})
				} else {
					this.scroll.refresh()
				}
			},
			//横向滑动better-scroll
			_picsScroll() {
				if (this.seller.pics) {
					let picwidth = 120
					let margin = 6
					let width = (picwidth + margin) * this.seller.pics.length - margin
					this.$refs.pics.style.width = width + 'px'
					this.$nextTick(() => {
						if (!this.picscroll) {
							this.picscroll = new BScroll(this.$refs.picswrapper,{
								scrollX: true,
								eventPassthroug: 'vertical'
							})
						} else {
							this.picscroll.refresh()
						}
					})
				}
			},
			toggleFavorite(event) {
				if (!event._constructed) {
				  return
				}
				return this.favorite = !this.favorite
				saveToLocal(this.seller.id, 'favorite', this.favorite)
			}
		}
	}
</script>

<style lang="stylus">
	@import '../../common/stylus/mixin.styl'

	.seller
		position: absolute
		top: 174px
		bottom: 0
		left: 0
		width: 100%
		overflow: hidden
		.overview
			padding: 18px 18px 0 18px
			border-1px(rgba(7,17,27,0.1))
		.seller-header
			border-1px(rgba(7,17,27,0.1))
			.name
				font-size: 14px
				color: rgb(7,17,27)
				line-height: 14px
			.star
				padding: 8px 0 18px
				display: inline-block
			.ratingcount,.sellcount
				display: inline-block
				font-size: 10px
				color: rgb(77,85,93)
				line-height: 18px
			.ratingcount
				padding: 0 10px 0 8px
		.seller-middle
			padding: 18px 0
			display: flex
			.same
				flex: 1
				display: inline-block
				text-align: center
				font-size: 0
				border-right: 1px solid rgba(7,17,27,0.1)
				&:last-child
					border-right: 0
				.word
					font-size: 10px
					color: rgb(147,153,159)
					line-height: 10px
					padding-bottom: 4px
				.price,.yuan
					display: inline-block
					font-size: 24px
					color: rgb(7,17,27)
					line-height: 24px
				.yuan
					font-size: 10px
		.favorite
			position: absolute
			top: 18px
			right: 10px
			width: 50px
			text-align: center
			.icon-favorite
				font-size: 24px
				line-height: 24px
				padding-bottom: 4px
				color: rgb(147,153,159)
				&.active
					color: rgb(240,20,20)
			.text
				font-size: 10px
				color: rgb(77,85,93)
				line-height: 10px
		.line
			width: 100%
			height: 16px
			background: #f3f5f7
			border-1px(rgba(7,17,27,0.1))
		.and
			padding: 18px
			border-1px(rgba(7,17,27,0.1))
			.bulletin
				.title
					font-size: 14px
					color: rgb(7,17,27)
					line-height: 14px
				.text
					font-size: 12px
					color: rgb(240,20,20)
					line-height: 24px
					padding: 8px 12px 16px
					border-1px(rgba(7,17,27,0.1))
			.supports
				.supports-item
					border-1px(rgba(7,17,27,0.1))
					padding: 16px 12px
					&:last-child
						border-1px(rgba(7,17,27,0))
						padding-bottom: 0
					.type
						display: inline-block
						width: 16px
						height: 16px
						padding-right: 6px
						vertical-align: top
						background-repeat: no-repeat
						background-size: 16px 16px
						&.decrease
							bg-image('img/decrease_4')
						&.discount
							bg-image('img/discount_4')
						&.guarantee
							bg-image('img/guarantee_4')
						&.invoice
							bg-image('img/invoice_4')
						&.special
							bg-image('img/special_4')
					.description
						display: inline-block
						font-size: 12px
						color: rgb(7,17,27)
						line-height: 16px
		.scene
			padding: 18px
			border-1px(rgba(7,17,27,0.1))
			.title
				font-size: 14px
				color: rgb(7,17,27)
				line-height: 14px
				padding-bottom: 12px
			.pics
				width: 100%
				white-space: nowrap
				overflow: hidden
				.item
					font-size: 0
					display: inline-block
					margin-right: 6px
					width: 120px
					height: 90px
					&:last-child
						margin-right: 0
		.infos
			padding: 18px
			.title
				font-size: 14px
				color: rgb(7,17,27)
				line-height: 14px
				padding-bottom: 12px
				border-1px(rgba(7,17,27,0.1))
			.infos-item
				font-size: 12px
				color: rgb(7,17,27)
				line-height: 16px
				padding: 16px 12px
				border-1px(rgba(7,17,27,0.1))
				&:last-child
					border-1px(rgba(7,17,27,0))				
</style>
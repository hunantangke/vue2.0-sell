<template>
	<div class="ratings" ref="ratings">
		<div class="ratings-centent">
			<div class="overview">
				<div class="overview-left">
					<div class="score">{{seller.score}}</div>
					<div class="title">综合评分</div>
					<div class="rankrate">高于周边商家{{seller.rankRate}}%</div>
				</div>
				<div class="overview-right">
					<div class="servicescore">
						<div class="title">服务态度</div>
						<star class="star" :size="36" :score="seller.serviceScore"></star>
						<div class="score">{{seller.serviceScore}}</div>
					</div>
					<div class="foodscore">
						<div class="title">美食评分</div>
						<star class="star" :size="36" :score="seller.foodScore"></star>
						<div class="score">{{seller.foodScore}}</div>
					</div>
					<div class="deliverytime">
						<div class="title">送达时间</div>
						<div class="time">{{seller.deliveryTime}}分钟</div>
					</div>
				</div>
			</div>
			<div class="line"></div>
			<ratingsmall :select-type="selectType" :only-content="onlyContent" :ratings="ratings"></ratingsmall>
			<div class="ratings-wrapper">
				<ul>
					<li v-for="rating in ratings" class="rating" v-show="needshow(rating.rateType,rating.text)">
						<img :src="rating.avatar" width="28px" height="28px" class="avatar">
						<div class="rating-right">
							<div class="name">{{rating.username}}</div>
							<div class="ratetime">{{rating.rateTime | formatDate}}</div>
							<star class="star" :size="24" :score="rating.score"></star>
							<div class="time" v-if="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</div>
							<div class="text" v-if="rating.text">{{rating.text}}</div>
							<div class="bottom">
								<div class="icon-thumb_down" v-if="rating.rateType===1"></div>
								<div class="icon-thumb_up" v-else></div>
								<ul v-if="rating.recommend" class="rating-ul">
									<li class="recommend" v-for="item in rating.recommend">{{item}}</li>
								</ul>
							</div>
						</div>
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script>
	import star from 'components/star/star'
	import ratingsmall from 'components/ratingsmall/ratingsmall'
	import {formatDate} from 'common/js/date'
	import BScroll from 'better-scroll'

	const alls = 2
	const ERR_ON = 0

	export default {
		props: {
			seller: {}
		},
		data() {
			return {
				ratings: [],
				selectType: {
					a: alls
				},
				onlyContent: {
					a: false
				},
			}
		},
		filters: {
			formatDate(time) {
				let date = new Date(time)
				return formatDate(date,'yyyy-MM-dd hh:mm')
			}
		},
		created() {
			//api请求ratings
			this.$http.get('/api/ratings').then((res) => {
			    res = res.body
			    if (res.errno === ERR_ON) {
			        this.ratings = res.data
			        //数据更新获取DOM
			        this.$nextTick(() => {
			        	this.scroll = new BScroll(this.$refs.ratings,{
			        		click: true
			        	})
			        })
			    }
			})
		},
		components: {
			star,
			ratingsmall
		},
		methods: {
			// show() {
			// 	this.showFlag = true
			// 	this.selectType.a = alls
			// 	this.onlyContent.a = false
			// },
			// back() {
			// 	this.showFlag = false
			// },
			// addcart(event) {
			// 	if(!event._constructed) {
			// 		return
			// 	}
			// 	//注册data没有的属性
			// 	Vue.set(this.food,'count',1)
			// },
			needshow(type,text) {
				if(this.onlyContent.a && !text) {
					//异步更新Bscroll
					this.$nextTick(() => {
						this.scroll.refresh()
					})
					return false
				}
				if(this.selectType.a === alls) {
					this.$nextTick(() => {
						this.scroll.refresh()
					})
					return true
				} else {
					this.$nextTick(() => {
						this.scroll.refresh()
					})
					return type === this.selectType.a
				}
			}
		}
	}
</script>

<style lang="stylus">
	@import '../../common/stylus/mixin.styl'

	.ratings
		position: absolute
		top: 174px
		//BScroll设置定位很重要bottom：0
		bottom: 0
		left: 0
		width: 100%
		overflow: hidden
		.overview
			display: flex
			padding: 18px 0
			border-1px(rgba(7,17,27,0.1))
			.overview-left
				flex: 0 0 137px
				width: 137px
				border-right: 1px solid rgba(7,17,27,0.2)
				text-align: center
				//适应iPhone5的屏幕大小
				@media only screen and (max-width: 320px)
					flex: 0 0 120px
					width: 120px
				.score
					font-size: 24px
					color: rgb(255,153,0)
					line-height: 28px
				.title
					font-size: 12px
					color: rgb(7,17,27)
					line-height: 12px
					padding: 6px 0 8px
				.rankrate
					font-size: 10px
					color: rgb(147,153,159)
					line-height: 10px
					padding-bottom: 6px
			.overview-right
				flex: 1
				padding-left: 24px
				@media only screen and (max-width: 320px)
					padding-left: 6px
				.foodscore
					padding: 6px 0
				.title,.star,.score,.time
					display: inline-block
					font-size: 12px
					line-height: 18px
				.title
					color: rgb(7,17,27)
				.star
					padding: 0 8px
					vertical-align: top
				.score
					color: rgb(255,153,0)
				.time
					color: rgb(147,153,159)
					padding: 0 8px
		.line
			width: 100%
			height: 16px
			background: #f3f5f7
			border-1px(rgba(7,17,27,0.1))
		.ratings-wrapper
			.rating
				margin: 18px
				padding-bottom: 18px
				display: flex
				border-1px(rgba(7,17,27,0.1))
				.avatar
					flex: 0 0 28px
					border-radius: 50%
				.rating-right
					flex: 1
					padding-left: 12px
					position: relative
					.star,.time
						display: inline-block
					.name
						font-size: 10px
						color: rgb(7,17,27)
					.time,.star
						font-size: 10px
						color: rgb(147,153,159)
						line-height: 12px
						padding: 4px 0 6px 0
					.text
						font-size: 12px
						color: rgb(7,17,27)
						line-height: 18px
						padding-bottom: 8px
					.ratetime
						position: absolute
						top: 0
						right: 0
						font-size: 10px
						color: rgb(147,153,159)
						line-height: 12px
					.bottom
						.icon-thumb_up,.icon-thumb_down
							display: inline-block
							font-size: 12px
							line-height: 24px
							float: left
						.icon-thumb_up
							margin-right: 4px
							color: rgb(0,160,220)
						.icon-thumb_down
							color: rgb(183,187,191)
						.recommend
							display: inline-block
							font-size: 9px
							color: rgb(147,153,159)
							line-height: 16px
							padding: 0 6px
							margin-left: 8px
							margin-bottom: 4px
							border: 1px solid rgba(7,17,27,0.1)
							border-radius: 1px
</style>
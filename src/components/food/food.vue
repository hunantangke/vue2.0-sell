<template>
	<transition name="flag">
		<div class="food" v-show="showFlag" ref="foodscroll">
			<div class="food-content">
				<div class="header-img">
					<img :src="food.image">
					<i class="icon-arrow_lift" @click="back"></i>
				</div>
				<div class="content">
					<div class="food-name">{{food.name}}</div>
					<div class="food-sell">
						<span>月售{{food.sellCount}}份</span><span class="ratingsmall">好评率{{food.ratingsmall}}%</span>
					</div>
					<div class="food-price">
						<span class="icon">&yen;</span><span>{{food.price}}</span><s v-show="food.oldPrice" class="oldprice">&yen;{{food.oldPrice}}</s>
					</div>
					<transition name="change" mode="out-in">
						<div class="addcart" v-if="!food.count" @click.stop.prevent="addcart" key="addcart">加入购物车</div>
						<div class="cart2" v-else key="cart">
							<cartcontrol :food="food"></cartcontrol>
						</div>
					</transition>
					
				</div>
				<div class="line"></div>
				<div class="goodsinfo">
					<div class="info-title">商品介绍</div>
					<div class="info">{{food.info}}</div>
				</div>
				<div class="line"></div>

				<div class="goodratingsmall">
					<div class="ratingsmall-title">商品评价</div>
					<ratingsmall :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings"></ratingsmall>
					<div class="ratinglist">
						<ul v-if="food.ratings">
							<li v-for="item in food.ratings" class="li-rating border-1px" v-show="needshow(item.rateType,item.text)">
								<div class="top">
									<span class="time">{{item.rateTime | formatDate}}</span>
									<img class="avatar" :src="item.avatar" width="12" height="12">
									<span class="username">{{item.username}}</span>
								</div>
								<div class="bottom">
									<i class="icon-thumb_down" v-if="item.rateType===1"></i>
									<i class="icon-thumb_up" v-else></i>
									<span class="text">{{item.text}}</span>
								</div>
							</li>
						</ul>
						<div class="norating" v-show="!food.ratings">暂无评价</div>
					</div>
				</div>
			</div>
		</div>
	</transition>
</template>

<script>
	import BScroll from 'better-scroll'
	import cartcontrol from 'components/cartcontrol/cartcontrol'
	import Vue from 'vue'
	import ratingsmall from 'components/ratingsmall/ratingsmall'
	import {formatDate} from 'common/js/date'

	const alls = 2
	const negatives = 1
	const positives = 0

	export default {
		props: {
			food: {}
		},
		data() {
			return {
				showFlag: false,
				selectType: {
					a: alls
				},
				onlyContent: {
					a: false
				},
				desc: {
					all: '全部',
					positive: '推荐',
					negative: '吐槽'
				}
			}
		},
		filters: {
			formatDate(time) {
				let date = new Date(time)
				return formatDate(date,'yyyy-MM-dd hh:mm')
			}
		},
		components: {
			cartcontrol,
			ratingsmall
		},
		//挂载放置在创建后(惨痛的bug教训)
		created() {
			this.$nextTick(() => {
				this.scroll = new BScroll(this.$refs.foodscroll,{
					click: true
				})
			})
		},
		methods: {
			show() {
				this.showFlag = true
				this.selectType.a = alls
				this.onlyContent.a = false
			},
			back() {
				this.showFlag = false
			},
			addcart(event) {
				if(!event._constructed) {
					return
				}
				//注册data没有的属性
				Vue.set(this.food,'count',1)
			},
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

	.flag-enter-active,.flag-leave-active {
	  transition: all .2s linear;
	}
	.flag-enter, .flag-leave-active {
	  transform: translateX(100%);
	  opacity: 0;
	}
	.food
		position: fixed
		top: 0
		left: 0
		bottom: 48px
		width: 100%
		background: #fff
		z-index: 30
		.header-img
			position: relative
			width: 100%
			height: 0
			padding-top: 100%
			img
				position: absolute
				top: 0
				left: 0
				width: 100%
				height: 100%
			.icon-arrow_lift
				position: absolute
				top: 20px
				left: 20px
				font-size: 20px
				padding: 15px
				display: inline-block
				background: green
				border-radius: 50%
		.content
			position: relative
			.food-name
				padding: 18px 0 8px 18px
				font-size: 14px
				font-weight: 700
				color: black
				line-height: 14px
			.food-sell
				padding: 0 0 10px 18px
				font-size: 10px
				color: rgb(147,153,159)
				.ratingsmall
					padding-left: 12px
			.food-price
				color: red
				padding: 0 0 18px 18px
				font-size: 14px
				font-weight: 700
				line-height: 24px
				border-1px(rgba(7,17,27,0.1))
				.icon
					font-size: 10px
				.oldprice
					padding-left: 10px
					font-size: 10px
					color: rgb(147,153,159)
					font-weight: 700
			.change-enter-active, .change-leave-active {
			  transition: opacity .4s ease;
			}
			.change-enter, .change-leave-active {
			  opacity: 0;
			}
			.addcart
				position: absolute
				right: 18px
				bottom:	18px
				background: rgb(0,160,220)
				border-radius: 12px
				padding: 8px
				color: #fff
				font-size: 10px
				font-weight: 12px
			.cart2
				position: absolute
				right: 18px
				bottom:	12px
		.line
			width: 100%
			height: 16px
			background: #f3f5f7
			border-1px(rgba(7,17,27,0.1))
		.goodsinfo
			padding: 18px
			border-1px(rgba(7,17,27,0.1))
			.info-title
				padding-bottom: 6px
				font-size: 14px
				color: black
				line-height: 14px
			.info
				font-size: 12px
				line-height: 24px
				color: rgb(147,153,159)
				padding: 0 8px
		.goodratingsmall
			.ratingsmall-title
				padding-bottom: 6px
				font-size: 14px
				color: black
				line-height: 14px
				margin: 18px 0 0 18px
			.ratinglist
				.li-rating
					margin: 0 18px
					padding: 12px 0
					border-1px(rgba(7,17,27,0.1))
					color: rgb(147,153,159)
					.top
						margin-bottom: 6px
						font-size: 10px
						.time
							positive: inline-block
							line-height: 12px
						.username
							positive: inline-block
							line-height: 12px
							float: right
							margin-right: 6px
						.avatar
							positive: inline-block
							float: right
							border-radius: 50%
					.bottom
						.icon-thumb_up,.icon-thumb_down
							font-size: 12px
							line-height: 24px
						.icon-thumb_up
							margin-right: 4px
							color: rgb(0,160,220)
						.text
							font-size: 12px
							color: black
				.norating
					font-size: 14px
					padding: 16px 0
</style>
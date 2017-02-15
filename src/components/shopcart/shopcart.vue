<template>
	<div class="all">
		<div class="shopcart">
			<div class="content">
				<div class="left" @click="listShow">
					<div class="logo-wrapper">
											<!--购物车激活高亮样式-->
						<div class="icon" :class="{'heightLight': totalCount>0}">
							<span class="icon-shopping_cart"></span>
						</div>
						<!--购物车中商品的数量-->
						<div class="num" v-show="totalCount>0">{{totalCount}}</div>
					</div>
					<div class="price" :class="{'heightLight': totalCount>0}">&yen; {{totalPrice}}</div>
					<div class="desc">另需配送费 &yen; {{deliveryPrice}}元</div>
				</div>
				<div class="right" @click.stop.prevent="pay">
			        <div class="pay" :class="payClass">{{payDesc}}</div>
				</div>
			</div>
			<!--购物车列表-->
			<transition-group name="fade">
				<div class="shopcart-list" v-show="fold" ref="shopcartList" key>
					<div class="list-header"  v-show="totalPrice>0">
						<div class="title">购物车</div>
						<span class="empty" @click="empty">清空</span>
					</div>
					<div class="list-content" ref="listContent">
						<ul>
							<li class="food border-1px" v-for="(food,index) in selectFoods" :key="food">
								<span class="name">{{food.name}}</span>
								<div class="price">
									<span class="icon">&yen;</span> {{food.price*food.count}}
								</div>
								<div class="cartcontrol-wrapper">
									<cartcontrol :food="food"></cartcontrol>
								</div>
							</li>
						</ul>
					</div>
				</div>
			</transition-group>
		</div>
		<transition name="mask">
			<div class="list-mask" v-show="fold && totalPrice>0"></div>
		</transition>
	</div>
	
</template>

<script>
	import cartcontrol from 'components/cartcontrol/cartcontrol'
	import BScroll from 'better-scroll'
	import Vue from 'vue'

	export default {
		//接收父组件的数据
		props: {
			minPrice:{
				default: 0
			},
			deliveryPrice:{
				default: 0
			},
			//获购物车中商品的单价与数量
			selectFoods: {
				default() {
					return []
				}
			}
		},
		data() {
			return {
				fold: false
			}
		},
		components: {
			cartcontrol
		},
		methods: {
			listShow() {
				if(this.totalCount) {
					this.fold = !this.fold
					this.$nextTick(() => {
						if(!this.scroll) {
							this.scroll = new BScroll(this.$refs.listContent,{
								click: true
							})
						} else {
							this.scroll.refresh()
						}
					})
				}
			},
			empty() {
				this.selectFoods.forEach((food) => {
					food.count = 0
				})
			},
			pay() {
				if(this.totalPrice < this.minPrice) {
					return
				} else {
					window.alert(`请支付${this.totalPrice+this.deliveryPrice}元`)
				}
			}
		},
		computed: {
			//计算总价
			totalPrice() {
				let total = 0
				this.selectFoods.forEach((food) => {
					total += food.price*food.count
				})
				//暂时性的掩盖！！！
				if(total<0) {
					return total = 0
				}
				return total
			},
			//购物车商品的总数
			totalCount() {
				let count = 0
				this.selectFoods.forEach((food) => {
					count += food.count
				})
				return count
			},
			payDesc() {
		        if (this.totalPrice === 0) {
					return `￥${this.minPrice}元起送`;
		        } else if (this.totalPrice < this.minPrice) {
					let diff = this.minPrice - this.totalPrice;
					return `还差￥${diff}元起送`;
		        } else {
					return '去结算';
		        }
		    },
		    payClass() {
				if (this.totalPrice < this.minPrice) {
					return 'not-enough';
				} else {
					return 'enough';
				}
		    },
			
		}
	}
</script>

<style lang="stylus">
	@import '../../common/stylus/mixin.styl'

	.shopcart
		position: fixed
		left: 0
		bottom: 0
		width: 100%
		height: 48px
		z-index: 50
		color: #80858a
		.content
			display: flex
			background: #141d27
			.left
				flex: 1
				.logo-wrapper
					display: inline-block
					position: relative
					vertical-align: top
					top: -10px
					margin: 0 8px
					padding: 6px
					width: 56px
					height: 56px
					box-sizing: border-box
					border-radius: 50%
					vertical-align: top
					background: #141d27
					.icon
						width: 100%
						height: 100%
						border-radius: 50%
						background: #2b343c
						&.heightLight
							background: rgb(0,160,220)
							color: white
						font-size: 24px
						text-align: center
						line-height: 48px
					.num
						position: absolute
						top: 0
						right: 0
						font-size: 9px
						line-height: 16px
						text-align: center
						width: 24px
						border-radius: 6px
						background: rgb(240,20,20)
						box-shadow: 0 4px 8px 0 rgba(0,0,0,0.4)
						color: white
				.price
					display: inline-block
					font-size: 16px
					line-height: 24px
					font-weight: 700
					padding: 0 6px 0 0
					margin: 10px 0
					border-sizing: border-box
					border-right: 1px solid rgba(255,255,255,0.1)
					&.heightLight
						color: white
				.desc
					display: inline-block
					margin: 12px 0 0 4px
					font-size: 10px	
					line-height: 24px	
			.right
				flex: 0 0 105px
				width: 105px
				.pay
					font-size: 12px
					font-weight: 700
					text-align: center
					padding: 20px 0
					&.not-enough
						background: #2b333b
					&.enough
						background: #00b43c
						color: #fff
		.fade-enter-active {
		  transition: all .4s linear;
		}
		.fade-leave-active {
		  transition: all .5s linear;
		}
		.fade-enter, .fade-leave-active {
		  transform: translateY(220px);
		  opacity: 0;
		}
		.shopcart-list
			position: absolute
			bottom: 47px
			left: 0
			width: 100%
			z-index: -1
			.list-header
				height: 40px
				line-height: 40px
				padding: 0 18px
				background: #f3f5f7
				border-bottom: 1px solid rgba(7,17,27,0.1)
				.title
					float: left
					font-size: 14px
					color: rgb(7,17,27)
					line-height: 40px
				.empty
					font-size: 12px
					color: rgb(0,160,220)
					float: right
			.list-content
				background: white
				max-height: 230px
				overflow: hidden
				.food
					position: relative
					height: 46px
					border-1px(rgba(7,17,27,0.2))
					margin: 0 18px
					.name
						display: inline-block
						font-size: 14px
						color: rgb(7,17,27)
						line-height: 48px
					.price
						position: absolute
						left: 200px
						bottom: 14px
						display: inline-block
						font-size: 14px
						font-weight: 700
						color: red
						.icon
							font-size: 10px
					.cartcontrol
						position: absolute
						bottom: 0
						right: 30px
						margin-left: 30px
	.mask-enter-active {
	  transition: all .5s;
	}
	.mask-leave-active {
	  transition: all .5s;
	}
	.mask-enter, .mask-leave-active {
	  opacity: 0;
	}
	.list-mask
		position: fixed
		top: 0
		left: 0
		height: 100%
		width: 100%
		background: rgba(7,17,27,0.5)
		z-index: 40
</style>
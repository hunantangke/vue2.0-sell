<template>
	<div class="goods">
		<!--左侧菜单-->
		<div class="menu-wrapper" ref="menuwrapper">
			<ul>
				<li class="menu-item border-1px" v-for="(item,index) in goods" :class="{'current':currentIndex===index}"
				@click="selectMenu(index,$event)">
					<span class="text">
						<span class="icon" :class="classMap[item.type]" v-show="item.type>0"></span>
						{{item.name}}
					</span>
				</li>
			</ul>
		</div>
		<!--右侧详情-->
		<div class="foods-wrapper" ref="foodswrapper">
			<ul>
				<li class="food-list food-list-hook" v-for="item in goods">
					<h1 class="title">{{item.name}}</h1>
					<ul>
						<li class="food-item border-1px" v-for="food in item.foods">
							<div class="icon" @click="selectFood(food,$event)">
								<img :src="food.icon" width="57px" height="57px">
							</div>
							<div class="content">
								<div @click="selectFood(food,$event)">
									<h2 class="name">{{food.name}}</h2>
									<p class="desc" v-show="food.description">{{food.description}}</p>
									<div class="extra">
										<span class="sellCount">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
									</div>
									<div class="price">
										<span class="rmb">&yen;</span><span class="newprice">{{food.price}}</span>
										<s v-if="food.oldPrice" class="oldPrice">&yen;{{food.oldPrice}}</s>
									</div>
								</div>
								<!--购物车控件-->
								<cartcontrol :food="food" class="cart"></cartcontrol>
							</div>
						</li>
					</ul>
				</li>
			</ul>
		</div>
		<!--购物车(传递给子组件数据)-->
		<shopcart :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice" :select-foods="selectFoods">
		</shopcart>
						<!--商品详情 使用ref调用food的方法-->
		<food :food="selectedFood" ref="food"></food>
	</div>
</template>

<script type="text/javascript">
	import BScroll from 'better-scroll'
	import shopcart from 'components/shopcart/shopcart'
	import cartcontrol from 'components/cartcontrol/cartcontrol'
	import food from 'components/food/food'

	const ERR_ON = 0
 
	export default {
		props: {
			seller: {},
		},
		components: {
			shopcart,
			cartcontrol,
			food
		},
		data() {
			return {
				goods: [],
				listHeight: [],
				sy: 0,
				selectedFood: {}
			}
		},
		created() {
			//api请求goods
			this.$http.get('/api/goods').then((res) => {
			    res = res.body
			    if (res.errno === ERR_ON) {
			        this.goods = res.data
			        //数据更新获取DOM
			        this.$nextTick(() => {
			        	this._initScroll() 
			        	this._computedHeight()
			        })
			    }
			}),
			//优惠图标样式
			this.classMap = ['decrease','discount','special','invoice','guarantee']
		},
		computed: {
			//获取左侧菜单索引值
			currentIndex() {
				for(let i=0;i<this.listHeight.length;i++) {
					let height1 = this.listHeight[i]
					let height2 = this.listHeight[i + 1]
					if (!height2 || (this.sy >= height1 && this.sy < height2)) {
						return i
					}
				}
				return 0
			},
			//数据双向绑定，计算属性观测goods进而重新计算购物车商品数
			selectFoods() {
				let foods = []
				this.goods.forEach((good) => {
					good.foods.forEach((food) => {
						if(food.count) {
							foods.push(food)
						}
					})
				})
				return foods
			}
		},
		//定义开头为下划线的为私有方法，其他的为可调用(父组件可调用子组件的方法，但子不可调父)
		methods: {
			//点击左侧菜单，右侧变动
			selectMenu(index,event) {
				if(!event._constructed) {
					return
				}
				let foodList = this.$refs.foodswrapper.getElementsByClassName('food-list-hook')
				let el = foodList[index]
				this.foodsScroll.scrollToElement(el,600)
			},
			//滚动条插件设置(通过$refs挂载)
			_initScroll() {
				this.menuScroll = new BScroll(this.$refs.menuwrapper,{
					click: true
				})
				this.foodsScroll = new BScroll(this.$refs.foodswrapper,{
					click: true,
					probeType: 3  //获取实时滚动的位置
				})
				//通过监控scroll事件返回滚动位置
				this.foodsScroll.on('scroll',(pos) => {
					this.sy = Math.abs(Math.round(pos.y))
				})
			},
			//客户端视口clientHeight获取
			_computedHeight() {
				let foodList = this.$refs.foodswrapper.getElementsByClassName('food-list-hook')
				let height = 0
				this.listHeight.push(height)

				for(let i=0;i<foodList.length;i++) {
					let item = foodList[i]
					height += item.clientHeight
					this.listHeight.push(height)
				}
			},
			//点击商品详情
			selectFood(food,event) {
				if(!event._constructed) {
					return
				}
				this.selectedFood = food
				//调用子组件food,show()方法
				this.$refs.food.show()
			}
		}
	}
</script>

<style lang="stylus">
	@import '../../common/stylus/mixin.styl'

	.goods
		display: flex
		position: absolute
		top: 174px
		bottom: 46px
		width: 100%
		overflow: hidden
		.menu-wrapper
			flex: 0 0 80px
			width: 80px
			background: #f3f5f7
			.menu-item
				display: table
				height: 54px
				width: 56px
				padding: 0 12px
				border-1px(rgba(7,17,27,0.1))
				&.current
					position: relative
					margin-top: -1px
					z-index: 10
					background: #fff
					font-weight: 700
					.text
						border-none()
				.icon
					display: inline-block
					width: 12px
					height: 12px
					vertical-align: top
					background-size: 12px 12px
					background-repeat: no-repeat
					&.decrease
						bg-image('img/decrease_3')
					&.discount
						bg-image('img/discount_3')
					&.guarantee
						bg-image('img/guarantee_3')
					&.invoice
						bg-image('img/invoice_3')
					&.special
						bg-image('img/special_3')
				.text
					display: table-cell
					font-size: 12px
					line-height: 14px
					vertical-align: middle
		.foods-wrapper
			width: 100%
			.title
				font-size: 12px
				color: rgb(147,153,159)
				// height: 26px
				line-height: 26px
				background: #f3f5f7
				padding-left: 12px
				border-left: 2px solid #d9dde1
			.food-item
				position: relative
				display: flex
				margin: 18px
				padding-bottom: 18px
				border-1px(rgba(7,17,27,0.1))
				&:last-child
					border-none()
					padding-bottom: 0
				.icon
					flex: 0 0 57px
					margin-right: 10px
				.content
					flex: 1
					.name
						font-size: 14px
						color: rgb(7,17,27)
						line-height: 14px
						padding: 2px 0 8px
					.desc
						padding-bottom: 8px
						line-height: 14px
					.desc,.extra
						font-size: 10px
						color: rgb(147,153,159)
						.sellCount
							display: inline-block
							margin-right: 12px
					.price
						line-height: 24px
						.rmb
							font-size: 4px
							color: rgb(240,20,20)
						.newprice
							font-size: 14px
							font-weight: 700
							color: rgb(240,20,20)
						.oldPrice
							color: rgb(147,153,159)
							font-size: 10px
							line-height: 24px
							font-weight: 700
					.cart
						position: absolute
						right: 0
						bottom: -6px						
</style>
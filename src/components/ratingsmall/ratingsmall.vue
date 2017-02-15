<template>
	<div class="rating-wrapper">
		<div class="rating-type border-1px">
			<span class="span span1" @click="select(2,$event)" :class="{'active':selectType.a===2}">{{desc.all}}
				<span class="num">{{ratings.length}}</span>
			</span>
			<span class="span span1" @click="select(0,$event)" :class="{'active':selectType.a===0}">{{desc.positive}}
				<span class="num">{{positive}}</span>
			</span>
			<span class="span span3" @click="select(1,$event)" :class="{'active':selectType.a===1}">{{desc.negative}}
				<span class="num">{{ratings.length-positive}}</span>
			</span>
		</div>
		<div class="switch border-1px"  :class="{'on':onlyContent.a}">
			<span class="icon-check_circle" @click.stop.prevent="onActive"></span>
			<span class="only">只看有内容的评价</span>
		</div>
	</div>
</template>

<script>
	import Vue from 'vue'

	const Event = new Vue()

	const alls = 2
	const negatives = 1
	const positives = 0

	export default {
		props: {
			ratings: {
				default() {
					return []
				}
			},
			//设置为对象,可以通过改变子进而改变父
			selectType: {
				default() {
					a: alls
				}
			},
			onlyContent: {
				default() {
					a: true
				}
			},
			desc: {
				default() {
					return {
						all: '全部',
						positive: '满意',
						negative: '不满意'
					}
				}
			}
		},
		methods: {
			onActive(event) {
				if(!event._constructed) {
					return
				}
				// console.log(event)
				this.onlyContent.a = !this.onlyContent.a
			},
			select(type) {
				this.selectType.a = type
			}
		},
		computed: {
			positive() {
				let count = 0
				this.ratings.forEach((rating) => {
					if(rating.rateType === positives) {
						count++
					}
				})
				return count
			}
		}
	}
</script>

<style lang="stylus">
	@import '../../common/stylus/mixin.styl'

	.rating-wrapper
		.rating-type
			margin: 0 18px 12px 18px
			padding: 18px 0
			border-1px(rgba(7,17,27,0.1))
			font-size: 0
			.span
				display: inline-block
				padding: 8px 12px
				font-size: 12px
				margin-right: 8px
				border-radius: 2px
				color: black
				&.active
					color: #fff
			.num
				display: inline-block
				font-size: 10px
				margin-left: 6px
			.span1
				background: rgba(0,160,220,0.2)
				&.active
					background: rgb(0,160,220)
			.span3
				background: rgba(147,153,159,0.2)
				&.active
					background: rgb(147,153,159)
		.switch
			padding: 0 0 12px 18px
			font-size: 0
			color: rgb(147,153,159)
			border-1px(rgba(7,17,27,0.4))
			.icon-check_circle
				font-size: 24px
				line-height: 24px
				display: inline-block
				margin-right: 4px
				vertical-align: top
			&.on
				.icon-check_circle
					color: #00c850
			.only
				font-size: 14px
				display: inline-block
				line-height: 24px
</style>
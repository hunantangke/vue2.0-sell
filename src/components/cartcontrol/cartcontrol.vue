<template>
	<div class="cartcontrol">
		<transition name="move">
			<div class="decrease icon-remove_circle_outline" v-show="food.count>0" @click.stop.prevent="decreaseCart($event)"></div>
		</transition>
		<div class="count" v-show="food.count>0">{{food.count}}</div>
		<div class="add icon-add_circle" @click.stop.prevent="addCart($event)"></div>
	</div>
</template>

<script>
	import Vue from 'vue'

	export default {
		props: {
			food: {}
		},
		methods: {
			addCart(event) {
				if(!event._constructed) {
					return
				}
				if (!this.food.count) {
					Vue.set(this.food,'count',1)
				} else {
					this.food.count++
				}
			},
			decreaseCart(event) {
				if(!event._constructed) {
					return
				}
				this.food.count--
			}
		}
	}
</script>

<style lang="stylus">
	.cartcontrol
		.move-enter-active {
		  transition: all .3s linear;
		}
		.move-leave-active {
		  transition: all .3s linear;
		}
		.move-enter, .move-leave-active {
		  transform: translateX(20px);
		  opacity: 0;
		}
		.decrease,.add
			display: inline-block
			padding: 10px
			font-size: 24px
			color: rgb(0,160,220)
			margin: 0 -4px
		.count
			display: inline-block
			font-size: 12px
			color: rgb(147,153,159)
			position: relative
			bottom: 6px
</style>
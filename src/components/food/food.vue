<template>
	<div class="food" v-show="showFlag" transition="move" v-el:food>
		<div class="food-content">
			<div class="image-header">
				<img :src="food.image">
				<div class="back">
					<i class="icon-arrow_lift" @click="hide"></i>
				</div>
			</div>
			<div class="content">
				<div class="title">{{food.name}}</div>
				<div class="detail">
					<span class="sell-count">月售{{food.sellCount}}</span>
					<span class="rating">好评率{{food.rating}}%</span>
				</div>
				<div class="price">
					<span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.price}}</span>
				</div>
				<div class="cartcontrol-wrap">
					<cart-control :food="food"></cart-control>
				</div>
				<div class="buy" v-show="!food.count || food.count === 0" @click.stop.prevent="addFirst" transition="fade">加入购物车</div>
			</div>
			<split></split>
			<div class="info">
				<h1 class="title">商品信息</h1>
				<p class="text">{{food.info}}</p>
			</div>
		</div>
	</div>
</template>
<script type="text/ecmascript-6">
import BScroll from 'better-scroll';
import cartControl from '../cartControl/cartControl.vue';
import split from '../split/split.vue';
import Vue from 'vue';
	export default {
		data () {
			return {
				showFlag: false
			};
		},
		props: {
			food: {
				type: Object
			}
		},
		components: {
			cartControl,
			split
		},
		methods: {
			show () {
				this.showFlag = true;
				this.$nextTick(() => {
					if (!this.scroll) {
						// 这个地方入过坑
						this.scroll = new BScroll(this.$els.food, {
							click: true
						});
					} else {
						this.scroll.refresh();
					}
				});
			},
			hide () {
				this.showFlag = false;
			},
			addFirst (event) {
				if (!event._constructed) {
					return;
				}
				if (!this.food.count) {
					Vue.set(this.food, 'count', 1);
				}
				this.$dispatch('cart.add', event.target);
			}
		}
	};
</script>
<style ref="stylesheet/stylus" lang="stylus">
	.food
		position: fixed
		left: 0
		top: 0
		bottom: 48px
		z-index: 30
		width: 100%
		background: #fff
		&.move-transition
			transition: all 0.2s linear
			transform: translate3D(0,0,0)
		&.move-enter, &.move-leave
			transform: translate3D(100%,0,0)
		.image-header
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
			.back
				position: absolute
				top: 10px
				left: 0
				.icon-arrow_lift
					display: block
					font-size: 20px
					padding: 10px
					color: #fff
		.content
			padding: 18px
			position: relative
			.title
				margin-bottom: 8px
				font-size: 14px
				line-height: 14px
				font-weight: 700
				color: rgb(7,17,27)
			.detail
				margin-bottom: 18px
				height: 10px
				line-height: 10px
				font-size: 0
				.sell-count, .rating
					font-size: 10px
					color: rgb(147,153,159)
				.sell-count
					margin-right: 12px
			.price
				font-weight: 700
				line-height: 24px
				.now
					margin-right: 8px
					font-size: 14px
					color: rgb(240,20,20)
				.old
					text-decoration: line-through
					font-size: 10px
					color: rgb(147,153,159)
			.cartcontrol-wrap
				position: absolute
				right: 12px
				bottom: 12px
			.buy
				position: absolute
				right: 18px
				bottom: 18px
				z-index: 10
				height: 24px
				line-height: 24px
				padding: 0 12px
				box-sizing: border-box
				border-radius: 12px
				font-size: 10px
				color: #fff
				background: rgb(0,160,220)
				&.fade-transition
					opacity: 1
					transition: all 0.2s
				&.fade-enter, &.fade-leave
					opacity: 0
		.info
			padding: 18px
			.title
				line-height: 14px
				margin-bottom: 6px
				font-size: 14px
				font-weight: 700
				color: rgb(7,17,27)	
			.text
				line-height: 24px
				padding: 0 8px
				font-size: 12px
				font-weight: 200
				color: rgb(77,85,93)
</style>
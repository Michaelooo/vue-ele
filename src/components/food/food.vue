<template>
	<div class="food" v-show="showFlag" transition="move" v-el:food>
		<div class="food-content">
			<div class="image-header">
				<img :src="food.image">
			</div>
			<div class="back">
				<i class="icon-arrow_lift" @click="hide"></i>
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
			<split></split>
			<div class="ratings">
				<h1 class="title">商品评价</h1>
				<ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings"></ratingselect>
				<div class="rating-wrapper">
					<ul v-show="food.ratings && food.ratings.length">
						<li v-for="rating in food.ratings" class="rating-item" v-show="needShow(rating.rateType, rating.text)">
							<div class="user">
								<span class="name">{{rating.username}}</span>
								<img :src="rating.avatar" class="avatar" width="12px" height="12px">
							</div>
							<div class="time">{{rating.rateTime  | formatDate}}</div>
							<p class="text">
								<span :class="{'icon-thumb_up':rating.rateType === 0,'icon-thumb_down':rating.rateType === 1}"></span>{{rating.text}}
							</p>
						</li>
					</ul>
					<div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
				</div>
			</div>
		</div>
	</div>
</template>
<script type="text/ecmascript-6">
import BScroll from 'better-scroll';
import cartControl from '../cartControl/cartControl.vue';
import split from '../split/split.vue';
import ratingselect from '../ratingselect/ratingselect.vue';
import {formatDate} from '../../common/js/date';
import Vue from 'vue';
// const POSITIVE = 0;
// const NEGATIVE = 1;
const ALL = 2;
	export default {
		data () {
			return {
				showFlag: false,
				selectType: ALL,
				onlyContent: true,
				desc: {
					all: '全部',
					positive: '推荐',
					negative: '不满意'
				}
			};
		},
		props: {
			food: {
				type: Object
			}
		},
		components: {
			cartControl,
			split,
			ratingselect
		},
		events: {
			'ratingtype.select' (type) {
				this.selectType = type;
				this.$nextTick(() => {
					this.scroll.refresh();
				});
			},
			'content.toggle' (onlyContent) {
				this.onlyContent = onlyContent;
				this.$nextTick(() => {
					this.scroll.refresh();
				});
			}
		},
		filters: {
			formatDate (time) {
				let date = new Date(time);
				return formatDate(date, 'yyyy-MM-dd hh:mm');
			}
		},
		methods: {
			show () {
				this.showFlag = true;
				this.selectType = ALL;
				this.onlyContent = true;
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
			},
			needShow (type, text) {
				if (this.onlyContent && !text) {
					return false;
				}
				if (this.selectType === ALL) {
					return true;
				} else {
					return type === this.selectType;
				}
			}
		}
	};
</script>
<style ref="stylesheet/stylus" lang="stylus">
@import "../../common/stylus/mixin.styl"
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
		.ratings
			padding-top: 18px
			.title
				line-height: 14px
				margin-left: 16px
				font-size: 14px
				font-weight: 700
				color: rgb(7,17,27)	
			.rating-wrapper
				padding: 0 18px
				.rating-item
					position: relative
					padding: 16px 0
					border-1px: rgba(7,17,27,0.1)
					.user
						position: absolute
						right: 0
						top: 16px
						line-height: 12px
						font-size: 0
						.name
							display: inline-block
							margin-right: 6px
							font-size: 10px
							color: rgb(147,153,159)
						.avatar
							border-radius: 50%
					.time
						margin-bottom: 6px
						line-height: 12px
						font-size: 10px
						color: rgb(147,153,159)
					.text
						font-size: 12px
						color: rgb(7,17,27)
						line-height: 16px
						.icon-thumb_up, .icon-thumb_down
							display: inline-block
							line-height: 16px
							font-size: 12px
							color: rgb(147,153,159)
							margin-right: 8px
						.icon-thumb_up
							color: rgb(0,160,220)
				.no-rating
					padding: 16px 0
					font-size: 12px
					color: rgb(147,153,159)
</style>
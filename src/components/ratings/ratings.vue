<template>
	<div>
		<div class="ratings-wrapper" ref="ratings">
			<div class="ratings-content">
				<div class="overview">
					<div class="overview-left">
						<h1 class="score">{{seller.score}}</h1>
						<div class="title">综合评分</div>
						<div class="rank">高于周边商家{{seller.rankRate}}%</div>
					</div>
					<div class="overview-right">
						<div class="score-wrapper">
							<span class="title">服务态度</span>
							<star :size="36" :score="seller.serviceScore"></star>
							<span class="score">{{seller.serviceScore}}</span>
						</div>
						<div class="score-wrapper">
							<span class="title">商品评分</span>
							<star :size="36" :score="seller.foodScore"></star>
							<span class="score">{{seller.foodScore}}</span>
						</div>
						<div class="delivery-wrapper">
							<span class="title">送达时间</span>
							<span class="delivery">{{seller.deliveryTime}}分钟</span>
						</div>
					</div>
				</div>
				<split></split>
				<ratingselect @select="selectRating" @toggle="toggleContent" :select-type="selectType" :only-content="onlyContent" :ratings="ratings"></ratingselect>
				<div class="rating-wrapper">
					<ul>
						<li v-for="rating in ratings" class="rating-item" v-show="needShow(rating.rateType, rating.text)">
							<div class="avatar">
								<img :src="rating.avatar" width="28px" height="28px">
							</div>
							<div class="content">
								<div class="name">{{rating.username}}</div>
								<div class="star-wrapper">
									<star :size="24" :score="rating.score"></star>
									<span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
								</div>
								<p class="text">{{rating.text}}</p>
								<div class="recommend" v-show="rating.recommend && rating.recommend.length">
									<span class="icon-thumb_up"></span>
									<span v-for="item in rating.recommend" class="item">{{item}}</span>
								</div>
								<div class="time">{{rating.rateTime | formatDate}}</div>
							</div>
						</li>
					</ul>
				</div>
			</div>
		</div>
	</div>
</template>
<script type="text/ecmascript-6">
import BScroll from 'better-scroll';
import star from 'components/star/star.vue';
import split from 'components/split/split.vue';
import ratingselect from 'components/ratingselect/ratingselect.vue';
import {formatDate} from 'common/js/date';

const ALL = 2;
const ERR_OK = 0;
	export default {
		props: {
			seller: {
				type: Object
			}
		},
		components: {
			star,
			split,
			ratingselect
		},
		created () {
			this.$http.get('/api/ratings').then((response) => {
				response = response.body;
				if (response.errno === ERR_OK) {
					this.ratings = response.data;
					this.$nextTick(() => {
						this.scroll = new BScroll(this.$refs.ratings, {
							click: true
						});
					});
				}
			});
		},
		methods: {
			needShow (type, text) {
				if (this.onlyContent && !text) {
					return false;
				}
				if (this.selectType === ALL) {
					return true;
				} else {
					return type === this.selectType;
				}
			},
			selectRating (type) {
				this.selectType = type;
				this.$nextTick(() => {
					this.scroll.refresh();
				});
			},
			toggleContent () {
				this.onlyContent = !this.onlyContent;
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
		data () {
			return {
				ratings: [],
				selectType: ALL,
				onlyContent: true
			};
		}
	};
</script>
<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"
	.ratings-wrapper
		position: absolute
		top: 174px
		left: 0
		bottom: 0
		width: 100%
		overflow: hidden
		.overview
			display: flex
			padding: 18px 0
			.overview-left
				flex: 0 0 137px
				width: 137px
				padding: 6px 0
				border-right: 1px solid rgba(7,17,27,0.1)
				text-align: center
				@media only screen and (max-width: 320px)
					flex: 0 0 120px
					width: 120px
				.score
					line-height: 28px
					font-size: 24px
					color: rgb(255,153,0)
				.title
					margin-top: 6px
					font-size: 12px
					color: rgb(7,17,27)
					line-height: 12px
				.rank
					margin: 8px
					font-size: 10px
					color: rgba(7,17,27,0.4)
					line-height: 10px
			.overview-right
				flex: 1
				padding: 6px 0 6px 24px
				@media only screen and (max-width: 320px)
					padding-left: 6px
				.score-wrapper
					display: flex
					margin-bottom: 8px
					font-size: 0
					.title
						display: inline-block
						line-height: 18px
						font-size: 12px
						vertical-align: top
						color: rgb(7,17,27)
					.star
						display: inline-block
						margin: 0 12px
						vertical-align: top
					.score
						display: inline-block
						vertical-align: top
						line-height: 18px
						font-size: 12px
						color: rgb(255,163,0)
				.delivery-wrapper
					font-size: 0
					marigin-bottom: 0
					.title
						display: inline-block
						line-height: 18px
						font-size: 12px
						vertical-align: top
						color: rgb(7,17,27)
					.delivery
						font-size: 12px
						line-height: 18px
						color: rgb(147,153,159)
						margin-left: 12px
		.rating-wrapper
			padding: 0 18px
			.rating-item
				display: flex
				padding: 18px 0
				border-1px(rgba(7,17,27,0.1))
				.avatar
					flex: 0 0 28px
					width: 28px
					margin-right: 12px
					img
						border-radius: 50%
				.content
					position: relative
					flex: 1
					.name
						line-height: 12px
						font-size: 10px
						color: rgb(7,17,27)
						margin-bottom: 4px
					.star-wrapper
						display: flex
						margin-bottom: 6px
						font-size: 0
						.star
							display: inline-block
							vertical-align: top
							margin-right: 6px
						.delivery
							display: inline-block
							vertical-align: top
							font-size: 10px
							font-weight: 200
							color: rgb(147,153,159)
							line-height: 12px
					.text
						line-height: 18px
						font-size: 12px
						color: rgb(7,17,27)
						margin-bottm: 8px
					.recommend
						line-height: 16px
						font-size: 0px
						color: rgb(0,160,220)
						.icon-thumb_up, .item
							display: inline-block
							margin: 0 8px 4px 0
							font-size: 9px
						.icon-thumb_up
							color: rgb(0,160,220)
						.item
							padding: 0 6px
							border: 1px solid rgba(7,17,27,0.1)
							border-radius: 1px
							color: rgb(147,153,159)
							background: #fff
					.time
						position: absolute
						top: 0
						right: 0
						line-height: 12px
						font-size: 10px
						color: rgb(147,153,159)
</style>
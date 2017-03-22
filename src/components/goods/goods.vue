<template>
	<div class="goods">
		<div class="menu-wrapper">
			<ul>
				<li v-for = "item in goods"  class="menu-item">
					<span class="text border-1px">
						<span class="icon" v-show = "item.type>0" :class="classMap[item.type]"></span>{{item.name}}
					</span>
				</li>
			</ul>
		</div>
		<div class="foods-wrapper">
			<ul>
				<li v-for="item in goods" class="food-list">
					<h1 class="title">{{item.name}}</h1>
					<ul>
						<li v-for="food in item.foods" class="food-item">
							<div class="icon">
								<img :src="food.icon">
							</div>
							<div class="content">
								<h2 class="name">{{food.name}}</h2>
								<p class="desc">{{food.description}}</p>
								<div class="extra">
									<span>月售{{food.sellCount}}份</span>
									<span>好评率{{food.rating}}%</span>
								</div>
								<div class="price">
									<span>￥{{food.price}}</span>
									<span v-show="food.oldPrice">￥{{food.price}}</span>
								</div>
							</div>
						</li>
					</ul>
				</li>
			</ul>
		</div>
	</div>
</template>
<script type="text/ecmascript-6">
const ERR_OK = 0;
	export default {
		data () {
			return {
				goods: {}
			};
		},
		props: {
			seller: {
				type: Object
			}
		},
		created () {
			this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special'];
			this.$http.get('/api/goods').then((response) => {
				response = response.body;
				if (response.body !== ERR_OK) {
					this.goods = response.data;
				}
			});
		},
		methods: {

		}
	};
</script>
<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"
	.goods
		display: flex
		position:absolute
		width: 100%
		top: 174px
		bottom: 46px
		overflow: hidden
		.menu-wrapper
			flex: 0 0 80px
			width: 80px
			background-color: #f3f5f7
			.menu-item
				display: table
				width: 54px
				height: 56px
				font-size: 24px
				padding: 0 12px
				font-weight: 200
				line-height: 14px
				// color: rgb(240, 20, 20)
				.icon
					display: inline-block
					width: 12px
					height: 12px
					margin-right: 2px
					background-size: 12px 12px
					background-repeat: no-repeat
					&.decrease
						bg-image('decrease_3')
					&.discount
						bg-image('discount_3')
					&.guarantee
						bg-image('guarantee_3')
					&.invoice
						bg-image('invoice_3')
					&.special
						bg-image('special_3')
				.text
					display: table-cell
					width: 56px
					font-size: 12px
					vertical-align: middle
					border-1px(rgba(7, 17, 27, 0.2))
		.foods-wrapper
			flex: 1
</style>

<template>
	<view class="goods-item">
		<!-- 商品左侧图片区域 -->
		<view class="goods-item-left">
			<label class="radio" v-if="showRadio">
				<radio value="r1" :checked="goods.goods_state" color="#c00000" @click="radioClickHandler" />
			</label>
			<image :src="goods.goods_small_logo||defaultPic" class="goods-pic"></image>
		</view>
		<!-- 商品右侧信息区域 -->
		<view class="goods-item-right">
			<!-- 商品标题 -->
			<view class="goods-name">{{goods.goods_name}}</view>
			<view class="goods-info-box">
				<!-- 商品价格 -->
				<view class="goods-price">
					¥{{goods.goods_price|toFixed}}
				</view>
				<!-- 商品数量 -->
				<uni-number-box :min="1" :value="goods.goods_count" @change="numberChangeHandler" v-if="showNum">
				</uni-number-box>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		name: "my-goods",
		props: {
			goods: {
				type: Object,
				default: {}
			},
			// 是否展示图片左侧的 radio
			showRadio: {
				type: Boolean,
				// 如果外界没有指定 showRadio 属性的值，则默认不展示 radio 组件
				default: false
			},
			// 是否展示价格右侧的 NumberBox 组件
			showNum: {
				type: Boolean,
				default: false
			}
		},
		data() {
			return {
				// 默认的空图片
				defaultPic: 'https://img3.doubanio.com/f/movie/8dd0c794499fe925ae2ae89ee30cd225750457b4/pics/movie/celebrity-default-medium.png'
			};
		},
		filters: {
			toFixed(num) {
				return Number(num).toFixed(2)
			}
		},
		methods: {
			// radio 组件的点击事件处理函数
			radioClickHandler() {
				// 通过 this.$emit() 触发外界通过 @ 绑定的 radio-change 事件，
				// 同时把商品的 Id 和 勾选状态 作为参数传递给 radio-change 事件处理函数
				this.$emit('radio-change', {
					// 商品的 Id
					goods_id: this.goods.goods_id,
					// 商品最新的勾选状态
					goods_state: !this.goods.goods_state
				})
			},
			// NumberBox 组件的 change 事件处理函数
			numberChangeHandler(val) {
				// 通过 this.$emit() 触发外界通过 @ 绑定的 num-change 事件
				this.$emit('num-change', {
					// 商品的 Id
					goods_id: this.goods.goods_id,
					// 商品的最新数量
					goods_count: +val
				})
			}
		}
	}
</script>

<style lang="scss">
	.goods-item {
		width: 750rpx;
		box-sizing: border-box;
		display: flex;
		padding: 10px 5px;
		border-bottom: 1px solid #f0f0f0;

		.goods-item-left {
			margin-right: 5px;
			display: flex;
			align-items: center;
			justify-content: space-between;

			.goods-pic {
				width: 100px;
				height: 100px;
				display: block;
			}
		}

		.goods-item-right {
			display: flex;
			flex: 1;
			flex-direction: column;
			justify-content: space-between;

			.goods-name {
				font-size: 13px;
			}

			.goods-info-box {
				display: flex;
				justify-content: space-between;
				align-items: center;

				.goods-price {
					color: #c00000;
					font-size: 16px;
				}
			}
		}
	}
</style>

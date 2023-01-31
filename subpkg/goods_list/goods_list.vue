<template>
	<view>
		<view class="goods-list">
			<view v-for="(goods,i) in goodsList" :key="i" @click="gotoDetail(goods)">
				<my-goods :goods="goods"></my-goods>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 请求参数对象
				queryObj: {
					query: '',
					cid: '',
					pagenum: 1,
					pagesize: 10
				},
				// 商品列表的数据
				goodsList: [],
				// 总数量，用来实现分页
				total: 0,
				// 是否正在请求数据
				isLoading: false
			};
		},
		onLoad(options) {
			// 将页面参数转存到 this.queryObj 对象中
			this.queryObj.cid = options.cid || ''
			this.queryObj.query = options.query || ''
			this.getGoodsList()
		},
		methods: {
			async getGoodsList(cb) {
				// ** 打开节流阀
				this.isLoading = true
				const {
					data: res
				} = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
				// ** 关闭节流阀
				this.isLoading = false
				// 只要数据请求完毕，就立即按需调用 cb 回调函数
				cb && cb()
				if (res.meta.status !== 200) return uni.$showMsg()
				// 为数据赋值：通过展开运算符的形式，进行新旧数据的拼接
				this.goodsList = [...this.goodsList, ...res.message.goods]
				this.total = res.message.total
			},

			gotoDetail(goods) {
				uni.navigateTo({
					url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods.goods_id
				})
			}
		},

		// 触底的事件
		onReachBottom() {
			// 判断是否还有下一页数据
			if (this.queryObj.pagesize * this.queryObj.pagenum >= this.total) return uni.$showMsg('数据加载完毕！')
			// 判断是否正在请求其它数据，如果是，则不发起额外的请求
			if (this.isLoading) return
			// 让页码值自增 +1
			this.queryObj.pagenum++
			this.getGoodsList()
		},

		// 下拉刷新的事件
		onPullDownRefresh() {
			// 1. 重置关键数据
			this.queryObj.pagenum = 0
			this.total = 0
			this.isLoading = false
			this.goodsList = []
			// 2. 重新发起请求
			this.getGoodsList(() => uni.stopPullDownRefresh())
		}
	}
</script>

<style lang="scss">

</style>

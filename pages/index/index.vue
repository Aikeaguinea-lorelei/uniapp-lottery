<template>
	<view class="content">
		<!-- 将list中代码拷贝过来 -->
		<unicloud-db ref="udb" v-slot:default="{data, pagination, loading, hasMore, error}" :collection="collectionList"
			field="title,picture">
			<view v-if="error">{{error.message}}</view>
			<view v-else-if="data">
				<uni-list class="uni-list">
					<!-- 对于list里面每一个值,它的index为num,即0~max里的随机数 -->
					<uni-list-item v-for="(item, index) in data" :key="index" :class="{active: index==num}">
						<template v-slot:body>
							<view class="item">
								<view>
									<!-- 拿到图片链接,并展示在列表中 -->
									<img :src="item.picture.url" mode="aspectFill">
								</view>
								<view>
									<!-- 此处默认显示为_id，请根据需要自行修改为其他字段 -->
									<!-- 如果使用了联表查询，请参考生成的 admin 项目中 list.vue 页面 -->
									{{item.title}}
								</view>
							</view>
						</template>
					</uni-list-item>
				</uni-list>
			</view>
			<uni-load-more :status="loading?'loading':(hasMore ? 'more' : 'noMore')"></uni-load-more>
		</unicloud-db>

<!-- 		<view class="panel">
			<text>
				{{num}}
			</text>
		</view>
 -->		<view class="button-area">
			<button v-show="isOver" @click="start">开始抽奖</button>
			<button v-show="!isOver" @click="stop">结束抽奖</button>
		</view>
	</view>
</template>

<script>
	const db = uniCloud.database();
	const dbCollectionName = 'lottery';
	
	export default {
		data() {
			return {
				length:0,
				num: 0,
				// max: 100, // 不含100
				interval: 100, // 数值自动变化的时间间隔
				isOver: true,
				clock: null, // 计时器
				collectionList:dbCollectionName,
				loadMore: {
				    contentdown: '',
				    contentrefresh: '',
				    contentnomore: ''
				}
			}
		},
		// 拿到数据库中的代码,并查询结果,数据的最大值就是所含数据的个数
		created() {
			this.$nextTick(()=>{
				db.collection(dbCollectionName)
				    .get()
				    .then((res) => {  // res 为数据库查询结果
					  this.max=res.result.data.length
				  })
			})
		},
		// 下拉刷新
		onPullDownRefresh() {
		  this.$refs.udb.loadData({
		    clear: true
		  }, () => {
		    uni.stopPullDownRefresh()
		  })
		},
		// 下拉加载更多
		onReachBottom() {
		  this.$refs.udb.loadMore()
		},
		methods: {
			start() {
				this.isOver = false // 点击开始抽奖按钮后切换到false状态,即展示"结束抽奖"
				// 每隔interval时间,页面展示的数值自动变化一次下面随机数装置创建出来的数
				this.clock = setInterval(() => {
					this.num = this.getRandomNum()  // 让num在0到最大值之间取随机数
				}, this.interval)
			},
			// 随机数装置 : 在0~max之间取随机数
			getRandomNum() {
				// max=100,min=10  得到数的范围就是[10,100)
				return Math.floor(Math.random() * parseInt(this.max))
			},
			// 当点击"结束抽奖",停下计时器,并把状态变为"开始抽奖"
			stop() {
				clearInterval(this.clock)
				this.isOver = true
			}
		}
	}
</script>

<style lang="scss">
	.active{
		border: 4rpx solid red;
		box-sizing: content-box;
	}
	.item{
		text-align: center;
		img{
			width:200rpx;
			height:200rpx;
		}
	}
	.content {
		.uni-list{
			display: flex;
			flex-wrap: wrap;  // flex的元素在需要时换行
			flex-direction: row;
			justify-content: center;
		}
		.uni-list-item{
			border:  4rpx solid transparent;
		}
		.active{
			border-color: red;
		}
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
	.button-area {
		margin-top: 120rpx;
	}

	.panel {
		width: 600rpx;
		height: 400rpx;
		margin-top: 60rpx;
		box-shadow: 0 0 40rpx 0 rgba(0, 0, 0, 0.1);
		border-radius: 8rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		border: 1rpx solid #c8c7cc;

		text {
			font-size: 120rpx;
		}
	}
</style>

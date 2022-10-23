<template>
	<view class="container">
		<view v-for="(item,index) in memoList" :key="index">
			<uni-card :title="item.title"  :extra="'Created at '+item.createTime" @click="goToDetail(index)">
				{{item.intro}}...
			</uni-card>
		</view>
		<uni-fab horizontal="right" vertical="bottom" @fabClick="click()"></uni-fab>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				memoList:[],
				seen:0
			}
		},
		onLoad() {
		
		},
		onShow(){
			//获取缓存中所存放的备忘录数组
			let that = this
			uni.getStorage({
				key:'memoList',
				success(res){
					that.memoList = res.data
					console.log(res.data)
				}
			})
		},
		methods: {
			click(){
				uni.navigateTo({
					url:'/pages/addmemo/addmemo'
				})
			},
			goToDetail(index){
				uni.navigateTo({
					url:'/pages/detail/detail?index='+index
				})
			}
		}
	}
</script>

<style scoped>
	.container {
		padding: 20px;/* 
		font-size: 14px;
		line-height: 24px; */
		
	}
	
	/deep/ .uni-card{
		border-radius: 10px;
	}
	/deep/ .uni-card .uni-card__header{
		border: 0;
	}
	/deep/ .uni-card{
		background-color: #FFFFCC;
	}
	/deep/ .uni-card .uni-card__header .uni-card__header-content .uni-card__header-content-title{
		font-size: 25px;
		font-weight: bold;
	}
	/deep/ .uni-fab__circle .uni-fab__plus .uni-fab__circle--rightBottom .uni-fab__content--other-platform{
		background-color: #99CCFF;
	}
</style>

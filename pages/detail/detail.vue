<template>
	<view class="container">
		<view class="viewtitle">
			<text class=".title">
				{{memoData.title}}
			</text>
		</view>
		<view class="viewtime">
			<text class=".time">
				创建于：{{memoData.createTime}}
			</text>
		</view>
		<view class="viewtext">
		<!-- <uni-easyinput type="textarea" autoHeight  v-model='memoData.content' class="viewcontent"   disabled='false'/> -->
			 <text class="viewcontent">
				{{memoData.content}}
			</text> 
		</view> 
		<view class="">
			<button type="default" @click="editmemo()" class="button_fix">修改</button>
			<button type="warn" @click="deleteItem()" class="button_del">
				<uni-icons type='trash' size="20" color = 'white'></uni-icons>
				删除
			</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				memoData:null,
				index: -1
			}
		},
		onLoad:function(options){
			let that = this
			uni.getStorage({
				key:'memoList',
				success(res){
					let memoList = res.data
					that.memoData = memoList[options.index]
					that.index = options.index
				},
				fail(err){
					console.log('showModel',err)
				}
			})
		},
		methods: {
			editmemo(){
				uni.navigateTo({
					url:'/pages/editmemo/editmemo?index='+this.index
				})
			},
			deleteItem(){
				let that = this
				//相当于在缓存中把数组memoList中的一个元素删掉
				
				uni.showModal({
					title:'警告',
					content:'确认删除？',
					success(res){
						if(res.confirm){
							//这里表示用户确认要删除，把删除的功能写在这里
							uni.getStorage({
								key:'memoList',
								success(res){
									let datas = res.data
									datas.splice(that.index,1)
									uni.setStorage({
										key:'memoList',
										data:datas,
										success(){
											//删除成功返回列表页
											uni.showToast({
												title:'删除成功',
												icon:'success',
												duration:1000
											})
											setTimeout(function(){
												uni.navigateBack()
											},1000)
											
										},
										fail(){
											console.log('setStorage',err)
										}
									})
								},
								fail(err){
									console.log('getStorage',err)
								}
							})
						}
					}
				})
			}
		}
	}
</script>

<style>
	
	.container{
		padding: 20px;
		margin: 20px;
	}
	.viewtitle{
		width: 100%;
		text-align: center;
	}
	.viewtime{
		width: 100%;
		text-align: center;
		padding-bottom: 5px;
		margin-bottom: 20px;
		margin-top: 5px;
		border-bottom: 1px solid #FFFFCC;
	}
	.viewtext{
		padding: 10px;
	}
	.viewcontent{
		/* width: 100%;
		background-color: #FFFFCC;
		border-radius: 10px;
		
		min-height: 50px; */
		    margin-bottom: 20px;
			max-width: 100%;
			border-radius: 10px;
		    padding: 20px 0;
		    display: flex;
		    min-height: 150px;
		    background-color: #FFFFCC;
		    justify-content: center;
		    align-items: center;
		    text-align: center;
		    font-size: 15px;
		    color: #353535;
		    line-height: 1.8;
			 word-break: break-all;
	}
	.button_fix{
		margin: 30px;
		margin-top: 70px;
		margin-bottom: 0%;
	}
	.button_del{
		margin: 30px;
		margin-top: 10px;
	}
	.title{
		font-size: 30px;
		font-weight: bold;
		
	}
	.time{
		color: #FFFFCC;
	}
	.content{
		margin: 5px;
		padding: 15px;
	}

</style>

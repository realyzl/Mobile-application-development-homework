<template>
	<view class="container">
		<uni-nav-bar rightIcon="checkmarkempty" @clickRight="editItem()" title="修改备忘录"></uni-nav-bar>
		<view class="viewtime">
			{{formData.createTime}}
		</view>
		<uni-forms :modelValue="formData" ref="form" :rules="rules">
			<uni-forms-item name="title">
				<uni-easyinput  v-model="formData.title" placeholder="这里输入标题 " />
			</uni-forms-item>
			
			<uni-forms-item name="content">
				<uni-easyinput type="textarea" autoHeight v-model="formData.content" placeholder="内容限200字以内" />
			</uni-forms-item>
		</uni-forms>
		<button type="default" @click="editItem()" class="button_fix">OK!</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				index:-1,
				formData:{
					title:"",
					content:"",
					createTime:"",
					intro: ""
				},
				rules:{
					title:{
						rules:[
							{required:true,errorMessage:'标题必填'},
							{minLength:3,maxLength:20,errorMessage:'内容限3-20个字'}
						]
					},
					content:{
						rules:[
						{maxLength:200,errorMessage:'内容限200字以内'}
						]
					}
				}
			}
		},
		onLoad:function(options){
			let that = this
			uni.getStorage({
				key:'memoList',
				success(res){
					let datas = res.data
					that.formData = datas[options.index]
					that.index = options.index
				}
			})
		},
		methods: {
			editItem(){
				//询问是否真的要修改得到确定后再修改showModal()
				//修改操作相当于从缓存中拿到memoList把当前元素删除掉splice（index，1）
				//然后把formData的值作品为index为下标的元素添加到数组中splice（index，0，formData）
				//把修改后的数组再重新写回到缓存中
				//修改成功后告诉用户修改结果然后再进入首页uni.navigateTo()
				//------------------------------------------------------------
				let that = this
				uni.showModal({
					title:'警告',
					content:'确认修改？',
					success(res){
						if(res.confirm){
							//===================================================================
				
							that.$refs.form.validate().then(res => {
								uni.getStorage({
									key:'memoList',
									success(res){
										let datas = res.data
										//---------------------------------------------
										datas.splice(that.index,1,that.formData)
										that.formData.intro = that.formData.content.substr(0,6)
										//---------------------------------------------
										uni.setStorage({
											key:'memoList',
											data:datas,
											success(){
												//删除成功返回列表页
												uni.showToast({
													title:'修改成功',
													icon:'success',
													duration:1000
												})
												setTimeout(function(){
													uni.reLaunch({
														url:'/pages/index/index'
													})
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
							}).catch(err => {
								console.log('表单错误信息：', err);
							})

							//===================================================================
							
							//---------------------------------------------------
							
							//---------------------------------------------------
						}
					}
				})
			},
			//---------------------------------------------
			
			//---------------------------------------------
		}
	}
</script>

<style>
	.container{
		padding: 10px;
		margin: 20px;
	}
	.viewtime{
		width: 100%;
		text-align: center;
		padding-bottom: 5px;
		margin-bottom: 20px;
		margin-top: 5px;
		border-bottom: 1px solid #FFFFCC;
		color: #FFFFCC;
	}

</style>

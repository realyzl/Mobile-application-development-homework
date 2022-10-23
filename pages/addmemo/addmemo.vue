<template>
	<view class="container">
		<uni-nav-bar rightIcon="checkmarkempty" @clickRight="addNew" title="新建备忘录"></uni-nav-bar>
		<view class="viewtime">
			创建时间：{{formData.createTime}}
		</view>
		<uni-forms :modelValue="formData" ref="form" :rules="rules">
			<uni-forms-item name="title">
				<uni-easyinput  v-model="formData.title" placeholder="这里输入标题 " />
			</uni-forms-item>
			
			<uni-forms-item name="content">
				<uni-easyinput type="textarea" autoHeight v-model="formData.content" placeholder="内容限200字以内" />
			</uni-forms-item>
		</uni-forms>
		<button type="default" @click="addNew()" class="button_fix">OK!</button>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
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
		onLoad(){
			let datetime = this.getTime()
			this.formData.createTime = datetime.date + "  " + datetime.time
		},
		methods: {
			getTime(){
				var now = new Date();
				var year = now.getFullYear();
				var month = now.getMonth() + 1;
				month >=1 && month <=9 ? (month = "0" + month) : "";
				var day = now.getDate() < 10 ? ("0" + now.getDate()) : now.getDate();
				var hour = now.getHours() < 10 ? ("0" + now.getHours()) : now.getHours();
				var minute = now.getMinutes() < 10 ? ("0" + now.getMinutes()) : now.getMinutes();
				var second = now.getSeconds() <10 ? ("0" + now.getSeconds()) : now.getSeconds();
				var value = {
					date: year + '-' + month + '-' + day,
					time: hour + ':' + minute + ':' + second
				}
				return value;
			},
			addNew(){
				let that=this
				this.$refs.form.validate()
				.then(res=>{
					//1.先对创建时间和字符串截取进行处理
					that.formData.intro = that.formData.content.substr(0,6)
					//对缓存数据进行操作
					uni.getStorage({
						key:'memoList',
						success(res){
							//在数组中追加一个新的元素，然后再把这个数据项写回到
							let datas = res.data
							datas.splice(0,0,that.formData)
							uni.setStorage({
								key: 'memoList',
								data:datas,
								success(){
									//删除成功返回列表页
									uni.showToast({
										title:'新建成功',
										icon:'success',
										duration:1000
									})
									setTimeout(function(){
										uni.reLaunch({
											url:'/pages/index/index'
										})
									},1000)
									
								},
							})
						},
						fail(){
							let memoData =[that.formData]
							uni.setStorage({
								key:'memoList',
								data:memoData,
								success(){
									//删除成功返回列表页
									uni.showToast({
										title:'新建成功',
										icon:'success',
										duration:1000
									})
									setTimeout(function(){
										uni.reLaunch({
											url:'/pages/index/index'
										})
									},1000)
									
								},
							})
						}
					})
				})
				.catch(err=>{
					console.log(err)
				})
			}
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
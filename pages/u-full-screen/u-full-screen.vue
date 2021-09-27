<template>
	<u-modal v-model="show" :show-cancel-button="true" confirm-text="更新" title="提示" @cancel="cancel"
		@confirm="confirm">
		<view class="u-update-content">
			<rich-text :nodes="content"></rich-text>
		</view>
	</u-modal>
</template>

<script>
	export default {
		data() {
			return {
				show: false,
				// 传递给uni-app"rich-text"组件的内容，可以使用"<br>"进行换行
				content: `
					检查出不是同一天是否更新
				`,
				fileContent: ''
			}
		},
		onLoad() {
			const self = this;
			// 请求本地系统文件对象 plus.io.PRIVATE_WWW：应用运行资源目录常量
			plus.io.requestFileSystem(plus.io.PRIVATE_DOC, function(fobject) {
				// fs.root是根目录操作对象DirectoryEntry
				fobject.root.getFile('./config.json', {
					create: true
				}, function(fileEntry) {
					fileEntry.file(function(file) {
						var fileReader = new plus.io.FileReader();
						fileReader.readAsText(file, 'utf-8');
						fileReader.onloadend = function(evt) {
							console.log(evt.target.result)
							let date = new Date().getDate()
							let sdData=JSON.parse(evt.target.result)
							//self.addId(sdData.mend)
							//self.addId(sdData.not_mend)
							self.data=JSON.stringify(sdData)
							if (date === sdData.date) {
								self.closeModal('no')
							} else {
								self.show = true
							}
						}
					});
				});
			});

		},
		onReady() {

		},
		methods: {
			addId(arr) {
				arr.forEach(function(item, index) {
					if (index <= 26) {
						item.id = String.fromCharCode(97 + index) //65
					}
				})
			},
			cancel() {
				this.closeModal('no');
			},
			confirm() {
				this.closeModal('yes');
			},
			closeModal(txt) {
				uni.redirectTo({
					url: '/pages/index/index?update=' + txt + '&data=' + this.data
				});
			}
		}
	}
</script>

<style scoped lang="scss">
	.u-full-content {
		background-color: #00C777;
	}

	.u-update-content {
		font-size: 26rpx;
		color: $u-content-color;
		line-height: 1.7;
		padding: 30rpx;
	}
</style>

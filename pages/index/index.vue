<template>
	<scroll-view id="content" scroll-y>
		<ul>
			<li>
				<ul class='insideUL'>
					<u-line color='#1b1beb' direction='col' style='position: absolute;left: 0;top: 0;' />
					<u-line color='#1b1beb' />
					<li class='title'>
						<view>
							可补任务
						</view>
						<u-line color='#1b1beb' />
					</li>
					<li v-for="(item,index) in mendFilter" class='task' :key='index+"1"'>
						<view class="wrapper">
							<view class="describe">
								{{item.describe}}
							</view>
							<view>
								<u-button shape="square" size="medium" class='but' v-on:click='del("mend",item)'>完成
								</u-button>
							</view>
						</view>
						<u-line color='#1b1beb' />
					</li>
					<u-line color='#1b1beb' direction='col' style='position: absolute;right: 0;top: 0;' />
					<u-line color='#1b1beb' style='position: absolute;bottom: 0;' />
				</ul>
			</li>
			<li>
				<ul class='insideUL'>
					<u-line color='#1b1beb' direction='col' style='position: absolute;left: 0;top: 0;' />
					<u-line color='#1b1beb' />
					<li class='title'>
						<view>
							不可补任务
						</view>
						<u-line color='#1b1beb' />
					</li>
					<li v-for="(item,index) in not_mendFilter" class='task' :key='index+"1"'>
						<view class="wrapper">
							<view class="describe">
								{{item.describe}}
							</view>
							<view>
								<u-button shape="square" size="medium" class='but' v-on:click='del("not_mend",item)'>完成
								</u-button>
							</view>
						</view>
						<u-line color='#1b1beb' />
					</li>
					<u-line color='#1b1beb' direction='col' style='position: absolute;right: 0;top: 0;' />
					<u-line color='#1b1beb' style='position: absolute;bottom: 0;' />
				</ul>
			</li>

		</ul>

	</scroll-view>
</template>

<script>
	export default {
		data() {
			return {
				data: {
					date: 23,
					mend: [{
						describe: '举铃20下',
						accomplish: false
					}, {
						describe: '举铃19下',
						accomplish: true
					}],
					not_mend: [{
						describe: '英雄杀日常任务',
						accomplish: false
					}]

				}
			}
		},
		onLoad(option) {
			if (option.update === 'yes') {
				console.log(option.data)
				this.reset(option.data)
			}
			if (option.update === 'no') {
				this.$set(this, 'data', JSON.parse(option.data))
			}
		},
		computed: {
			mendFilter: function() {
				let data = []
				this.data.mend.forEach(function(item, index) {
					if (!item.accomplish) {
						data.push(item)
					}
				})
				//console.log(data)
				return data;
			},
			not_mendFilter: function() {
				let data = []
				this.data.not_mend.forEach(function(item, index) {
					if (!item.accomplish) {
						data.push(item)
					}
				})
				//console.log(data)
				return data;
			}
		},
		methods: {
			fileWrite: function() {
				const self = this;
				// 请求本地系统文件对象 plus.io.PRIVATE_WWW：应用运行资源目录常量
				plus.io.requestFileSystem(plus.io.PRIVATE_DOC, function(fobject) {
					// fs.root是根目录操作对象DirectoryEntry
					fobject.root.getFile('./config.json', {
						create: true
					}, function(fileEntry) {
						fileEntry.file(function(file) {
							fileEntry.createWriter(function(writer) {
								writer.write(JSON.stringify(self.data,null,4));
								console.log('文件更新成功')
							}, function(e) {
								console.log(e)
							});
						});
					});
				});
			},
			arrParse(arr, i) {
				let newArr = []
				arr.forEach(function(item) {
					item.accomplish = false
				})
				this.$set(this.data, i, arr)
			},
			reset(data) {
				let d = JSON.parse(data)
				//let d = this.data
				this.arrParse(d.mend, 'mend');
				this.arrParse(d.not_mend, 'not_mend');
				this.$set(this.data, 'date', new Date().getDate())
				this.fileWrite()
			},
			del(name, index) {
				index.accomplish = true
				this.fileWrite()
			}
		}
	}
</script>

<style lang='scss'>
	* {
		margin: 0;
		padding: 0;
		list-style: none;
	}

	#content {
		color: #1b1beb;
		position: fixed;
		width: 100%;
		height: 100%;
		background-color: #00021b;

		.insideUL {
			position: relative;
			margin: 0 10rpx;
			margin-top: 80rpx;

			.title {
				view {
					padding: 16rpx 17rpx 5rpx 17rpx;
					font-size: 16px;
					text-align: left;
					color: #1b1beb;
					font-weight: bold;
					text-align: center;
				}
			}

			.task {
				.wrapper {
					display: flex;
					width: 100%;
					padding: 10rpx 30rpx;

					.describe {
						flex: 1;
						line-height: 60rpx;
					}

					.but {
						height: 60rpx;
						color: #1b1beb;
						background-color: #00021b;
					}
				}
			}

		}

	}
</style>

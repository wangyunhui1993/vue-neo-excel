<template>
	<div class="app-container">
		<el-row>
			<el-button type="primary"  :loading="downloadLoading" @click="handleDownload">导出</el-button>
		</el-row>
		<el-table :data="tableData" border highlight-current-row style="width: 100%;margin-top:20px;">
			<el-table-column prop="id" label="ID" />
			<el-table-column prop="name" label="名字" />
		</el-table>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				tableData: [{
					id: 1,
					name: "test1"
				}, {
					id: 2,
					name: "test2"
				}],
				downloadLoading:false,
			}
		},
		methods: {
			handleDownload() {
				this.downloadLoading = true
				/* 
				 * 安装三个依赖包
				 * npm install -S file-saver
				 * npm install -S xlsx
				 * npm install -D script-loader 
				 */
				import('@/vendor/Export2Excel').then(excel => {
					const tHeader = ['ID', '名字']
					const filterVal = ['id', 'name']
					const list = this.tableData
					const data = this.formatJson(filterVal, list)
					excel.export_json_to_excel({
						header: tHeader,
						data,
						filename: this.filename,
						autoWidth: this.autoWidth,
						bookType: this.bookType
					})
					this.downloadLoading = false
				})
			},
			formatJson(filterVal, jsonData) {
				return jsonData.map(v => filterVal.map(j => {
					if (j === 'timestamp') {
						return parseTime(v[j])
					} else {
						return v[j]
					}
				}))
			}



		}
	}
</script>

<template>
	<div class="app-container">
		<el-button type="primary" @click="importBtn" v-loading="importLoading">导入</el-button>
		<el-table :data="tableData" border highlight-current-row style="width: 100%;margin-top:20px;">
			<el-table-column v-for="item of tableHeader" :prop="item" :label="item" :key="item" />
		</el-table>
		
		
		<el-dialog :visible.sync="showImportModal" width="350px" class="importModal">
			<div class="download" style="text-align: center;margin-bottom: 20px;">
				<a ref="downLoad" href="static/doc/导入模板/人员基础信息导入模板.xlsx" download="人员基础信息导入模板.xlsx">
					<el-button>下载导入模板</el-button>
				</a>
			</div>
			<div class="import-wrap clearfix">
				<input ref="upload" type="file" @change="importFile(this)" name="" accept=".csv, application/vnd.openxmlformats-officedocument.spreadsheetml.sheet, application/vnd.ms-excel" style="display: none;" />
				<div class="import-left">
					<img src="@/assets/images/add.gif">
					<p style="margin-top:40px;font-size:16px;color:#1c2438;">批量添加员工</p>
					<p style="text-align:left;margin:20px 0 30px;color:#a0a0a0;">适用于批量导入首次添加的员工信息</p>
					<upload-excel-component :on-success="handleSuccess" :before-upload="beforeUpload" />
					<!-- <el-button type="primary" @click="showChooseFile('add')" v-loading="addLoading">导入花名册</el-button> -->
				</div>
			</div>
		</el-dialog>
	</div>
	
	
	
</template>

<script>
	import UploadExcelComponent from '@/components/UploadExcel/index.vue'

	export default {
		name: 'UploadExcel',
		components: {
			UploadExcelComponent
		},
		data() {
			return {
				tableData: [],
				tableHeader: [],
				showImportModal:false,
				importLoading:false,
			}
		},
		methods: {
			importBtn(){
				this.showImportModal=true;
			},
			beforeUpload(file) {
				const isLt1M = file.size / 1024 / 1024 < 1

				if (isLt1M) {
					return true
				}

				this.$message({
					message: 'Please do not upload files larger than 1m in size.',
					type: 'warning'
				})
				return false
			},
			handleSuccess({
				results,
				header
			}) {
				this.tableData = results;
				console.log(results);
				// console.log(this.excelTrans(results));  //格式化
				this.tableHeader = header
			},
			excelTrans(outdata) {
				var createUserHeadInfo={
					'ID':'id',
					'提示信息':'info',
					'语音文件名':'name'
				}
				let arr = [];
				var obj = {};
				for (var i = 0; i < outdata.length; i++) {
					obj = {};
					for (var key in createUserHeadInfo) {
						if (key in outdata[i]) {
							obj[createUserHeadInfo[key]] = outdata[i][key];
						} else {
							obj[createUserHeadInfo[key]] = "";
						}
					}
					arr.push(obj);
				}
				return arr;
			},




		}
	}
</script>
<style>
	.importModal .import-wrap{
		width:90%;
		margin:0 auto;
	}
	.importModal .import-left{
		float:left;
		width:100%;
		text-align: center;
		box-sizing: border-box;
		transition: 0.5s all;
		border-radius: 6px;
		padding:40px 30px;
	}
	.importModal .import-left:hover{
		-webkit-box-shadow:0 0 10px #cdcdcd;  
		-moz-box-shadow:0 0 10px #cdcdcd;  
		box-shadow:0 0 10px #cdcdcd;
	}
</style>

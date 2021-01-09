<template>
	<div class="content">

		<h3 class="h3_title">询问笔录</h3>

		<view class="item">
			<view class="title">
				询问时间
			</view>
			<view class="value">
				{{ form.startTime }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				询问地点
			</view>
			<view class="value">
				{{ form.place }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				询问人一
			</view>
			<view class="value">
				{{ form.inquirer1 }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				询问人一工作单位
			</view>
			<view class="value">
				{{ form.inquirer1Unit }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				询问人二
			</view>
			<view class="value">
				{{ form.inquirer2 }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				询问人二工作单位
			</view>
			<view class="value">
				{{ form.inquirer2Unit }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				记录人
			</view>
			<view class="value">
				{{ form.recorder }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				记录人工作单位
			</view>
			<view class="value">
				{{ form.recorderUnit }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				被询问人
			</view>
			<view class="value">
				{{ form.interviewee }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				性别
			</view>
			<view class="value">
				{{ form.sex }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				出生日期
			</view>
			<view class="value">
				{{ form.birthday }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				户籍所在地
			</view>
			<view class="value">
				{{ form.domicile }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				现住址
			</view>
			<view class="value">
				{{ form.currentAddress }}{{ form.currentAddressDetails }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				联系方式
			</view>
			<view class="value">
				{{ form.phone }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				身份证号码
			</view>
			<view class="value">
				{{ form.cardId }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				问：你是不是人大代表？
			</view>
		</view>

		<view class="item">
			<view class="title">
				答：{{ form.isRepresent }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				问：请问你是否受到过行政处罚？
			</view>
		</view>

		<view class="item">
			<view class="title">
				答：{{ form.isPunish }}
			</view>
		</view>

		<template v-for="(item, index) in form.saveQuestionList">
			<view class="item">
				<view class="title">
					询问人类别：{{ item.question }}
				</view>
			</view>

			<view class="item">
				<view class="title">
					问：{{ item.questiondiy }}
				</view>
			</view>
			<view v-if="item.beforePic !== ''" class="item">
				<view class="title">
					问图：<img :src="item.beforePic" height="300" alt="">
				</view>
			</view>

			<view class="item">
				<view class="title">
					答：{{ item.answer }}
				</view>
			</view>
			<view v-if="item.afterPic !== ''" class="item">
				<view class="title">
					答图：<img :src="item.afterPic" height="300" alt="">
				</view>
			</view>
		</template>

		<view class="item">
			<view class="title">
				问：{{ form.questionLast.question01 }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				答：{{ form.questionLast.answer01 }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				问：{{ form.questionLast.question02 }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				答：{{ form.questionLast.answer02 }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				问：{{ form.questionLast.question03 }}
			</view>
		</view>

		<view class="item">
			<view class="title">
				答：{{ form.questionLast.answer03 }}
			</view>
		</view>



		<!-- 底部按钮容器 -->
		<div class="botmBtnContainer">
			<button @click="exportWord" size="small" type="primary">导出word</button>
		</div>
	</div>
</template>
<script>
	import docxtemplater from 'docxtemplater'
	import PizZip from 'pizzip'
	import JSZipUtils from 'jszip-utils'
	import {
		saveAs
	} from 'file-saver'
	export default {
		name: "home",
		data() {
			return {
				// 表单对象
				form: {
					questionLast: {}
				}
			};
		},
		onLoad() {
			var url = this.GetRequest()
			var id = url.data
			this.getData(id)
		},
		methods: {
			getData(id) {
				uniCloud.callFunction({
					name: 'wordrecord',
					data: {
						id: id
					}
				}).then((res) => {
					console.log(res)
					var data = res.result.data[0]
					if (data.isPunish === '0') {
						data.isPunish = '是'
					} else {
						data.isPunish = '否'
					}

					if (data.isRepresent === '0') {
						data.isRepresent = '是'
					} else {
						data.isRepresent = '否'
					}

					this.form = data
				})
			},
			GetRequest() {
				var url = location.search; //获取url中"?"符后的字串  
				var theRequest = new Object();
				if (url.indexOf("?") != -1) {
					var str = url.substr(1);
					var strs = str.split("&");
					for (var i = 0; i < strs.length; i++) {
						theRequest[strs[i].split("=")[0]] = unescape(strs[i].split("=")[1]);
					}
				}
				return theRequest;
			},
			// 点击导出word
			exportWord() {
				var ImageModule = require('open-docxtemplater-image-module');
				// 点击导出word
				let _this = this;
				// 读取并获得模板文件的二进制内容
				JSZipUtils.getBinaryContent("/static/input.docx", function(error, content) {
					// exportTemplate.docx是模板。我们在导出的时候，会根据此模板来导出对应的数据
					// 抛出异常
					if (error) {
						throw error;
					}

					// 图片处理
					let opts = {}
					opts.centered = true; // 图片居中，在word模板中定义方式为{%%image}
					opts.fileType = "docx";
					opts.getImage = function(tagValue, tagName) {
						return new Promise(function(resolve, reject) {
							JSZipUtils.getBinaryContent(tagValue, function(error, content) {
								if (error) {
									return reject(error);
								}
								return resolve(content);
							});
						});
					};
					opts.getSize = function(img, tagValue, tagName) {
						// FOR FIXED SIZE IMAGE :
						return [150, 150];

						// FOR IMAGE COMING FROM A URL (IF TAGVALUE IS AN ADDRESS) :
						// To use this feature, you have to be using docxtemplater async
						// (if you are calling setData(), you are not using async).
						return new Promise(function(resolve, reject) {
							var image = new Image();
							image.src = url;
							image.onload = function() {
								resolve([image.width, image.height]);
							};
							image.onerror = function(e) {
								console.log("img, tagValue, tagName : ", img, tagValue, tagName);
								alert("An error occured while loading " + tagValue);
								reject(e);
							};
						});
					};

					let imageModule = new ImageModule(opts);
					// 创建一个PizZip实例，内容为模板的内容
					let zip = new PizZip(content);
					// 创建并加载docxtemplater实例对象
					let doc = new docxtemplater();
					doc.attachModule(imageModule);
					doc.loadZip(zip);

					// 设置模板变量的值
					doc.setData({
						clients: _this.form,
					});

					try {
						// 用模板变量的值替换所有模板变量
						doc.render();
					} catch (error) {
						// 抛出异常
						let e = {
							message: error.message,
							name: error.name,
							stack: error.stack,
							properties: error.properties
						};
						throw error;
					}
					// 生成一个代表docxtemplater对象的zip文件（不是一个真实的文件，而是在内存中的表示）
					let out = doc.getZip().generate({
						type: "blob",
						mimeType: "application/vnd.openxmlformats-officedocument.wordprocessingml.document"
					});
					// 将目标文件对象保存为目标类型的文件，并命名
					saveAs(out, "word文档名称.docx");
				});
			}
		}
	};
</script>
<style lang="scss">
	.content {
		padding: 20rpx;

		.h3_title {
			text-align: center;
		}

		.item {
			margin: 20rpx 0;
			display: flex;
			justify-content: space-between;
		}
	}

	// 底部按钮
	.botmBtnContainer {
		text-align: center;
		padding: 20px;
	}
</style>

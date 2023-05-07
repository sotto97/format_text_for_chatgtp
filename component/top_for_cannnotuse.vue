<template>
	<div class="container w-full">
		<div class="w-full">
			<p class="my-2 text-center text-3xl font-semibold">ChatGPTプロンプト生成</p>
		</div>
		<div class="w-full my-4">
			<table class="w-6/12 px-2 text-center mx-auto">
				<tr v-for="(item, index) in lists" :key="item.id">
					<template v-if="index === 3">
						<td class="w-4/12 text-left" v-for="(subItem, subIndex) in item.subItems" :key="subItem.id">
							<input type="checkbox" class="h-5 w-5" :value="subItem.value" v-model="subItem.checked">
							<label>
								<span class="ml-2">{{ subItem.label }}</span>
							</label>
						</td>
					</template>
					<template v-else>
						<td class="w-4/12 text-left">
							<label>
								<input type="checkbox" class="h-5 w-5" :value="item.value" v-model="item.checked">
								<span class="ml-2">{{ item.label }}</span>
							</label>
						</td>
					</template>
				</tr>
			</table>
		</div>
		<div class="flex">
			<div class="w-1/2 px-2 text-left border-r-2 h-screen">
				<div class="flex">
					<div class="w-2/12 h-10 text-center bg-gray-500 hover:bg-gray-700 hover:cursor-potinter text-white font-bold mr-2 py-1 rounded"
						@click="resetForm">
						<span class="mdi mdi-refresh text-2xl"></span>
					</div>
					<div class="w-10/12 h-10 text-center bg-blue-500 text-white font-bold py-2 px-2 rounded">
						<span>入力フォーム</span>
					</div>
				</div>
				<form ref="myForm">
					<div class="mt-2">
						<div v-for="item in lists" :key="item.id">
							<section v-if="item.checked">
								<label class="block leading-6 text-gray-900">{{ item.label }}</label>
								<section class="mt-2">
									<input v-model="item.modelName"
										class="block w-full rounded-md p-1.5 text-gray-900 shadow-sm ring-1 ring-gray-300">
								</section>
							</section>
						</div>

					</div>
				</form>
			</div>
			<div class="w-1/2 px-2 text-left border-r-2 h-screen">
				<button v-if="condition === 'copybutton'" @click="copyToClipboard"
					class="h-10 w-full text-center bg-gray-500 hover:bg-gray-700 transition text-white font-bold p-2 rounded">
					<span>クリップボードにコピーする<span class="mdi mdi-clipboard-text-outline m-0 p-0 ml-1"></span></span>
				</button>
				<v-alert v-if="condition === 'success'" class="h-10 text-white p-2 font-semibold mb-0"
					type="success">コピーに成功しました</v-alert>
				<v-alert v-if="condition === 'error'" class="h-10 text-white p-2 font-semibold mb-0"
					type="error">コピーに失敗しました</v-alert>

				<div id="contextForChatgpt" ref="contextForChatgpt" class="mt-2 p-2 bg-gray-200 rounded-md">
					<div v-for="item in lists" :key="item.id">
						<div v-if="item.checked">
							<label class="mb-2" for="name">## {{ item.label }}</label>
							<p>{{ item.modelName }}</p>
						</div>
					</div>
				</div>

			</div>
		</div>
	</div>
</template>

<script>

const lists = [
	{
		id: 1,
		label: '質問内容',
		value: 'qaSwitch',
		modelName: '',
		checked: true
	},
	{
		id: 2,
		label: 'エラー文',
		value: 'errorSwitch',
		modelName: '',
		checked: false
	},
	{
		id: 3,
		label: '環境',
		value: 'envSwitch',
		modelName: '',
		checked: false
	},
	{
		id: 4,
		label: 'ソースコード',
		value: 'qaSwitch',
		modelName: '',
		checked: false
	},
	{
		id: 5,
		label: '',
		value: '',
		modelName: '',
		checked: false
	},
	{
		id: 6,
		label: '',
		value: '',
		modelName: '',
		checked: false
	},
];

module.exports = {
	data: function () {
		return {
			lists: lists,
			name: "topComponent",
			contextForChatgpt: '',
			condition: 'copybutton'
		};
	},
	mounted() {
		// Clipboard.jsの初期化
		new Clipboard(this.$refs.copyButton);
	},
	computed: {
		formattedErrorContext() {
			// 改行文字をHTMLの改行タグに変換する
			return this.errorContext.replace(/\n/g, '<br>');
		},
		formattedCodeContext() {
			// 改行文字をHTMLの改行タグに変換する
			return this.codeContext.replace(/\n/g, '<br>');
		},
	},
	methods: {
		resetForm() {
			this.lists['modelName'] = ''
		},
		adjustTextareaHeight(event) {
			const textarea = event.target;
			textarea.style.height = 'auto';
			textarea.style.height = `${textarea.scrollHeight}px`;
		},
		adjustCodeareaHeight(event) {
			const textarea = event.target;
			textarea.style.height = 'auto';
			textarea.style.height = `${textarea.scrollHeight}px`;
		},
		copyToClipboard() {
			const textToCopy = this.$refs.contextForChatgpt.innerText; // コピーしたいテキストを指定
			navigator.clipboard.writeText(textToCopy).then(() => {
				this.condition = 'success'; // v-alertを表示する
				// 3秒後にv-alertを非表示にする
				setTimeout(() => {
					this.condition = 'copybutton';
				}, 2000);
			}).catch((error) => {
				this.condition = 'error'; // v-alertを表示する
				// 3秒後にv-alertを非表示にする
				setTimeout(() => {
					this.condition = 'copybutton';
				}, 2000);
			});
		},
	},
};
</script>

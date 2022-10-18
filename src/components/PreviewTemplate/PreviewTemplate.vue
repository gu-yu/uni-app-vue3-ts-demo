<template>
	<view class="view_box">
		<uni-section title="预览" type="line">
			<view class="example">
				<uni-forms ref="customForm" label-position="top">
					<!-- 主标题 -->
					<uni-title class="word_break_title" align="center" type="h1" :title="comData.MainTitle"
						color="#027fff"></uni-title>
					<!-- 副标题 -->
					<uni-title class="word_break_title" type="h4" :title="comData.Subheading"></uni-title>
					<!-- 题目 -->
					<template v-for="(entryDataItem,index) in comData.entryData" :key="index">
						<uni-forms-item label-width="100%" :label="getEntryListItem(entryDataItem, index)"
							:required="entryDataItem.isRequired">
							<previewTemplateType :entryDataIndex="index" @checkboxChange="checkboxChange" :entryDataItem="entryDataItem"></previewTemplateType>
						</uni-forms-item>
					</template>
				</uni-forms>
			</view>
		</uni-section>
		<button style="margin-bottom: 10px;" type="primary" @click="handleBackActive">返回</button>
		<button type="primary" @click="formSubmit">校验</button>
		<uni-popup ref="PreviewPopup" type="message">
      <uni-popup-message type="error" message="请输入内容" :duration="2000"></uni-popup-message>
    </uni-popup>
	</view>
</template>

<script setup lang="ts">
	import {
		ref,
		reactive,
		inject,
		computed
	} from 'vue'
	import previewTemplateType from './PreviewTemplateType.vue'
	const props = inject('TemplateList')
	const emit = defineEmits(["backActive"])
	const PreviewPopup = ref<HTMLElement | null>(null)
	const customFormData = reactive({})
	const comData = computed(() => {
		return props
	})
	const getEntryListItem = (item, index): String => {
		return `${index + 1}.${item.TopicDescription}`
	}
	const getSelectEntryListItem = (list): any[] => {
		return Object.values(list)
	}
	const handleBackActive = () => {
		emit('backActive')
	}
	const checkboxChange = (v) => {
		customFormData[v.questionId] = {
			isRequired: v.isRequired,
			content: v.value,
			entryItemType: v.entryItemType,
			questionId: v.questionId
		}
	}
	const formSubmit = () => {
		if (getRequired()) {
			PreviewPopup.value.open()
		} else {
			console.log('都填了！！！！！！！！', customFormData)
		}
	}
	const getRequired = ():boolean => {
		const requiredList = Object.keys(customFormData)
		// 校验是否空卷
		if (requiredList.length === 0) {
			return true
		}
		// 校验是否漏填
		for (const i in customFormData) {
			if ((!customFormData[i].content || (customFormData[i].content && customFormData[i].content.length === 0)) && customFormData[i].isRequired) {
				return true
			}
		}
		// 校验是否必填
		const newRequiredList = comData._value.entryData.filter(item => {
			return item.isRequired && !requiredList.includes(item.questionId)
		})
		if (newRequiredList.length === 0) {
			return false
		} else {
			return true
		}
	}
</script>

<style lang="scss" scoped>
	.example {
		padding: 15px;
		background-color: #fff;
	}

	.button {
		display: flex;
		align-items: center;
		height: 35px;
		margin-left: 10px;
	}
	.main_preview_unipopup{
		width: 200px;
		height: 100px;
	}
	.uni-popup__wrapper {
		border-radius: 5px;
		overflow: hidden;
	}
</style>

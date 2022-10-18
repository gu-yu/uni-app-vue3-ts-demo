<template>
  <view>
    <!-- 单选 -->
    <view v-if="props.entryDataItem.templateType === 'radio'" class="uni-list">
      <radio-group @change="e=>checkboxChange(e,entryDataItem.isRequired,index)">
        <label style="justify-content: normal;" class="uni-list-cell uni-list-cell-pd"
          v-for="(item, index) in entryDataItem.SelectItemData" :key="index">
          <view>
            <radio :value="item.value" />
          </view>
          <view class="word_break_title">{{item.text}}</view>
        </label>
      </radio-group>
    </view>
    <!-- 多选 -->
    <view v-if="props.entryDataItem.templateType === 'checkbox'" class="uni-list">
      <checkbox-group @change="e=>checkboxChange(e,entryDataItem.isRequired)">
        <label style="justify-content: normal;" class="uni-list-cell uni-list-cell-pd"
          v-for="(item, index) in entryDataItem.SelectItemData" :key="index">
          <view>
            <checkbox  :value="item.value" />
          </view>
          <view class="word_break_title">{{item.text}}</view>
        </label>
      </checkbox-group>
    </view>
    <!-- 输入 -->
    <view v-if="props.entryDataItem.templateType === 'input'" class="uni-list">
      <uni-easyinput @input="e=>checkboxChange(e,entryDataItem.isRequired)" />
    </view>
  </view>
</template>

<script setup lang="ts">
  import {
		ref
	} from 'vue'
  const inputValue = ref('')
  const emit = defineEmits(["checkboxChange"])
  const props = defineProps({
    entryDataItem: Object,
    entryDataIndex: Number
  })
  const checkboxChange = (e, isRequired, index) => {
    const data = {
      value: props.entryDataItem.templateType==='input' ? e : e.detail.value,
      isRequired,
      index: props.entryDataIndex,
      entryItemType: props.entryDataItem.templateType,
      questionId: props.entryDataItem.questionId
    }
		emit('checkboxChange', data)
	}
</script>

<style lang="scss" scoped>
.uni-list-cell:after {
  display: none;
}
.uni-list:after {
  display: none;
}
</style>
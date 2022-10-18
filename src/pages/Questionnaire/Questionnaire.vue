<template>
  <view class="view_box">
    <!-- 单选或多选 -->
    <uni-forms v-if="templateType === 'checkbox' || templateType === 'radio'" :rules="rules" :modelValue="templateData" label-position="top" ref="entryBaseForm">
      <uni-forms-item name="TopicDescription" label="标题" required>
        <uni-easyinput type="textarea" v-model="templateData.TopicDescription" placeholder="请输入标题" />
      </uni-forms-item>
      <view class="SelectEntryOption">
        <text class="SelectEntryOption_Required uni-info">必填</text>
        <switch @change="switchChange" checked />
      </view>
      <button class="content_button" type="primary" @click="handleAdd">添加选择项</button>
      <template v-for="(item, index) in templateData.entryList" :key="index">
        <uni-forms-item :rules="[{'required': true,errorMessage: '请填写选择项描述'}]" :name="['entryList', index, 'text']" label-width="100" :label="item.value+'选择项描述'" required>
          <uni-easyinput v-model="templateData.entryList[index].text" placeholder="请输入选择项描述" />
        </uni-forms-item>
      </template>
      <button type="primary" style="margin-bottom: 10px;" @click="saveHandle">保存</button>
      <button :disabled="isDelete" type="warn" @click="deleteAdd">删除</button>
    </uni-forms>
    <!-- 输入框 -->
    <uni-forms v-if="templateType === 'input'" :rules="rules" :modelValue="templateData" label-position="top" ref="entryBaseForm">
      <uni-forms-item name="TopicDescription" label="标题" required>
        <uni-easyinput type="textarea" v-model="templateData.TopicDescription" placeholder="请输入标题" />
      </uni-forms-item>
      <view class="SelectEntryOption">
        <text class="SelectEntryOption_Required">必填</text>
        <switch @change="switchChange" checked />
      </view>
      <button type="primary" style="margin-bottom: 10px;" @click="saveHandle">保存</button>
    </uni-forms>
  </view>
</template>

<script setup lang="ts">
  import { ref, reactive, getCurrentInstance, onBeforeMount, computed } from 'vue'
  import { onLoad } from "@dcloudio/uni-app";
  const entryBaseForm = ref<HTMLElement | null>(null)
  const instance = getCurrentInstance().proxy
  const eventChannel = instance.getOpenerEventChannel();
  const templateType = ref(0)
  const templateData = reactive({})
  const getQuestionId = () => {
    return (Math.random() + new Date().getTime()).toString(32).slice(0,8)
  }
  // 定义表单校验规则
  const rules:object = reactive({
    TopicDescription: {
      rules: [{
				required: true,
				errorMessage: '请填写标题',
			}]
    }
  })
  const isDelete = computed(() => {
    return Object.values(templateData.entryList).length > 1 ? false : true
  })
  // 更新数据方法
  const updataListHandle = (num = 'A', data = ''):void => {
    templateData.entryList[num] = {
      value: num,
      text: data
    }
  }
  // 添加选择项
  const handleAdd = ():void => {
    if (Object.keys(templateData.entryList).length === 26) return
    const index = String.fromCharCode(65+Object.keys(templateData.entryList).length)
    updataListHandle(index)
  }
  // 删除选择项
  const deleteAdd = (num):void => {
    delete templateData.entryList[String.fromCharCode(65+Object.keys(templateData.entryList).length-1)]
    // delete rules[String.fromCharCode(65+Object.keys(templateData.entryList).length-1)]
  }
  // 保存模板
  const saveHandle = ():void => {
    entryBaseForm.value.validate().then(res => {
      // eventChannel.emit('acceptDataFromOpenedPage', templateData, templateType.value);
      uni.$emit('acceptDataFromOpenedPage', templateData, templateType.value)
      uni.navigateBack()
    })
  }
  // 必选项设置
  const switchChange = (e):void => {
    templateData.isRequired = e.detail.value
  }
  // eventChannel.on('acceptDataFromOpenerPage', (type) => {
  //   console.log('--------------------', type)
  //   templateType.value = type
  //   // 模板容器初始化
  //   templateData['questionId'] = getQuestionId()
  //   templateData['TopicDescription'] = ''
  //   templateData['isRequired'] = true
  //   templateData['entryList'] = {}
  //   if (type !== 'input') {
  //     updataListHandle()
  //   }
  // })
  onLoad((option) => {
    console.log('--------------------', option.type)
    templateType.value = option.type
    // 模板容器初始化
    templateData['questionId'] = getQuestionId()
    templateData['TopicDescription'] = ''
    templateData['isRequired'] = true
    templateData['entryList'] = {}
    if (option.type !== 'input') {
      updataListHandle()
    }
    // eventChannel.emit('acceptDataFromOpenedPage', {data: 'data from test page'});
    // eventChannel.emit('someEvent', {data: 'data from test page for someEvent'});
    // 监听acceptDataFromOpenerPage事件，获取上一页面通过eventChannel传送到当前页面的数据
    // eventChannel.on('acceptDataFromOpenerPage', function(data) {
    //   console.log(data)
    // })
  });
</script>

<style lang="scss" scoped>
.content_button{
  margin-bottom: 50px;
}
.SelectEntryOption{
  margin: 10px 0;
  .SelectEntryOption_Required{
    margin-right: 10px;
  }
}
</style>
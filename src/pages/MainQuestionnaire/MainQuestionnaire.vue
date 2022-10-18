<template>
  <view class="view_box">
    <uni-forms label-position="top" v-if="!isTemplateShow" :rules="rules" :modelValue="TemplateList" ref="baseForm">
      <uni-forms-item name="MainTitle" label="标题" required>
        <uni-easyinput v-model="TemplateList.MainTitle" placeholder="请输入标题" />
      </uni-forms-item>
      <uni-forms-item name="Subheading" label="副标题" required>
        <uni-easyinput type="textarea" v-model="TemplateList.Subheading" placeholder="副标题" />
      </uni-forms-item>
      <button style="margin-bottom: 10px;" type="primary" @click="handleFrom()">添加条目</button>
      <button v-if="isTemplateButtonShow" type="primary" @click="handleFrom(true)">预览</button>
    </uni-forms>
    <PreviewTemplate v-else @backActive="backActive"></PreviewTemplate>
    <uni-popup ref="popup" background-color="#fff" type="bottom">
      <view class="main_question_unipopup">
        <view @click="handlPopupItem('radio')" class="main_question_unipopup_item uni-info">
          <uni-icons custom-prefix="iconfont" color="#8f939c" type="icon-danxuankuang" size="20"></uni-icons>
          <text>单选</text>
        </view>
        <view @click="handlPopupItem('checkbox')" class="main_question_unipopup_item uni-info">
          <uni-icons custom-prefix="iconfont" color="#8f939c" type="icon-duoxuan-" size="20"></uni-icons>
          <text>多选</text>
        </view>
        <view @click="handlPopupItem('input')" class="main_question_unipopup_item uni-info">
          <uni-icons custom-prefix="iconfont" color="#8f939c" type="icon-tianxie" size="20"></uni-icons>
          <text>填写</text>
        </view>
      </view>
    </uni-popup>
  </view>
</template>

<script setup lang="ts">
  import { ref, reactive, onBeforeMount, computed, provide } from 'vue'
  import { onLoad, onUnload } from "@dcloudio/uni-app";
  const popup = ref<HTMLElement | null>(null)
  const baseForm = ref<HTMLElement | null>(null)
  // 定义表单校验规则
  const rules:{} = reactive({
    MainTitle: {
      rules: [{
				required: true,
				errorMessage: '请填写标题',
			}]
    },
    Subheading: {
      rules: [{
				required: true,
				errorMessage: '请填写副标题',
			}]
    }
  })
  const TemplateList = reactive({})// 模板容器
  let entryNum = ref(0)
  provide('TemplateList', TemplateList)
  // 初始化模板数据
  onBeforeMount(() => {
    const id:string = getId()
    TemplateList['TemplateId'] = id
    TemplateList['MainTitle'] = ''
    TemplateList['Subheading'] = ''
    TemplateList['entryData'] = []
    // console.log('onBeforeMount_active', TemplateList)
  })
  // 预览是否显示
  let isTemplateShow = ref(false)
  // 预览按钮是否显示
  let isTemplateButtonShow = ref(false)
  // 获取id
  const getId = ():string => {
    var s = [];
    var hexDigits = "0123456789abcdef";
    for (var i = 0; i < 36; i++) {
        s[i] = hexDigits.substr(Math.floor(Math.random() * 0x10), 1);
    }
    s[14] = "4"; // bits 12-15 of the time_hi_and_version field to 0010
    s[19] = hexDigits.substr((s[19] & 0x3) | 0x8, 1); // bits 6-7 of the clock_seq_hi_and_reserved to 01
    s[8] = s[13] = s[18] = s[23] = "-";
    var uuid = s.join("");
    return uuid;
  }
  // 表单提交
  const handleFrom = (isPreview = false):void => {
    baseForm.value.validate().then(res => {
      TemplateList['MainTitle'] = res.MainTitle
      TemplateList['Subheading'] = res.Subheading
      if (!isPreview) {
        popup.value.open()
      } else {
        isTemplateShow.value = true
      }
    })
  }
  // 跳转页面
  const handlPopupItem = (type):void => {
    uni.navigateTo({
      url: `/pages/Questionnaire/Questionnaire?type=${type}`,
      // events: {
        // 为指定事件添加一个监听器，获取被打开页面传送到当前页面的数据
        // acceptDataFromOpenedPage: function(data, type) {
        //   console.log('过来的数据', data)
        //   addEntryItem()
        //   const newNum = entryNum.value-1
        //   for(const i of TemplateList.entryData) {
        //     if (i.entryNum === newNum) {
        //       i.SelectItemData = data.entryList
        //       i['TopicDescription'] = data.TopicDescription
        //       i['isRequired'] = data.isRequired
        //       i['templateType'] = type
        //       i['questionId'] = data.questionId
        //     }
        //   }
        //   console.log('最后拿到的数据', TemplateList)
        //   isTemplateButtonShow.value = true
        // },
        // someEvent: function(data) {
        //   console.log('22222222222222', data)
        // }
      // },
      // success: function(res) {
      //   console.log('这里是不是次次都触发', type)
      //   // 通过eventChannel向被打开页面传送数据
      //   res.eventChannel.emit('acceptDataFromOpenerPage', type)
      // }
    })
    popup.value.close()
  }
  onLoad((option) => {
    uni.$on('acceptDataFromOpenedPage',function(data, type){
      console.log('过来的数据', data)
      addEntryItem()
      const newNum = entryNum.value-1
      for(const i of TemplateList.entryData) {
        if (i.entryNum === newNum) {
          i.SelectItemData = data.entryList
          i['TopicDescription'] = data.TopicDescription
          i['isRequired'] = data.isRequired
          i['templateType'] = type
          i['questionId'] = data.questionId
        }
      }
      console.log('最后拿到的数据', TemplateList)
      isTemplateButtonShow.value = true
    })
  })
  onUnload(() => {
    uni.$off('acceptDataFromOpenedPage')
  })
  // 添加条目
  const addEntryItem = ():void => {
    if (TemplateList.entryData.length === 0) {
      TemplateList.entryData.push({
        entryNum: entryNum.value,
        SelectItemData: {}
      })
    } else {
      const filterList = TemplateList.entryData.filter(item => {
        return item.entryNum === entryNum.value
      })
      if (filterList.length === 0) {
        TemplateList.entryData.push({
          entryNum: entryNum.value,
          SelectItemData: {}
        })
      }
    }
    entryNum.value++
  }
  // 监听预览回退
  const backActive = (v):void => {
    isTemplateShow.value = false
  }
</script>

<style lang="scss" scoped>
.question_box{
  margin-top: 50px;
  border: 1px solid gray;
}
input{
  border: 1px solid gray;
}
textarea{
  border: 1px solid gray;
}
.main_question_add{
  width: 150px;
  height: 50px;
  border: 1px solid gray;
}
.main_question_unipopup{
  height: 150px;
  border-radius: 5px 5px 0 0;
  display: flex;
  padding: 15px;
  .main_question_unipopup_item{
    display: flex;
    flex-direction: column;
    margin-right: 50px;
  }
  .main_question_unipopup_item:last-child{
    margin-right: 0px;
  }
}
</style>
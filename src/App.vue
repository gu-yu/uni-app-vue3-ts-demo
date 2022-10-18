<script setup lang="ts">
import { onLaunch, onShow, onHide } from "@dcloudio/uni-app";
onLaunch(() => {
  console.log("App Launch");
});
onShow(() => {
  console.log("App Show");
});
onHide(() => {
  console.log("App Hide");
});
</script>
<style lang='scss'>
@import './uni_modules/uni-scss/variables.scss';
@import './common/uni.css';
@import './uni_modules/uni-scss';
@import "./static/iconfont.css";
.uni-h1{
  line-height: 30px !important;
}
.uni-h4{
  line-height: 20px !important;
}
.view_box{
  padding: 15px;
}
.word_break_title{
  word-break: break-word;
}
.uni-forms-item__label{
  height: auto !important;
  word-break: break-word !important;
  align-items: stretch !important;
}
.is-required{
  padding-top: 3px;
  padding-right: 3px;
}
</style>

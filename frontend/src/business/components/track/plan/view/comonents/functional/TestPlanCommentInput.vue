<template>
  <el-form class="comment-form clearfix">
    <el-form-item
    :label="'评论'">
      <ms-mark-down-text
        default-open="edit"
        prop="comment"
        custom-min-height="100"
        :class="{inputWarning: inputWarning}"
        :data="data"
        :toolbars="toolbars"
        ref="md"/>
    </el-form-item>
  </el-form>
</template>

<script>
import MsMarkDownText from "@/business/components/track/case/components/MsMarkDownText";
export default {
  name: "TestPlanCommentInput",
  components: {MsMarkDownText},
  props: {
    data: {
      type: Object,
      default() {
        return {
          comment: ''
        }
      }
    }
  },
  watch: {
    'data.comment'() {
      if (this.data && this.data.comment && this.data.comment.length > 0) {
        this.inputWarning = false;
      }
    },
    'data.status'() {
      if (this.data.status !== 'Failure' && this.inputWarning) {
        this.inputWarning = false;
      }
    }
  },
  data() {
    return {
      toolbars: {
        bold: false, // 粗体
        italic: false, // 斜体
        header: false, // 标题
        underline: false, // 下划线
        strikethrough: false, // 中划线
        mark: false, // 标记
        superscript: false, // 上角标
        subscript: false, // 下角标
        quote: false, // 引用
        ol: false, // 有序列表
        ul: false, // 无序列表
        link: false, // 链接
        imagelink: true, // 图片链接
        code: false, // code
        table: false, // 表格
        fullscreen: false, // 全屏编辑
        readmodel: false, // 沉浸式阅读
        htmlcode: false, // 展示html源码
        help: false, // 帮助
        /* 1.3.5 */
        undo: false, // 上一步
        redo: false, // 下一步
        trash: false, // 清空
        save: false, // 保存（触发events中的save事件）
        /* 1.4.2 */
        navigation: false, // 导航目录
        /* 2.1.8 */
        alignleft: false, // 左对齐
        aligncenter: false, // 居中
        alignright: false, // 右对齐
        /* 2.2.1 */
        subfield: false, // 单双栏模式
        preview: false, // 预览
      },
      inputWarning: false
    };
  },
  methods: {
    inputLight() {
      this.inputWarning = true;
      this.$refs.md.getTextareaDom().focus();
    },
  }
}
</script>

<style scoped>

.comment-form {
  padding-right: 20px;
  padding-left: 30px;
  margin-top: 100px;
}

.inputWarning {
  -webkit-box-shadow: 0 0 8px rgb(205,51,43) !important;
}

</style>

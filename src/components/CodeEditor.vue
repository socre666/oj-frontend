<template>
  {{ langurage }}
  <div
    id="code-editor"
    ref="codeEditorRef"
    style="min-height: 400px; height: 70vh"
  ></div>
  <!--  <a-button @click="fillvalue">填充值</a-button>-->
  <Editor :value="value" :plugins="plugins" @change="handleChange" />
</template>

<script setup lang="ts">
import * as monaco from "monaco-editor";
import { defineProps, onMounted, ref, toRaw, watch, withDefaults } from "vue";
import { languages } from "monaco-editor";

/**
 * 定义组件属性的类型
 */
interface Props {
  value: string;
  langurage?: string;
  handleChange: (v: string) => void;
}

/**
 * 给组件定义初始值
 */
const props = withDefaults(defineProps<Props>(), {
  value: () => "",
  langurage: () => "java",
  handleChange: (v: string) => {
    console.log(v);
  },
});
const codeEditorRef = ref();
const codeEditor = ref();

// const fillvalue = () => {
//   if (!codeEditor.value) {
//     return;
//   }
//   //改变值
//   toRaw(codeEditor.value).setValue("新的值");
// };
watch(
  () => props.langurage,
  () => {
    // Hover on each property to see its docs!
    codeEditor.value = monaco.editor.create(codeEditorRef.value, {
      value: props.value,
      language: props.langurage,
      automaticLayout: true,
      minimap: {
        enabled: true,
      },
      // lineNumbers: "off",
      // roundedSelection: false,
      // scrollBeyondLastLine: false,
      readOnly: false,
      theme: "vs-dark",
    });
  }
);
onMounted(() => {
  if (!codeEditorRef.value) {
    return;
  }
  // Hover on each property to see its docs!
  codeEditor.value = monaco.editor.create(codeEditorRef.value, {
    value: props.value,
    language: props.langurage,
    automaticLayout: true,
    minimap: {
      enabled: true,
    },
    // lineNumbers: "off",
    // roundedSelection: false,
    // scrollBeyondLastLine: false,
    readOnly: false,
    theme: "vs-dark",
  });
  // 编辑 监听内容变化
  codeEditor.value.onDidChangeModelContent(() => {
    props.handleChange(toRaw(codeEditor.value).getValue());
  });
});
</script>
<style scoped></style>

<template>
  <div id="exampleView">
    <CodeEditor :value="codeValue" :handle-change="onCodeChang" />
    <MdEditor :value="Mdvalue" :handle-change="onMdChang" />
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import MdEditor from "@/components/MdEditor.vue";
import CodeEditor from "@/components/CodeEditor.vue"; // @ is an alias to /src
const codeValue = ref();
const Mdvalue = ref();
const onCodeChang = (v: string) => {
  codeValue.value = v;
};
const onMdChang = (v: string) => {
  Mdvalue.value = v;
};
</script>

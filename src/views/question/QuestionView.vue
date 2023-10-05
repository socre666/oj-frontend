<template>
  <div id="questionView"></div>
  <a-form :model="searchParms" layout="inline">
    <a-form-item field="title" label="名称" style="min-width: 240px">
      <a-input v-model="searchParms.title" placeholder="请输入名称" />
    </a-form-item>
    <a-form-item field="tags" label="标签" style="min-width: 240px">
      <a-input-tag v-model="searchParms.tags" placeholder="请输入标签" />
    </a-form-item>
    <a-form-item>
      <a-button type="primary" @click="doSubmit">搜索</a-button>
    </a-form-item>
  </a-form>
  <a-divider size="0" />
  <a-table
    :columns="columns"
    :data="dataList"
    :pagination="{
      showTotal: true,
      pageSize: searchParms.pageSize,
      current: searchParms.current,
      total,
    }"
    @page-change="onPageChange"
  >
    <template #tags="{ record }">
      <a-space wrap>
        <a-tag v-for="(tag, index) of record.tags" :key="index" color="green"
          >{{ tag }}
        </a-tag>
      </a-space>
    </template>
    <template #acceptedRate="{ record }"
      >{{
        `${record.submitNum ? record.accpeyNum / record.submitNum : "0"}% (${
          record.acceptedNum
        }/${record.submitNum})`
      }}
    </template>
    <template #createTime="{ record }"
      >{{ moment(record.createTime).format("YYYY-MM-DD") }}
    </template>
    <template #optional="{ record }">
      <a-space>
        <a-button type="primary" @click="toQuestionPage(record)">做题</a-button>
      </a-space>
    </template>
  </a-table>
</template>

<script setup lang="ts">
import { onMounted, ref, watchEffect } from "vue";
import {
  Question,
  QuestionControllerService,
  QuestionQueryRequest,
} from "../../../generated";
import message from "@arco-design/web-vue/es/message";
import { useRouter } from "vue-router";
import moment from "moment";

const show = ref(true);
const dataList = ref([]);
const total = ref(0);
const searchParms = ref<QuestionQueryRequest>({
  title: "",
  tags: [],
  pageSize: 10,
  //current是当前页号
  current: 1,
});

const loDada = async () => {
  const res = await QuestionControllerService.listQuestionVoByPageUsingPost(
    searchParms.value
  );
  if (res.code === 0) {
    dataList.value = res.data.records;
    total.value = res.data.total;
  } else {
    message.error("加载失败" + res.message);
  }
};
/**
 * 监听 searchParams 变量，改变时触发页面的重新加载
 */
watchEffect(() => {
  loDada();
});
/**
 * 页面加载时请求数据
 * {id: "1", title: "A+B", content: "题目内容", tags: "["栈","简单"]", answer: "暴力破解", submitNum: 0,…}
 */
onMounted(() => {
  loDada();
});
const columns = [
  {
    title: "题号",
    dataIndex: "id",
  },
  {
    title: "题目名称",
    dataIndex: "title",
  },
  {
    title: "标签",
    slotName: "tags",
  },
  {
    title: "通过率",
    slotName: "acceptedRate",
  },
  {
    title: "创建时间",
    slotName: "createTime",
  },
  {
    slotName: "optional",
  },
];

const onPageChange = (page: number) => {
  searchParms.value = {
    ...searchParms.value,
    current: page,
  };
};
const router = useRouter();
/**
 * 跳转到做题页面
 * @param question
 */
const toQuestionPage = async (question: Question) => {
  router.push({
    path: `/view/question/${question.id}`,
  });
};
/**
 * 确认搜索，重新加载数据
 */
const doSubmit = () => {
  //这里需要重新重置搜索页号
  searchParms.value = {
    ...searchParms.value,
    current: 1,
  };
};
</script>
<style scoped>
#questionView {
  max-width: 1280px;
  margin: 0 auto;
}
</style>

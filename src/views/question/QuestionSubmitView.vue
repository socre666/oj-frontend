<template>
  <div id="questionSubmitView"></div>
  <a-form :model="searchParms" layout="inline">
    <a-form-item field="questionId" label="题号" style="min-width: 240px">
      <a-input v-model="searchParms.questionId" placeholder="请输入名称" />
    </a-form-item>
    <a-form-item field="title" label="编程语言" style="min-width: 240px">
      <a-select
        v-model="searchParms.language"
        :style="{ width: '320px' }"
        placeholder="选择编程语言"
      >
        <a-option>java</a-option>
        <a-option>python</a-option>
        <a-option>cpp</a-option>
        <a-option>go</a-option>
      </a-select>
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
    <template #judgeInfo="{ record }"
      >{{ JSON.stringify(record.judgeInfo) }}
    </template>
    <template #createTime="{ record }"
      >{{ moment(record.createTime).format("YYYY-MM-DD") }}
    </template>
  </a-table>
</template>

<script setup lang="ts">
import { onMounted, ref, watchEffect } from "vue";
import {
  Question,
  QuestionControllerService,
  QuestionSubmitQueryRequest,
} from "../../../generated";
import message from "@arco-design/web-vue/es/message";
import { useRouter } from "vue-router";
import moment from "moment";

const show = ref(true);
const dataList = ref([]);
const total = ref(0);
const searchParms = ref<QuestionSubmitQueryRequest>({
  questionId: undefined,
  language: undefined,
  tags: [],
  pageSize: 10,
  //current是当前页号
  current: 1,
});

const loDada = async () => {
  const res = await QuestionControllerService.listQuestionSubmitByPageUsingPost(
    {
      ...searchParms.value,
      sortField: "createTime",
      sortOrder: "descend",
    }
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
    title: "提交号",
    dataIndex: "id",
  },
  {
    title: "编程语言",
    dataIndex: "language",
  },
  {
    title: "判题信息",
    slotName: "judgeInfo",
  },
  {
    title: "判题状态",
    dataIndex: "status",
  },
  {
    title: "题目ID",
    dataIndex: "questionId",
  },
  {
    title: "提交者ID",
    dataIndex: "userId",
  },
  {
    title: "创建时间",
    slotName: "createTime",
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
#questionSubmitView {
  max-width: 1280px;
  margin: 0 auto;
}
</style>
